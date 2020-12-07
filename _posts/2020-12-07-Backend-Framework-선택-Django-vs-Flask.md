---

layout: post
title: Backend Framework 선택(Django vs Flask)
subtitle: 개발자 이영석
tags: [devlog]

---

# 개요

기존 외부업체에서 진행한 1cup 페이지는 express로 개발되어 시스템 확장이 힘들거같아, 새 프로젝트를 진행하기로 하였다. 백엔드쪽에서 고민을 많이 하였는데,  JAVA(Spring)와 python 중 택1 하기로 하였다. 그중 python을 사용하기로 했는데, 우선 배포환경 구성이 간편하고 스크립트언어라 쥬니어 개발자로서 새 프로젝트를 수행하기에 알맞는 것 같아 택했다. 그리고 python Framework에서 어떤 것을 택할지 의사결정을 하게 되었다. Python framework의 양대산맥인 Flask, Django중 고민하였는데, 양쪽 기본환경을 각각 세팅해보고 비교해보았다. 결론부터 말하자면, 새 프로젝트의 백엔드는 **Django**를 이용하기로 하였다. 이번 포스팅에는 Django와 Flask의 장단점을 알아보고 각각 세팅한 환경 그리고 왜 Django를 선택하였는지 써보았다.



<center><img src="https://seeklogo.com/images/D/django-logo-F46C1DD95E-seeklogo.com.png" alt="Django Logo Vector" style="zoom: 67%;" />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="https://seeklogo.com/images/F/flask-logo-44C507ABB7-seeklogo.com.png" alt="Flask Logo Vector" style="zoom: 67%;" /> 
</center>




# Pros and Cons

#### Flask

Flask는 Micro Framework, 간단히 말하면, 앱이 독립된 기능을 가지고 필요할 때만 통신하는 것이다. (Flask의 ``url_for()`` 가 단적인 예) 이는 확장성이 용이하고, 유연한 APP 개발이 가능하다. 그리고 처음 Flask 문서를 봤을 때도 느꼈지만 개발하기 정말 편하게 되어있다. Django와 다르게 RESTful 제약도 없고 러닝 커브가 완만하다.

하지만, 유연함과 확장성, 손쉬운 개발은 책임감을 부여한다. 뚜렷한 개발규칙이 존재하지 않는다면, 개발자 각각의 코드 스타일에 영향을 받기 쉽고, 협업하기 힘들게 된다. 그리고 CRUD based 앱에서 생산성을 고려하여 ORM 기능을 활용한다면, 별도 ORM extension인 **SQLAlchemy**를 사용하여야 하는데, 이는 Data mapper 기반이라 제약이 적은 Flask에서 제약을 받는 아이러니한 상황이 생길 수 있다.

 

#### Django

Django framework는 기본적인 기능들 특히 반복적으로 수행하는 로그인이나 인증과 같은 기능들은 대부분 구현이 되어있고 가져다 쓰기만 하면 되는 편리함이 있다. 그리고 강력한 Admin page와 DRF, Django Debug Toolbar 등 많은 사용자들에게 검증된 다양한 extension들이 있다. 

하지만, 강력한 기능에 뒤따르는 러닝커브와 제약은 Django의 단점이다. Django Rest Framework(DRF)는 강력하고 새로운 디자인패턴을 제공하지만, DRF의 러닝커브는 익히 알려져 있다시피 flask에 비하면 상당히 큰 편이다. 그리고 Django ORM의 경우엔 직관적이지만, Active record 기반이라 복잡한 read-only 쿼리의 경우엔 SQLAlchemy extension 보다 생산성이 떨어지기 쉽다.

>  https://medium.com/@kshitij/django-for-real-designers-f97adfef1355

<center><img src="https://miro.medium.com/max/700/1*9Vvj64asS418DOG96EdgDw.jpeg" style="zoom:50%;" /></center>

<center>  Django Learning Curve</center>



# 세팅환경

Django와 Flask 둘다 Docker-compose의 컨테이너에 올려 세팅했다.


|BackEnd|Web Server|FrontEnd|ORM|DataBase|
|:---:|:---:|:---:|:---:|:---:|
|**Django**|NGINX|React|Django ORM|Postgresql|
|**Flask**|NGINX|React|SQLAlchemy|Postgresql|

초기환경 세팅의 경우 두 Framework 다 어려운 부분은 없었다. Docker - Frontend - Backend - Webserver - DataBase 간 컨테이너 연결이 가장 어려웠고, 개별적인 세팅의 난이도는 동일했던 것 같다. Backend와 초기버전 세팅의 경우에는 다음에 배포쪽 세팅과 함께 포스팅 할 예정이다.

<center><img src="https://github.com/bizcoworkdev/bizcoworkdev.github.io/blob/master/assets/img/initial-setting.png?raw=true" style="zoom:80%;" /></center>
<center>초기세팅환경 조직도</center>






# 결론

Django 사용의 결정적인 이유로 몇 개 꼽아보자면, 

- 구성원이 Flask에 익숙치 않음
- 결성된지 얼마되지 않은 개발팀이라 rule을 새로 만드는데 들이는 시간이 많음
- 아직 개발자 인력을 모집 중이라, 깔끔한 코드가 필요
- 주된 ORM 이용방식이 CRUD가 될 예정이라 Active Record 쪽이 더 편리함
- 간단한 CRUD 앱을 만들었을 때, Django를 사용하는편이 더 여러사람이 협업해도 정돈된 코딩을 할 수 있을 것 같았음

의 이유로 Django를 선택하였다.

