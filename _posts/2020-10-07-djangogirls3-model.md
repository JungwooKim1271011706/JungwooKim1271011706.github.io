---
title: 파이썬 - djangogirls(3)/Model
category:
- Python
tag:
- Django
---

blog/model.py에 이렇게 코드를 추가하라고 한다.


    from django.conf import settings
    from django.db import models
    from django.utils import timezone

    class Post(models.Model):
        author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
        title = models.CharField(max_length=200)
        text = models.TextField()
        created_date = models.DateTimeField(
                default=timezone.now)
        published_date = models.DateTimeField(
                blank=True, null=True)

        def publish(self):
            self.published_date = timezone.now()
            self.save()

        def __str__(self):
            return self.title

이전에는 타이핑하기만 하고 설명을 얼핏 보고 넘어갔다. 하지만 지금은 정리하기 때문에 django model field reference를 보고 정리하고자 한다.
Model field reference에 보면 Field options와 Field types, Relationship fields와 Field attribute reference가 있다. Field attribute reference는 영어가 딸려서 어떤건지 모르겠다. 그 외 필드 옵션과 필드 타입, 관계 필드는 어느정도 예상이 가기에 위 코드에 맞춰서 공부를 한 뒤 정리하겠다.

먼저, 위 코드에 있는 CharField, TextField, DateTimeField는 필드 타입을 해당 객체의 모델 타입을 결정하고, 필드 타입에 ()에 들어가는 settings, default, blank, null, max_length, settings, on_delete는 필드 옵션이다. 필드 타입에서 추가적으로 주는 옵션 사항이다.
ForeignKey는 관계 필드로 관계를 형성하는 필드이다. 그럼 하나씩 살펴보자.

CharField(max_length=None, **options)
문자열 필드이다. 작은 것부터 어느정도 큰 것 까지 다룰 수 있는 것 같다. max_length 필드로 최대 길이를 제한할 수 있다. 주로 이름이나 포스트의 제목과 같은 어느정도 크기가 제한되어 있는 문자열을 담을 때 사용하면 좋다.

TextField(**options)
문자열 필드로 CharField보다 큰 데이터를 담을 수 있다. max_length를 지정하면 Textarea 위젯이 자동적으로 폼 필드에 반영되지만, 이는 모델이나 데이터베이스에서 담당하는게 아니라 CharField에서 max_lenght를 사용하라고 하는데, 무슨 말인지 모르겠다. 일단은 TextField는 CharField보다 큰 모델이니 포스트의 내용을 담기에 적합하다.

DateTimeField(auto_now=False, auto_now_add=False, **options)
날짜와 시간을 담당하는 필드로 Python의 datetime.datetime 인스턴스를 참조한다고 한다. extra arguemnts를 DateField랑 동일하게 사용한다고 한다. 다만 DateField는 Python의 datetime.date 인스턴스를 참조한다. DateField는 날짜를 받고, DateTimeField는 날짜와 시간까지 받을 수 있는 것 같다.

ForeignKey(to, on_delete, **options)
다대일 관계를 형성하는 관계형 필드이다.....