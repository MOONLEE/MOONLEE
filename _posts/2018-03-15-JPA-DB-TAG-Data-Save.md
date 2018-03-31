---
layout: post
title:  "JPA 준영속성과 TAG 데이터 DB 저장 문제"
date:   2018-03-30 11:24:21 +0900
categories: blog
tags: [blog, JPA]
comments: true
---


## 이번 블로그 개선 사항에서 전체 Tag별 Count 추가 Table 생성
이번에 블로그 개선사항에서 Tag태이블이 추가 되었고
각 Tag별 Count를 할 수 있도록 컬럼이 구성되었습니다.


## @Transaction 속하는 있는 스프링 데이터 메서드는 준영속성을 갖는다
```
public void savePostHashTag(List<HashTagForm> tagForm, Long postId) {
    List<HashTagEntry> entryList = new ArrayList<HashTagEntry>();
    
    for (HashTagForm form : tagForm) {
        HashTag tag = hashTagRepository.findByName(form.getText());
        if (ObjectUtils.isEmpty(tag)) {
            tag = hashTagRepository.save(new HashTag(form.getText()));
        } else {
            tag.addCount();
        }
        
        HashTagEntry hashTagEntry = new HashTagEntry(form, HashTagEntry.HashTagableType.post ,postId);
        hashTagEntry.setHashTag(tag);
        entryList.add(hashTagEntry);
        
    }

    hashTagEntryRepository.save(entryList);
}
```
기존 위의 코드를 보면 "HashTag Entity"를 조회 하고 나서 addCount를 하여 tag별 count를 증가 시킵니다.
그런데 건수를 저장하는 메소드 사용이 안보입니다. 영속성 Entity로 생각하고 작성된 로직입니다.

하지만 테이블 데이터를 조회하면
아래와 같이 Count값이 올라가지 않습니다.

| name | count| 
| ---- | ---- | 
|  spring    |   0   |
|  java  |   0   |


이유는 아래와 같이 상위 Controller로직에서 *`@Transaction`* 을 설정해놓았기 때문입니다.

```
@Transactional
	public PostDto savePost( @RequestBody PostForm form
			, Principal principal) {
		User user = null;
            ...
			post.setCategory(category);
			post = postService.save(post);
            ...
	}
}
```



## 아래와 같이 Tag별 Count 추가시 로직을 하는 경우 생기는 문제점
이러한 테그 생성시 Count건수를 실시간으로 처리하려면 문제가 하나있습니다.

```
public void savePostHashTag(List<HashTagForm> tagForm, Long postId) {
    List<HashTagEntry> entryList = new ArrayList<HashTagEntry>();
    
    for (HashTagForm form : tagForm) {
        HashTag tag = hashTagRepository.findByName(form.getText());
        if (ObjectUtils.isEmpty(tag)) {
            tag = hashTagRepository.save(new HashTag(form.getText()));
        } else {
            tag.addCount();
        }
        
        HashTagEntry hashTagEntry = new HashTagEntry(form, HashTagEntry.HashTagableType.post ,postId);
        hashTagEntry.setHashTag(tag);
        entryList.add(hashTagEntry);
        
    }

    hashTagEntryRepository.save(entryList);
}
```

헤시태그의 카운트를 저장 하는 동안의 *`hashTagRepository.save(tag);`* tag 데이터는 준영속성을 갖는 Entity로 구성이 되어있습니다.
이 의미는 동시에 수많은 사용자들이 글을 저장할 때, 헤시태그의 중복이 생기면 해당 해시태그의 count가 정확하지 않게 저장되는 가능성을 가지고 있습니다.
물론 현재 CODE404 블로그는 사용자가 4명이기 때문이 이러한 가능성이 매우 낮으며, 엄청나게 중요한 문제도 아닙니다.

그래도 이러한 문제를 어떻게 이를 처리해야 할까요?

## savePostHashTag를 Syncronized (동기화) 하기
HashTagService의 저장 메서드를 동기화 하는 방법으로 문제를 해결 할 수 있습니다.
하지만 문제는 Was 컨테이너가 증가하게 되면 똑같은 문제가 다시 발생합니다.

## DB update 쿼리로 데이터를 저장하기

```
UPDATE  hashtag
SET     count = count + 1
WHERE   name = :name
```

위의 쿼리를 사용하여 DB Level 에서 건수를 업데이트를 하는 방법입니다.
DB는 하나의 인스턴스만이 존재하기 때문에 Was 컨테이너가 여러가 늘어나도 문제가 되지 않습니다.
이 경우에는 JPA의 namedQuery를 사용해서 쿼리를 update해야 합니다.

## 너무 많은 사용자가 몰릴경우!
너무 많은 사용자가 몰린다면 DB를 사용한 위의 방법 또한 문제가 될수 있습니다.
데이터를 update하기 위해서는 락이 생성 될성됩니다.
키워드 중복이 심하다면, 해당 로우의 락이 많이 걸릴것이기 때문입니다.
만일 테이블이 Row단위의 락을 지원하지 않는다면 테이블 단위로 락이 걸리게 되는데
이는 서비스에 심각한 문제를 발생시킵니다.
이러한 경우에는 Batch를 통한 데이터 처리를 하는 수 밖에는 없습니다.
하지만, 실제로는 이러한 경우는 많지 않을 것입니다.!


* 좀 더 좋은 포스팅을 위해 잘못된 부분이 있다는 것을 알려주시면 정말 좋아요!!