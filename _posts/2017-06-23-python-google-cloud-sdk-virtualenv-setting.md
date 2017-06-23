---
layout: post
title: "VirtualEnv 에 Google Cloud SDK 설정하기"
comments: true
description: "Google Cloud SDK for Python 을 VirtualEnv 에 종속시키기"
keywords: "flask restful, blueprint"
---

사용할 코드들은 [KakaoTalk-Bot-Boilerplate](https://github.com/Hongseokzip/KakaoTalk-Bot-Boilerplate) 에 있다. 이것은 KakaoTalk 자동응답 API 를 Flask, Flask-RESTful 을 이용하여 만든 예제이다. 또한, 이 코드를 활용하여 운영 중인 서버는 Google App Engine (이하 GAE) 을 쓰고 있어 Google Cloud SDK 관련 설정 파일들도 Git 에 업로드 되어있다.

위 Git 을 Clone 하면 .gitignore 에 `venv` 가 포함되어있기 때문에, 필자의 VirtualEnv 설정을 사용할 수 없다. Virtual 설정을 어떻게 했는지 설명한다.

OS 는 macOS 를 사용했지만, 다른 OS 도 설정이 크게 달라지는건 없다.

먼저, 필자는 Python 2.7 과 Python 3.6 이 둘다 설치되어있어서, VirtualEnv 에서 사용할 Python 바이너리를 명시적으로 선택해주었다. GAE 를 Python 2.7 로 설정해서 VirtualEnv 도 Python 2.7 로 설정했다.

먼저, 프로젝트 루트 디렉터리로 이동하고 다음 명령을 수행한다.
``` sh
$ virtualenv -p /usr/bin/python venv
$ source venv/bin/activate
```

다음은 프로젝트 환경 설정이다.
``` sh
(venv)$ pip install -r requirements.txt
```

이제 Google Cloud SDK 를 설치한다.
``` sh
(venv)$ curl https://sdk.cloud.google.com | bash
```

> 실행중에 google-cloud-sdk 가 설치될 경로를 설정하라고 한다.
> 그냥 Enter를 쳐서 넘어가 시스템에 설치하거나, 필자처럼 `venv` 를 입력하여 venv 에 설치해도 된다.

> PATH 를 수정할지 여부를 묻는다. `Y` 로 선택하면, 
> 수정할 rc file 의 경로를 입력하라고 한다.
> 이때, `venv/bin/activate` 라고 입력한다.

Google Cloud SDK 의 설치가 완료 되었다면, 현재 프로젝트를 Google Cloud SDK 와 연동한다.
``` sh
(venv)$ gcloud init
```

> 이제부터는 사용자 개인설정이다.
