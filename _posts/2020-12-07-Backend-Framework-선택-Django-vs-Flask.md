---

layout: post
title: Backend Framework 선택(Django vs Flask)
subtitle: 개발자 이영석
tags: [devlog]

---

# 개요

기존 외부업체에서 진행한 1cup 페이지는 express로 개발되어 시스템 확장이 힘들거같아, 새 프로젝트를 진행하기로 하였다. 그 중 백엔드쪽 그 중 python Framework에서 어떤 것을 택할지 의사결정을 하게 되었다. Python framework의 양대산맥인 Flask, Django중 고민하였는데, 양쪽 기본환경을 각각 세팅해보고 비교해보았다. 결론부터 말하자면, 새 프로젝트의 백엔드는 **Django**를 이용하기로 하였다. 이번 포스팅에는 Django와 Flask의 장단점, 활용기업을 알아보고 왜 Django를 선택하였는지 써보았다.



<center><img src="https://seeklogo.com/images/D/django-logo-F46C1DD95E-seeklogo.com.png" alt="Django Logo Vector" style="zoom: 67%;" />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://seeklogo.com/images/F/flask-logo-44C507ABB7-seeklogo.com.png" alt="Flask Logo Vector" style="zoom: 67%;" /> 
</center>




# Pros and Cons

#### Flask

Flask는 Micro Framework, 간단히 말하면, 앱이 독립된 기능을 가지고 필요할 때만 통신하는 것이다. (Flask의 ``url_for()`` 가 단적인 예) 이는 확장성이 용이하고, 유연한 APP 개발이 가능하다. 그리고 처음 Flask 문서를 봤을 때도 느꼈지만 개발하기 정말 편하게 되어있다. Django와 다르게 RESTful 제약도 없고 러닝 커브가 완만하다.

하지만, 유연함과 확장성, 손쉬운 개발은 책임감을 부여한다. 뚜렷한 개발규칙이 존재하지 않는다면, 개발자 각각의 코드 스타일에 영향을 받기 쉽고, 협업하기 힘들게 된다. 그리고 CRUD based 앱에서 생산성을 고려하여 ORM 기능을 활용한다면, 별도 ORM extension인 **SQLAlchemy**를 사용하여야 하는데, 이는 Data mapper 기반이라 제약이 적은 Flask에서 제약을 받는 아이러니한 상황이 생길 수 있다.

 

#### Django

Django framework는 기본적인 기능들 특히 반복적으로 수행하는 로그인이나 인증과 같은 기능들은 대부분 구현이 되어있고 가져다 쓰기만 하면 되는 편리함이 있다. 그리고 강력한 Admin page와 DRF, Django Debug Toolbar 등 많은 사용자들에게 검증된 다양한 extension들이 있다. 

하지만, 강력한 기능에 뒤따르는 러닝커브와 제약은 Django의 단점이다. Django Rest Framework(DRF)는 강력하고 새로운 디자인패턴을 제공하지만, DRF의 러닝커브는 익히 알려져 있다시피 flask에 비하면 상당히 큰 편이다. 그리고 Django ORM의 경우엔 직관적이지만, Active record 기반이라 복잡한 read-only 쿼리의 경우엔 SQLAlchemy extension 보다 생산성이 떨어지기 쉽다.





# Reference Site

#### Django



<center><img src="https://www.instagram.com/static/images/ico/favicon-192.png/68d99ba29cc8.png"/></center>

<center>Instagram</center>

Instagram은 전 서비스가 python을 사용하고 Django framework로 개발된 가장 유명한 사이트이다. 아래는 Instagram의 개발 블로그 포스트 제목 : **[Web Service Efficiency at Instagram with Python](https://instagram-engineering.com/web-service-efficiency-at-instagram-with-python-4976d078e366)** 링크이다. 다수의 사용자가 이용하는 사이트인만큼 Django에서 일어날 수 있는 문제점과 그 해결법, 효율성으로 고민한 흔적이 포스팅 되어있다.

 

#### Flask

<center><img src="https://upload.wikimedia.org/wikipedia/commons/thumb/6/69/Netflix_logo.svg/200px-Netflix_logo.svg.png"/></center>

<center>Netflix</center>

Netflix는 DevOps로 Flask를 사용한 Winston을 사용하고 있다. 아래는 Netflix 개발 블로그에 있는 [Monitoring, alert and auto-remediation](https://netflixtechblog.com/python-at-netflix-bba45dae649e) 부분 전문이다.

> # Monitoring, alert and auto-remediation

> The Insight Engineering team is responsible for building and operating the tools for operational insight, alerting, diagnostics, and auto-remediation. With the increased popularity of Python, the team now supports Python clients for most of their services. One example is the [Spectator](https://github.com/Netflix/spectator-py) Python client library, a library for instrumenting code to record dimensional time series metrics. We build Python libraries to interact with other Netflix platform level services. In addition to libraries, the [Winston](https://medium.com/netflix-techblog/introducing-winston-event-driven-diagnostic-and-remediation-platform-46ce39aa81cc) and [Bolt](https://medium.com/netflix-techblog/introducing-bolt-on-instance-diagnostic-and-remediation-platform-176651b55505) products are also built using Python frameworks (Gunicorn + Flask + Flask-RESTPlus).
>





# 결론

Django 사용의 결정적인 이유로 몇 개 꼽아보자면, 

- 구성원이 Flask에 익숙치 않음
- 결성된지 얼마되지 않은 개발팀이라 rule을 새로 만드는데 들이는 시간이 많음
- 아직 개발자 인력을 모집 중이라, 깔끔한 코드가 필요
- 주된 ORM 이용방식이 CRUD가 될 예정이라 Active Record 쪽이 더 편리함

의 이유로 Django를 선택하였다.

