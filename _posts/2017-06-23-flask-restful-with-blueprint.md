---
layout: post
title: "Flask RESTful 라이브러리와 Blueprint"
comments: true
description: "Flask 의 Blueprint 를 Flask-RESTful 라이브러리에 어떻게 적용했는지 사례"
keywords: "flask restful, blueprint"
---

사용할 코드들은 [KakaoTalk-Bot-Boilerplate](https://github.com/Hongseokzip/KakaoTalk-Bot-Boilerplate) 에 있다. 이것은 KakaoTalk 자동응답 API 를 Flask, Flask-RESTful 을 이용하여 만든 예제이다.

Flask 에서 Blueprint 를 왜 사용하는 지에 대한 직관적인 설명은 이 [LINK](http://exploreflask.com/en/latest/blueprints.html) 를 참고한다.

[Flask-RESTful](https://flask-restful.readthedocs.io) 라이브러리는 Flask 를 활용하여 RESTful API 서버를 만들기 쉽게 해준다.

Flask-RESTful 라이브러리에서 Blueprint 를 사용하는 것에 대한 설명은(라이브러리 0.3.5 버전 기준) [LINK](https://flask-restful.readthedocs.io/en/0.3.5/intermediate-usage.html#use-with-blueprints) 를 참고한다.

KakaoTalk-Bot-Boilerplate 에서는 KakaoTalk 자동 응답을 담당하는 모듈을 Blueprint 로 구현했다.

그 모듈은 Flask 어플리케이션에 다음과 같이 등록된다.
``` python
# app.py
# -*- coding: utf-8 -*-

from flask import Flask

from kakao_bot import kakao_bot

app = Flask(__name__)
app.register_blueprint(kakao_bot, url_prefix="/iface")  # if url_prefix, set url_prefix parameter

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0', port=8888)
```

url_prefix 파라미터로 app 의 루트로부터 이 모듈의 url prefix 를 정해줄 수 있다. 

그리고 kakao_bot 이라는 Blueprint 는 kakao_bot 패키지 아래의 \_\_init\_\_에서 생성한다.
``` python
# kakao_bot/__init__.py
# -*- coding: utf-8 -*-

import logging

from flask import Blueprint
from flask_restful import Api

from chat_room import Chatroom
from friend import Friend
from keyboard import Keyboard
from message import Message

kakao_bot = Blueprint('kakao_bot', __name__)
api = Api(kakao_bot)

# resource map
api.add_resource(Chatroom, '/chat_room/<user_key>')
api.add_resource(Friend, '/friend', '/friend/<user_key>')
api.add_resource(Keyboard, '/keyboard')
api.add_resource(Message, '/message')


@kakao_bot.errorhandler(500)
def server_error(e):
    logging.exception('An error occurred during a request. %s' % str(e))
    return 'An internal error occurred.', 500

```

여기서 개인적으로 Blueprint 의 첫번째 인자, 두번째 인자에 대해서 이해가 잘 안갔는데,
[LINK](http://flask.pocoo.org/docs/0.12/blueprints/#blueprints) 에 따르면,
첫번째 인자는 Blueprint 의 이름(kakao_bot = Blueprint.. 니깐 'kakao_bot')을 입력,
또한, [LINK](http://flask.pocoo.org/docs/0.12/blueprints/#blueprint-resource-folder) 에 따르면,
두번째 인자는 \_\_name\_\_ 을 입력하자.

resource map 이라고 주석을 단 부분에서는 kakao_bot 패키지에서 선언한 Resource 들을 불러와 api 로 등록한다.

> 요약하자면, app.py 에서는 kakao_bot 패키지의 kakao_bot 이라는 
> Blueprint 만 import 하여 등록함으로써, KakaoTalk 자동응답 API 모듈을 사용할 수 있게 되는 것이다.