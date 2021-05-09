---
title: '[Network] TCP 3-way handshake'
date: 2021-05-09 19:44:63
category: 'Network'
thumbnail: { thumbnailSrc }
draft: false
---

TCP 프로토콜은 연결 지향 프로토콜이다. 즉 연결이 확립된 상태에서 통신을 시작한다.

연결을 확립할 때는

1. 클라이언트가 서버에 요청 패킷을 보낸다(SYN).
2. 서버가 클라이언트의 요청을 받아들이는 패킷을 보낸다(SYN+ACK).
3. 클라이언트는 이를 최종적으로 수락하는 패킷을 보낸다(ACK).

위 과정을 3-way handshake라고 한다. TCP를 이용한 데이터 통신을 할 때는 위 과정을 먼저 거쳐서
연결이 확립된 후에 데이터를 전송한다.

![B68FAC74-EAAC-4DA9-85F8-9F011551F7F1](https://user-images.githubusercontent.com/63030569/117566096-977a9e80-b0ef-11eb-89dd-0bc2ca8cc2f2.png)

<br>

클라이언트 혹은 서버로부터 전달된 TCP 세그먼트의 Flag 비트를 보고 SYN인지 ACK인지 알 수 있다.

![TCPseg](https://user-images.githubusercontent.com/63030569/117566416-80d54700-b0f1-11eb-88d8-6d4ca6a7096f.jpeg)
