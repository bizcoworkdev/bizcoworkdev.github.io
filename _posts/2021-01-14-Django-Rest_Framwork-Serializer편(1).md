---
layout: post
title: Django Rest Framework - Serailizer편(1)
subtitle: 개발자 이영석
author: 이영석
tags: [devlog, drf, nested_serializer]


---

# Django Rest Framework - Serializer 편(1)

Bizcowork에서는 RESTful한 endpoint 제공을 위해, Django Rest Framework를 활용합니다. DRF 설치, django에의 적용법과 같은 내용은 이미 설명을 잘 해둔 블로그가 많을뿐더러, 재미없는 이야기가 될 수 있으니 생략하려고 합니다.

아래는 [DRF Document](https://www.django-rest-framework.org/) 에서 설명하는 "Some reasons you might want to user REST framework" 입니다.

1. [WEB browsable API](https://restframework.herokuapp.com/)는 개발자들에게 많은 편의성을 제공합니다.
2. [OAuth1a](https://www.django-rest-framework.org/api-guide/authentication/#django-rest-framework-oauth) 와 [OAuth2](https://www.django-rest-framework.org/api-guide/authentication/#django-oauth-toolkit)를 위한 [인증정책](https://www.django-rest-framework.org/api-guide/authentication/)을 제공합니다.
3. ORM 방식과 non-ORM방식 둘다 지원하는 [Serialization](https://www.django-rest-framework.org/api-guide/serializers/)을 제공합니다.
4. 강력한 Customize를 제공합니다. - [더](https://www.django-rest-framework.org/api-guide/generic-views/) [강력한](https://www.django-rest-framework.org/api-guide/viewsets/) [기능](https://www.django-rest-framework.org/api-guide/routers/)이 필요없다면 [함수기반View](https://www.django-rest-framework.org/api-guide/views/#function-based-views)를 사용
5. 광범위한 문서를 제공하고 [우수한 Community](https://groups.google.com/g/django-rest-framework?pli=1)가 있습니다.
6. Mozzilla, Red Hat, Heroku, 그리고 Eventbrite사를 포함한 많은 기업에서 사용되어 신뢰성이 있습니다.

Django에서는 사실 RESTful endpoint 제작에 DRF말고 다른건 떠오르지 않을정도로 보편화된 오픈소스 라이브러리입니다. 그래서 오늘은 DRF에서 제공하는 가장 강력한 기능인 Serializer에 대해 알아보고 다양한 Model 상황에서 Serializer의 활용법에 대해 포스팅해보려 합니다.

<br><br>

# Serializer 

*강력한 기능인건 알겠는데, Serailizer가 뭔데?*

<br>

Serialize (직렬화) 는 여러가지 정의가 있는데 여기서 사용할 직렬화란, 서로 다른 환경에서 데이터를 주고받기 위하여 상호 이해할수있는 테이터 구조를 만드는 작업입니다. 

Client 와 Django 간의 데이터 통신을 위하여 Serialize, Deserialize가 필요한데 이를 위하여 DRF는 Serializer를 제공합니다.

## - Serializer Class

```python
from rest_framework import serializers
from snippets.models import Snippet, LANGUAGE_CHOICES, STYLE_CHOICES


class SnippetSerializer(serializers.Serializer):
    id = serializers.IntegerField(read_only=True)
    title = serializers.CharField(required=False, allow_blank=True, max_length=100)
    code = serializers.CharField(style={'base_template': 'textarea.html'})
    linenos = serializers.BooleanField(required=False)
    language = serializers.ChoiceField(choices=LANGUAGE_CHOICES, default='python')
    style = serializers.ChoiceField(choices=STYLE_CHOICES, default='friendly')

    def create(self, validated_data):
        """
        Create and return a new `Snippet` instance, given the validated data.
        """
        return Snippet.objects.create(**validated_data)

    def update(self, instance, validated_data):
        """
        Update and return an existing `Snippet` instance, given the validated data.
        """
        instance.title = validated_data.get('title', instance.title)
        instance.code = validated_data.get('code', instance.code)
        instance.linenos = validated_data.get('linenos', instance.linenos)
        instance.language = validated_data.get('language', instance.language)
        instance.style = validated_data.get('style', instance.style)
        instance.save()
        return instance
```

DRF에서 제공하는 가장 기본적인 Serailizer 형태는 위와 같습니다. 각각의 필드는 DB column과 그의 속성을 가지고 있고, Serializer는 이 속성들을 Serialize 하거나 Deserialize 합니다. 그리고 create와 update 메소드를 정의해줍니다. 여기서 `create()` 메소드는 DB 인스턴스 생성시의 동작을 정의하고, `update()` 메소드는 DB 인스턴스 수정시의 동작을 정의합니다. 

## - ModelSerializer Class

```python
class SnippetSerializer(serializers.ModelSerializer):
    class Meta:
        model = Snippet
        fields = '__all__'  # fields = ['title', 'style', ]
```

Model Serializer의 경우에는 기본 Serializer class를 리팩토링한 Serializer입니다. 한눈에 보기에도 기본형태보다 더 간단해서, 편리하게 사용할 수 있습니다. 필드는 Model에서 정의된 필드들과 속성을 그대로 가져가고, 일부만 필요한 경우, meta class에서 선언해줄 수 있습니다. `create()`와 `update()`메소드도 정의되어있고 기본형태에서 다른 작업이 필요한 경우에는 Override 하여 구현해주면 됩니다. 또한 `validate()` 메소드로 유효성 검사를 Override 할 수 있지만, 저희는 유효성 검사의 경우엔 Model단에서 처리하기 때문에 구현하지 않았습니다.

<br>

*그래서 어떻게 사용할건데?*

<br>

```python
from django.db import models
from django.utils.translation import gettext_lazy as _


class CustomGroup(models.Model):
    name = models.CharField(_('그룹명'), max_length=50, unique=True)
    description = models.CharField(
        _('설명'),
        max_length=100,
        blank=True,
        default=''
    )
```

<br>

```python
from rest_framework import serializers
from .models import CustomGroup


class CustomGroupSerializer(serializers.ModelSerializer):
    class Meta:
        model = CustomGroup
        fields = '__all__'
```

위는 실제 저희 Bizcowork에서 사용하고있는 간단한 Group Model과 Group Serializer입니다. 해당 Serailizer에 View를 통해 Response로 data를 입력해주면, 올바른 요청인지 판단한 후, DB에 insert 해주거나 update를 하는 가장 기본적인 Serailizer 형태입니다.

<br>

# DRF에서의 Serializer

DRF에서 Request를 보내면 Serializer가 어떻게 동작하는지 확인 할 수 있습니다. 

POST - GET Flow를 따라가기 위해서 Deserialize - Serialize 순으로 설명해보도록 하겠습니다.

## Request

```python
POST app/group HTTP/1.1
Host: 127.0.0.1:8000
Authorization: Token abcd
Content-Type: application/json
Content-Length: 67

{   
    "name" : "mygroup",
    "description": "mygroup 입니다."
}

GET app/group HTTP/1.1
Host: 127.0.0.1:8000
Authorization: Token abcd
```

<br>

## Serialize and Deserialize

```python
'''
Deserialize
'''
print(type(request.data))
# <class 'dict'>
serializer = CustomGroupSerializer(data=request.data)
serializer.is_valid()
print(serializer.validated_data)
# {'name': 'mygroup', 'description': 'mygroup 입니다.'}
print(type(serializer.validated_data))
# <class 'rest_framework.utils.serializer_helpers.ReturnDict'>

'''
Serialize
'''
mygroup = CustomGroup.objects.all()
print(mygroup)
# <QuerySet [<CustomGroup: mygroup>]>
serializer = CustomGroupSerialzier(mygroup)
print(serializer.data)
# [{'name': 'mygroup', 'description': 'mygroup 입니다.'}]
```

 POST로 Client가 String 형태 json data를 Django에 HTTP 통신으로 전송하면, Serializer는 이를 Django에서의 유의미한 데이터로 변경하기 위하여 `is_valid()` 메소드로 검증한뒤, 검증된 Dict 형태 `<class 'rest_framework.utils.serializer_helpers.ReturnDict'>`로 변환하고 이를 Deserialize라고 합니다.

반대로 GET으로 전송하게되면(여기서는 Read 동작을 수행하도록 하였습니다), Serializer는 QuerySet이나 Model 객체를 Client가 사용할 수 있는 json형식으로 변환하고 이를 Serialize라고 합니다.

# 좀 더 복잡한 Model의 Serializer

복잡한 Model(OneToMany, ManyToMany)에서 Serializer의 기능은 더욱 강력합니다. 간단한 예시를 들어보겠습니다.

```python
class CustomUser(AbstractBaseUser):
    user_id = models.CharField(_('아이디'), max_length=20, unique=True)
    group = models.ManyToManyField(CustomGroup, through='UserGroup')
    
    USERNAME_FIELD = 'user_id'
    objects = UserManager()
    
    
class CustomGroup(models.Model):
    name = models.CharField(_('그룹명'), max_length=50, unique=True)
    description = models.CharField(_('설명'), max_length=100, blank=True, default='')
    
    
class UserGroup(models.Model):  # for many to many
    user = models.ForeignKey(CustomUser, on_delete=models.CASCADE, null=True)
    group = models.ForeignKey(CustomGroup, on_delete=models.CASCADE, null=True)
```

```python
class CustomUserSerializer(serializers.ModelSerializer):
    def update(self, instance, validated_data):
        user = super().update(instance, validated_data)

        if 'password' in validated_data:  # patch password
            user.set_password(validated_data['password'])
        user.save()
        return user

    class Meta:
        model = CustomUser
        extra_kwargs = {"password": {"write_only": True}}
```

위와 같은 ManyToMany 관계에서, 기본적인 ModelSerializer와 View를 선언하여 유저정보를 RESTful하게 가져올 수 있습니다. (View의 작성은 생략하겠습니다.) 여기서 특정 User를 GET 하게된다면, group에 해당하는 정보는 group의 pk값이 list로 나타나게 됩니다. 만약 group의 name과 description 정보도 함께 표시하고 싶다면, 두 table을 JOIN 한 뒤, deserialize 하여야 하는데, 이 역할을 Serializer가 수행 할 수 있습니다.

```python
class CustomUserSerializer(serializers.ModelSerializer):
    group = CustomGroupSerializer(many=True, read_only=True)
    
    def update(self, instance, validated_data):
        user = super().update(instance, validated_data)

        if 'password' in validated_data:  # patch password
            user.set_password(validated_data['password'])
        user.save()
        return user

    class Meta:
        model = CustomUser
        extra_kwargs = {"password": {"write_only": True}}
```

group에 위에 작성했던 CustomGroupSerializer만 할당해주면, 최종적으로 CustomGroup 모델이 JOIN된 JSON으로 Deserialize 되어 return 해줍니다. 이를 중첩된 Serializer이라하여 Nested Serializer라 합니다. Nested Serializer를 잘 활용하면, Client에 Response 해주는 data들을 개발자의 입맛대로 커스텀 할 수 있기 때문에 편리하고 강력한 기능이라 할 수 있습니다.

하지만 이러한 Nested Serializer는 query를 DB로 중복해서 보내는 성능이슈를 야기할 수 있습니다. 총 5명의 유저가 table에 있다면, 이를 List 해주는 Request를 보낼때, 총 6줄의 query를 실행하게됩니다. 아래는 List를 했을 때, 확인할 수 있는 query log 입니다.

```
(0.001) SELECT "custom_user"."id", "custom_user"."password", "custom_user"."user_id", "custom_user"."reg_date" FROM "custom_user" ORDER BY "custom_user"."reg_date" ASC; args=()

(0.001) SELECT "custom_group"."id", "custom_group"."name", "custom_group"."description" FROM "custom_group" INNER JOIN "custom_user_group" ON ("custom_group"."id" = "custom_user_group"."group_id") WHERE "custom_user_group"."user_id" = 1; args=(1,)

(0.001) SELECT "custom_group"."id", "custom_group"."name", "custom_group"."description" FROM "custom_group" INNER JOIN "custom_user_group" ON ("custom_group"."id" = "custom_user_group"."group_id") WHERE "custom_user_group"."user_id" = 2; args=(2,)

(0.001) SELECT "custom_group"."id", "custom_group"."name", "custom_group"."description" FROM "custom_group" INNER JOIN "custom_user_group" ON ("custom_group"."id" = "custom_user_group"."group_id") WHERE "custom_user_group"."user_id" = 3; args=(3,)

(0.000) SELECT "custom_group"."id", "custom_group"."name", "custom_group"."description" FROM "custom_group" INNER JOIN "custom_user_group" ON ("custom_group"."id" = "custom_user_group"."group_id") WHERE "custom_user_group"."user_id" = 4; args=(4,)

(0.000) SELECT "custom_group"."id", "custom_group"."name", "custom_group"."description" FROM "custom_group" INNER JOIN "custom_user_group" ON ("custom_group"."id" = "custom_user_group"."group_id") WHERE "custom_user_group"."user_id" = 5; args=(5,)
```

만약, 10000명의 회원이 있는 경우에는 10000줄의 query를 실행하기 때문에 성능적으로 문제가 일어 날 수 밖에 없습니다. 이를 해결하는 방법으로, [to_representation](https://www.django-rest-framework.org/api-guide/relations/)과 [select_related](https://docs.djangoproject.com/en/3.1/ref/models/querysets/#select-related), [prefetch_related](https://docs.djangoproject.com/en/3.1/ref/models/querysets/#prefetch-related)가 있습니다. 이는 다음 포스트에서 다뤄보도록 하겠습니다.