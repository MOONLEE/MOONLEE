---
layout: post
title:  "Reactive Programming!!"
date:   2018-05-01 11:24:21 +0900
categories: blog
tags: [blog, Java]
comments: true
---

## 이번 포스팅은 RX 기반 node의 기술적 특성을 이해를 해보고자 작성을 했습니다.

## Reactive Programming / node?
리액티브 프로그래밍의 대표적인 기술로 node가 있습니다. 
노드는 functional 프로그래밍 언어로 싱글 Thread가 특징인 기술입니다.
여기서, 싱글 Thread라는 것은 Process를 실행하면 Thread 하나만 존재한다는 것이 되겠죠.


## Thread?
Thread를 번역하면 실이라는 뜻을 가집니다.
컴퓨터 Thread 프로그래밍은 병렬 처리를 위해 고안된 기술입니다.
Thread이전에 사용되었던 방식으로 fork를 통한 병렬 처리 방식이 있습니다.
Thread가 fork를 대체했었던 장점은 바로 빠르다는 것입니다. 
여기서 빠르게 만들었던 방법은 stack 메모리 구조에서 함수 메모리 영역을 제외한
나머지 메모리 구조 부분을 공유하여 overhead를 줄여 
context switching cost를 줄여 성능을 개선한 것입니다.


## Reactive vs Thread
그렇다면, Reactive프로그래밍이 Thread프로그래밍보다 개선한 부분은
Thread를 생성하면서 발생하는 cpu의 context switching cost를 줄이고자 하는 것입니다.
이를 통해서 많이 생성된 thread의 context switching을 위한 
cpu의 interrupt를 줄여 overhead를 줄이기 때문에 cpu 활용도를 최대화 시키는 것입니다.
위의 성능 부분을 개선시키기 위해서 기존 Thread 생성하고 함수 실행시 io함수 실행으로 
context가 block상태인 경우의 유휴 지점을 활용하자는 부분입니다.
프로그래밍 언어에서는 비동기 함수 실행 방식을 지원해야하며
해당 함수가 실행이 끝났는지를 지속적으로 체크하는 Loop가 필요할 것입니다.


## Reactive 프로그래밍의 단점
Reactive프로그래밍에서 단점으로는 비동기 함수 사용으로
결과 처리를 위한 callback 영역을 정의를 해야 합니다.
이러한 의미는 기존 함수 절차적으로 프로그래밍을 수행하는 것이 아닌
흐름이 진행되다 callback영역이 다시 실행된다는 부분이 있어
프로그래밍 복잡도가 증가한다는 것입니다.
또한 callback안의 callback이 발생하게 되면
이러한 문제는 더 크게 작용할 것입니다.


## Web Programming에서 Reactive 프로그래밍이 적용
Reactive프로그래밍이 적용 가능한 이유는 
웹프로그래밍에서 업무 처리 영역과 저장 영역이 다른 서버에 이루어지며
이를 위해서 cpu사용보다 io사용이 많이 발생하기 때문일 것입니다.


## cpu사용이 io보다 많은 경우
cpu사용을 많이 필요한 업무 처리에서는 Rective 프로그래밍은 적용이 어려울 수 있습니다.
예를 들어 오랜시간이 드는 계산업무 여러개를 사용한다면 멀티코어 cpu를 최대한 활용할 수 있는
병렬 프로그래밍을 사용해야 할것입니다. 이럴때는 Thread를 코어 수 만큼 증가 시켜 동시가 실행하거나
병렬 처리를 지원하는 프로그래밍 언어를 써야 효과적일 것입니다.


## 마지막으로
위의 내용은 RX 선언 내용을 명확히 이해가 되지 않아 node를 통한 기술적 특성을 가지고 
리액티브 프로그래밍으로 얻을 수 있는 것을 생각해본 내용입니다.
맞지 않는 내용이 있겠지만 적은 이유는 RX를 좀더 이해해보자는 의도로 이 글은 작성되었습니다.




* 좀 더 좋은 포스팅을 위해 잘못된 부분이 있다는 것을 알려주시면 정말 좋아요!!