---
layout: post
title: "react native 에서 파일명으로 index.js 을 쓰면 안된다"
comments: true
description: "react native 에서 파일명으로 index.js 을 쓰면 안된다"
keywords: "react native, index.js"
---

index.android.js 와 index.ios.js 의 공통코드를 index.js 로 빼고 싶은 강한 욕구가 있는데,.

그러면, Element type is invalid expected a string (for built-in components) or a class/function ... 에러가 뜬다.

아래 코드는 index.ios.js 이고, 공통 코드는 index.js 로 빼둔 상태이다.

``` javascript
import member from './index';
import {AppRegistry} from 'react-native';
 
console.log(typeof member);
console.log(member.type)

AppRegistry.registerComponent('member', () => member);
```

에러를 차근차근 해결하기 위해, 

http://localhost:8081/debugger-ui 에서 크롬 개발자도구로, 위 코드의 console.log 로 찍은 로그들을 살펴보니,

member 는 그냥 빈 Object 객체로 넘어왔으니, import 문이 그냥 제대로 실행되지 않았다는 것이다.


해결한 방법은 index.js 를 common.js 로 이름을 바꾸었다. app 디렉터리를 만들어, app/index.js 로 해도 괜찮은 듯하다.