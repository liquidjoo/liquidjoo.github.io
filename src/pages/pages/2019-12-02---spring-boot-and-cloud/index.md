---
title: spring boot란 그리고 마이크로서비스 - 1
date: "2019-02-12T22:00:00.121Z"
layout: post
draft: false
path: "/posts/about-spring-boot/"
category: "Dev"
description: "스프링 부트 그리고 클라우드, 마이크로서비스에 대해"
---

### Spring Boot?
- 스프링 부트는 스프링 프레임워크를 재구성한 것으로, 스프링의 핵심 기능은 수용하지만 많은 엔터프라이즈 기능을 제거하고 그 대신 자바 기반의 REST 지향 마이크로서비스 프레임워크를 제공
- 단순한 애너테이션(@)으로 자바 개발자는 외부 애플리케이션 컨테이너 없이도 패키지하고 배포할 수 있는 REST 마이크로서비스를 신속하게 구축할 수 있다.

### Spring Cloud
- 마이크로서비스가 클라우드 기반 애플리케이션을 구축하는 일반적인 아키텍쳐 패턴 중 하나로 발전했기 때문에 스프링 개발 커뮤니티는 우리에게 스프링 클라우드를 보여줌
- 스프링 클라우드 프레임워크를 사용하면 사설(private) 및 공용(public) 클라우드에 마이크로서비스를 쉽게 운영하고 배포할 수 있다.

### MicroService
- 확장성과 중복성이 높은 애플리케이션을 구축하기 위해 독립적으로 빌드하고 배포할 수 있는 작은 서비스로 애플리케이션을 분해해야 한다는 역설을 수용해야 한다.
```
- 유연성
- 회복성
- 확장성
```

#### 유연성
- 새로운 기능을 신속하게 제공하도록 분리된 서비스를 구성하고 재배치할 수 있어야 한다.

#### 회복성
- 애플리케이션 한 부분의 저하로 인해 전체가 망가지는 애플리케이션이 아니여야 한다. 실패는 애플리케이션의 작은 부분에 국한되어 전체 장애로 확대되기 전에 억제된다.

#### 확장성
- 분리된 서비스를 여러 서버에 수평적으로 쉽게 분산할 수 있어 기능 및 서비스를 적절히 확장할 수 있다.


### MicroSerivce는 코드 작성 이상을 의미
- 개별 마이크로서비스 구축에 관련된 개념은 이해하기 쉽지만 견고한 마이크로서비스 애플리케이션을 실행하고 지원하는 것은 서비스 코드를 작성하는 것 이상이 필요하다.

다음엔 마이크로서비스 패턴에 대해서 정리를..

```
출처 - 스프링 마이크로서비스 코딩 공작소 (책)

* 공부하면서 정리할 겸에 블로깅을 하는 것이라 문제가 될 시에 해당글을 삭제 하겠습니다.
```