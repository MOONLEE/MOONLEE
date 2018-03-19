---
layout: post
title:  "리액트 Container Component Pattern"
date:   2018-03-15 11:24:21 +0900
categories: blog
tags: [blog, react]
comments: true
---

# Container와 Component의 구분을 어떻게 해야지?
React를 접하게 되면서 React 파일 구조를 보면 "Container" 디렉토리와 "Component" 디렉토리로 나누어 리액트를 구성하게 됩니다.
처음 이 구성을 접사면서 들었던 의문은 왜 Container와 Component의 기준이 무엇이가였습니다.


# Component란?
컴포넌트는 리액트에서 재사용 가능하여 분리될수 있는 기능이라고 생각될 수 있습니다. 그리고 Data를 Props로 전달 받아서 Html로 표현될 수 있어야 합니다.
따라서 Component는 인터페이스가 정의 되어야 하며 리액트에서 ProtoType을 사용하는 방법으로 표현될 수 있습니다.


# 그렇다면 Container는 어떤 기능을 해야할까?
Container는 그릇이라는 뜻을 가지고 있습니다. React에서 Component를 모아놓을 그릇이라고 생각할 수 있습니다. 
여기서 추가적으로 한가지 기능을 더해주어야 하는데, Component에 전달 할 데이터를 수집해야하는 기능이 추가해주어야 합니다. 
Request를 Container에서 해주어야 합니다.

이상 React의 Container와 Component를 분리를 위한 개념 정리였습니다.


참고 : [learnreact][learnreact]의 [Container Components 포스팅][Container-Components]


* 좀 더 좋은 포스팅을 위해 잘못된 부분이 있다는 것을 알려주시면 정말 좋아요!!

[learnreact]: https://medium.com/@learnreact
[Container-Components]: https://medium.com/@learnreact/container-components-c0e67432e005
