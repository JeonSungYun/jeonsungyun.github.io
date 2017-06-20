---
layout: post
title: "windows (venv) pip3 install 할 때, UnicodeDecodeError 'utf-8' 에러 수정하기"
description: "windows (venv) pip3 install 할 때, UnicodeDecodeError 'utf-8' 에러 수정하기"
comments: true
keywords: "windows, venv, pip3, unicodedecorderror, utf8"
---

 windows 에서 venv 로 pip install 할 때, UnicodeDecodeError 라고 뜬 에러를 보았다.
에러 내용은 'utf-8' 코덱으로 이해할 수 없는 바이트를 만났다는 것이다.

 python -c 'import sys;sys.getdefaultencoding()' 하면 'utf-8' 로 뜬다. 당황해서 차근차근히 에러를 잡다보니, 다음과 같이 해결했다.

>venv\Lib\site-packages\pip\compat\__init__.py 의 75번째 줄을 
return s.decode('utf_8') 에서 return s.decode('cp949') 로 바꾼다.

이유는 s 에 해당하는 문자열들을 print 해서 찍어보니, 모듈 컴파일하다가 Visual Studio C/C++ 측의 에러 텍스트를 CP949 인코딩으로 넘겨주는 경우를 보았고, 이것을 해석 못해서 에러나는 것이었다.
