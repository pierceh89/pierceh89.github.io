---
layout  : wiki
title   : A Deep Dive into Iptables and Netfilter Architecture 요약 (WIP)
summary : Iptables 와 netfilter 이해하기
date    : 2022-01-28 13:32:01 +0900
updated : 2022-02-08 11:06:05 +0900
tag     : iptables firewall linux
toc     : true
public  : true
parent  : [[network]]
latex   : false
---
* TOC
{:toc}

## Intro

`iptables`는 널리 사용되는 `netfilter` 패킷 필터링 프레임워크의 인터페이스 툴이다. `iptables` 아키텍처를  모르는 상태에서는 어려운 문법 뿐만이 아니라 프레임워크의 많은 상호 연관된 부분 때문에 의지할 수 있는 방화벽 정책을 생성하는 것이 쉽지 않을 것이다. 

## What Are IPTables and Netfilter?

`iptables`는 리눅스에서 가장 보편적으로 사용되는 방화벽 소프트웨어다. `iptables`는 리눅스 커널의 네트워킹 스택의 패킷 필터링 훅(hook)과 상호작용하여 작동한다. 이런 커널 훅은 `netfilter` 프레임워크로 알려져 있다.

네트워크 시스템으로 들어가는 모든 패킷(ingoing outgoing 모두)은 스택을 통해 진행되면서 이런 훅을 트리거한다. `iptables`와 관련된 커널 모듈은 트래픽이 방화벽 규칙에 의해 규정된 조건을 준수하는지 확인하기 위해 이러한 훅에 등록한다. 

## Netfilter hooks

프로그래머가 등록할 수 있는 `netfilter` 훅은 다섯가지가 있다. 패킷이 스택을 따라 진행하면서 이러한 훅에 등록된 커널 모듈을 트리거한다. 패킷이 트리거하는 훅은 패킷이 들어오는지 나가는지, 패킷의 도착지, 패킷이 이전 포인트에 드랍됐는지 혹은 거절됐는지에 따라 다르다. 다음은 네트워킹 스택에서 잘 정의된 지점을 대표하는 훅들이다.

- `NF_IP_PER_ROUTING`: incoming 트래픽이 네트워크 스택에 들어오자마자 트리거되는 훅이다. 패킷을 어디로 보낼지에 따라 라우팅 결정이 처리되기 전에 훅이 처리된다.
- `NF_IP_LOCAL_IN`: incoming 패킷의 목적지가 로컬 시스템인 경우 라우팅 된 후 바로 트리거된다.
- `NF_IP_FORWARD`: incoming 패킷이 다른 호스트로 forward돼야 하는 경우 라우팅 된 후 바로 트리거된다.
- `NF_IP_LOCAL_OUT`: 로컬에서 생성된 outbound 트래픽인 경우 네트워크 스택에 도달하는 즉시 트리거된다.
- `NF_IP_POST_ROUTING`: 유선으로 전송되기 직전인 outgoing 트래픽이나 forward 트래픽에 의해 트리거된다.

커널 모듈은 훅에 등록할 때 우선순위를 제공해서 여러 개의 모듈이 순서대로 등록 될 수 있게 한다. 각 모듈은 차례로 호출되고 패킷으로 수행해야 하는 작업을 작업을 수행해야하는 작업을 나타내는 처리 후 netfilter 프레임워크에 결정을 반환한다.

## IPTables Tables and Chains

`iptables` 방화벽은 규칙을 정리하기 위해 테이블을 사용한다. 이러한 테이블은 결정을 내리는 데 사용되는 유형에 따라 규칙을 분류한다. 가령 네트워크 주소 번역을 다루는 룰이라면, `nat`테이블에 놓일 것이다. 패킷이 목적지로 가는 것을 허용할지에 관한 룰이라면 `filter`테이블에 추가될 것이다.

각 `iptables` 테이블 내에서 별도의 "체인"내에서 규칙이 추가로 구성된다. 테이블은 테이블이 보유한 규칙의 일반적인 목적에 따라 정의되지만 기본 제공 체인은 테이블을 트리거하는 `netfilter`훅을 나타낸다. 체인은 기본적으로 규칙이 평가되는 시기를 결정한다.

보다시피 빌트인 체인의 이름은 연결된 `netfilter`훅의 이름을 반영한다.

- `PREROUTING`: `NF_IP_PRE_ROUTING`에 의해 트리거 된다.
- `INPUT`: `NF_IP_LOCAL_IN`에 의해 트리거 된다.
- `FORWARD`: `NF_IP_FORWARD`에 의해 트리거 된다.
- `OUTPUT`: `NF_IP_LOCAL_OUT`에 의해 트리거 된다.
- `POSTROUTING`: `NF_IP_POST_ROUTING`에 의해 트리거 된다.

체인을 통해 관리자는 패킷의 전달 경로에서 규칙을 평가할 위치를 제어할 수 있다. 각 테이블이 여러 개의 체인이 있기 때문에 처리 시 여러 지점에서 테이블의 영향을 받을 수 있다. 특정 유형의 결정은 네트워크 스택의 특정 지점에서만 의미가 있기 때문에 모든 테이블에는 각 커널 훅에 등록된 체인이 없다.

`netfilter`커널 훅은 다섯 개 뿐이라서 여러 테이블의 체인이 각 훅에 등록된다. 예를 들어 세 개 테이블은 `PREROUTING` 체인이 있다. 이러한 체인이 연결된 `NF_IP_PRE_ROUTING` 후크에 등록되면 각 테이블의 `PREROUTING` 체인이 호출되는 순서를 지정하는 우선 순위를 지정한다. 가장 높은 우선 순위의 `PREROUTING` 체인 내부의 각 규칙은 다음 `PREROUTING` 체인으로 이동하기 전에 순차적으로 평가된다.

## Which Tables are Available?

잠시 뒤로 물러나서 `iptables`가 제공하는 테이블들을 살펴보자. 이 테이블들은 패킷을 평가하기 위해 관심 영역으로 구성된 고유한 규칙 집합을 나타낸다.

### The Filter Table

필터 테이블은 `iptables`에서 가장 널리 쓰이는 테이블 중 하나이다. 필터 테이블은 패킷을 도착지로 계속 보낼지 또느 요청으 거부할지 결정하는데 사용한다.

### The NAT Table

NAT 테이블은 network address translation 규칙을 구현하기 위해 사용한다. 패킷이 네트워크 스택에 들어오면 이 테이블의 규칙은 패킷의 소스나 목적지 주소를 어떻게 수정할지 결정한다. 이 테이블은 직접 액세스가 불가능하 경우 패킷을 네트워크로 라우팅하는데 자주 사용한다.

### The Mangle Table

Mangle 테이블은 패킷의 IP 헤더를 변경하는데 사용한다. 예를 들어 TTL(Time To Live)을 조정하거나 패킷이 유지할 수 있는 네트워크 홉 수를 늘리거나 줄일 수 있다. 

This table can also place an internal kernel “mark” on the packet for further processing in other tables and by other networking tools. This mark does not touch the actual packet, but adds the mark to the kernel’s representation of the packet.

### The Raw Table

`iptables`는 패킷이 이전 패킷과의 관계와 관련하여 평가된다느 점에서 stateful하다. `netfilter` 프레임워크 위에 만들어진 연결 추저 기능은 `iptables`가 진행 중인 연결의 일부로 패킷이나 세션을 보도록 허용한다. 연결 추적 로직은 주로 패킷이 네트워크 인터페이스에 도달한 직후 적용된다.

Raw 테이블은 굉장히 좁게 정의된 함수가 있다. 이것의 유일한 목적은 연결 추적을 거부하기 위해 패킷을 표시하느 매커니즘으 제공하는 것이다.

### The Security Table

Security 테이블은 패킷에 내부 SELinux 보안 컨텍스트 표시르 설정하는 데 사용되며, 이는 SELinux 또는 SELinux 보안 컨텍스트를 해석할 수 있는 기타 시스템이 패킷으 처리하느 방식에 영향을 미친다. 이러한 표시는 패킷 또느 연결별로 적용할 수 있다.

## Which Chains are Implemented in Each Table?

## IPTables Rules

## Jumping to User-Defined Chains

## IPTables and Connection Tracking

## Conclusion

`netfilter` 커널 훅은 시스템에 의해 처리되는 파워풀한 패킷 컨트롤을 제공한다. `iptables` 방화벽은 이런 기능을 활용하여 정책 요구 사항을 커널에 전달하는 유연하고 확장 가능한 방법을 제공한다. 그래서 이 둘이 어떻게 맞물리는지 배워서 서버 환경을 잘 보호해보자.

## Reference

[원문](https://www.digitalocean.com/community/tutorials/a-deep-dive-into-iptables-and-netfilter-architecture)
