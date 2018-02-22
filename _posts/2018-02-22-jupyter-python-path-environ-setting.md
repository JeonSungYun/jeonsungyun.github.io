---
layout: post
title: "Jupyter Notebook 에서 커널 로딩 전 환경 변수 설정"
comments: true
description: "ipython profile 에 InteractiveShellApp.exec_lines 활용하기"
keywords: "jupyter notebook, PYTHON_PATH"
---

Jupyter Notebook 서버를 실행하는 PC 에서 다음 명령을 수행.
> ```$ ipython profile create```

ipython 설정 디렉터리(e.g. ```~/.ipython/```)에 ```profile_default/ipython_kernel_config.py``` 가 생성됨

텍스트 에디터로 그 파일을 열어서, ```c.InteractiveShellApp.exec_lines``` 를 검색.

``` python
c.InteractiveShellApp.exec_lines=[
    'import sys; sys.path.append(\"path/to/add\")'
]