---
layout: post
title: "realm js 에서 Results.addListener"
comments: true
description: "realm js 에서 Results.addListener"
keywords: "realm js, results.addlistener"
---

Collection notifications 기능에서 당연히 콜백에서 변경된 내용이 넘어어고, 콜백이 호출된 시점에 받아보는 내용은 변경이 완료된 내용인줄 알았다. 

그런데, 처음 호출되는 콜백에 비어있는 내용이 오고, 그 이후부터 변경 내용이 제대로 오는 문제가 있었다.

지금 시점에 아직 고쳐지지 않았으나, 이걸 고쳐야하는지 말아야하는지에 대한 의견이 분분..하다? 


https://github.com/realm/realm-js/issues/927
https://github.com/realm/realm-js/issues/950