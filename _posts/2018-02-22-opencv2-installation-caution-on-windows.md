---
layout: post
title: "Windows 7, 10 (64bit)에서 OpenCV2 설치 시 추가작업 시도"
comments: true
description: "OpenCV2 x86 dll 들을 Wow64 로 복사해준다"
keywords: "opencv2 installation, windows"
---

# 설치 환경
OS : Windows 10 (64bit)
OpenCV : 2.4.13.5
OpenCV Path : C:\opencv-2.4.13.5
IDE : Visual Studio 2017

# 설치 과정
0. x86 프로젝트로 가정.
1. Visual Studio 에서 C++ 프로젝트 생성하고 프로젝트 속성 편집
2. 모든구성
  - C/C++
    - 추가 포함 디렉터리에 {OpenCV Path}\build\include 추가
  - 링커
    - 추가 라이브러리 디렉터리에 {OpenCV Path}\build\x86\vc14\lib 추가
3. 디버깅 구성
  - 링커
    - 추가 종속성에 ```opencv_core2413d.lib;opencv_contrib2413d.lib;opencv_imgproc2413d.lib;opencv_highgui2413d.lib``` 추가
4. 릴리즈 구성
  - 링커
    - 추가 종속성에 ```opencv_core2413.lib;opencv_contrib2413.lib;opencv_imgproc2413.lib;opencv_highgui2413.lib``` 추가
5. (주의할 점1) 1-4 과정을 마치고 안되어 해본 작업
  - 64 bit 에서 x86 프로젝트이기 때문에, x86 dll 파일 바이너리들(```C:\opencv-2.4.13.5\build\x86\vc14\bin```)을 ```C:\Windows\SysWOW64``` 로 복사