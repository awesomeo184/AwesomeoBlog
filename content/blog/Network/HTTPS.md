---
title: '[Network] HTTPS란?'
date: 2021-05-09 19:44:63
category: 'Network'
thumbnail: { thumbnailSrc }
draft: true
---

HTTP의 단점은 클라이언트와 서버 간에 이동하는 데이터가 그대로 노출된다는 점이다.
HTTPS(Hyper Text Transfer Protocol Secure)는 이름 그대로 HTTP에 보안이 추가된 프로토콜이다.
443 포트를 사용한다.

여기서 보안이란 어떤 측면을 얘기하나면

1. 나와 웹 서버 사이에 오가는 정보를 제 3자가 볼 수 없다.
2. 내가 정보를 보내는 웹 서버가 신뢰할 수 있는 서버임을 확신할 수 있다.
3. 내가 보낸 정보가 중간에 변질되지 않는다.

HTTPS는 SSL 프로토콜 위에서 동작하는 프로토콜이다. SSL의 핵심은 보안이다.
SSL 프로토콜은 공개키와 대칭키를 함께 사용하여 위에 언급한 세 가지 보안을 충족한다.

## Handshake

클라이언트가 서버에 접속한다. 이를 Client Hello라고 한다. 이 단계에서 전달되는 정보는 다음과 같다.

1. 클라이언트 측에서 생성한 랜덤 데이터
2. 클라이언트가 지원하는 암호화 방식들
3. 세션 아이디

서버는 Client Hello에 대한 응답으로 Server Hello를 한다. 이 단계에서 전달되는 정보는 다음과 같다.

1. 서버 측에서 생성한 랜덤데이터
2. 서버가 선택한 암호화 방식
3. 인증서

클라이언트는 받은 인증서가 CA에서 발급된 것인지를 확인한다. 인증서는 CA의 개인키로 암호화되어 있기 때문에 CA의 공개키로 복호화할 수 있다.
CA 공개키는 클라이언트 내부에 저장되어 있다. 클라이언트에 내장된 CA 공개키로 해당 인증서를 복호화한다. 복호화에 성공했다면 상대 서버가 신뢰할 수 있는 곳임을 확신할 수 있다.

클라이언트는 위에서 생성한 두 개의 랜덤 데이터를 이용해서 pre master secret를 만들어낸다.
이 pre master secret를 인증서에 들어있는 서버의 공개키로 암호화해서 서버에 전달한다.

서버는 개인키로 pre master secret를 복호화 한다. 서버와 클라이언트는 일련의 과정을 거쳐서
pre master secret을 master secret으로 바꾼다. master secret은 session key를 생성하는데,
이 session key를 대칭키로 사용하여 데이터를 주고받는다.

클라이언트와 서버는 핸드쉐이크 단계의 종료를 서로에게 알린다.
