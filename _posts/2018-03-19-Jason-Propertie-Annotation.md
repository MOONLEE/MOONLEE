---
layout: post
title:  "@JsonProperty Annotion사용을 통한 응답 데이터 분류"
date:   2018-03-19 11:24:21 +0900
categories: blog
tags: [blog, java, jpa]
comments: true
---

## Jackson Json 라이브러리를 사용하여 파싱시 불필요한 항목 무시하게 만들기
JSON 변환 라이브러리를 사용하다보면 보내고 싶지 않은 데이터도 Jackson 라이브러리에서 파싱을 해서 보냅니다.
이러한 상황에서 필요한 Annotation이 *`@JsonProperty`* 입니다.


## Jackson Json 라이브러리 사용 방법 및 속성

```
public class Category extends CalendarEntity {

	@Id
	@GeneratedValue(strategy = GenerationType.AUTO)
	private Long id;
	private String name;
	private Long categoryOrder;

	@JsonProperty(access = Access.WRITE_ONLY)
	@JsonManagedReference
	@OneToMany(mappedBy = "category", cascade = { CascadeType.ALL })
	@LazyCollection(LazyCollectionOption.TRUE)
	private List<Post> posts;
	
```
위와 같이 JSON 변환시 무시하려는 변수에 어노테이션을 추가해주면 사용된다.
아래는 속성별 기능이다.


| accsess 속성 | 설명                             | 
| ----------- | -------------------------------------------- | 
| AUTO | 자동으로 결정 (다른 어노테이션을 참조) |
| READ_ONLY | JSON으로 파싱시(직렬화)에만 해당 변수를 변환   | 
| WRITE_ONLY | JSON에서 Domain으로 파싱시에(역직렬화)만 해당 변수를 변환 | 
| READ_WRITE | 직렬화와 역직렬화 둘다 해당 변수를 변환 | 


## @JsonProperty는 파싱 json 변수 명을 맵핑할 수 있다.


```
public class Category extends CalendarEntity {

	@Id
	@GeneratedValue(strategy = GenerationType.AUTO)
	private Long id;
	private String name;
    @JsonProperty("test")
	private Long categoryOrder;

	@JsonManagedReference
	@OneToMany(mappedBy = "category", cascade = { CascadeType.ALL })
	@LazyCollection(LazyCollectionOption.TRUE)
	private List<Post> posts;
	
```
위와 같이 `@JsonProperty("test")` 사용시 JSON 매핑 변수를 *`categoryOrder`*에서 *`test`*로 맵핑을 하여 직렬화 및 역직렬화를 사용한다.

```
[
    {
        "createdAt": 1520734340000,
        "updatedAt": 1521185783000,
        "id": 3,
        "name": "글쓰기",
        "test": 1
    },
    {
        "createdAt": 1520735324000,
        "updatedAt": 1521185783000,
        "id": 5,
        "name": "JPA",
        "test": 2
    },
    {
        "createdAt": 1520736442000,
        "updatedAt": 1521185783000,
        "id": 6,
        "name": "React",
        "test": 3
    }
]
```


참고 
1. [Java Json library jackson 사용법][Java Json library jackson 사용법]
2. [@JsonProperty 를 이용한 접근 제어 - 응답값에 포함하지 않기][@JsonProperty 를 이용한 접근 제어 - 응답값에 포함하지 않기]


* 좀 더 좋은 포스팅을 위해 잘못된 부분이 있다는 것을 알려주시면 정말 좋아요!!

[Java Json library jackson 사용법]: https://www.lesstif.com/pages/viewpage.action?pageId=24445183
[@JsonProperty 를 이용한 접근 제어 - 응답값에 포함하지 않기]: http://eglowc.tistory.com/28
