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

&nbsp;&nbsp;&nbsp;&nbsp;이전에는 타이핑하기만 하고 설명을 얼핏 보고 넘어갔다. 하지만 지금은 정리하기 때문에 django model field reference를 보고 정리하고자 한다.
Model field reference에 보면 Field options와 Field types, Relationship fields와 Field attribute reference가 있다. Field attribute reference는 영어가 딸려서 어떤건지 모르겠다. 그 외 필드 옵션과 필드 타입, 관계 필드는 어느정도 예상이 가기에 위 코드에 맞춰서 공부를 한 뒤 정리하겠다.

&nbsp;&nbsp;&nbsp;&nbsp;먼저, 위 코드에 있는 CharField, TextField, DateTimeField는 필드 타입을 해당 객체의 모델 타입을 결정하고, 필드 타입에 ()에 들어가는 settings, default, blank, null, max_length, settings, on_delete는 필드 옵션이다. 필드 타입에서 추가적으로 주는 옵션 사항이다.
ForeignKey는 관계 필드로 관계를 형성하는 필드이다. 그럼 하나씩 살펴보자.

##### CharField(max_length=None, **options)
&nbsp;&nbsp;&nbsp;&nbsp;문자열 필드이다. 작은 것부터 어느정도 큰 것 까지 다룰 수 있는 것 같다. max_length 필드로 최대 길이를 제한할 수 있다. 주로 이름이나 포스트의 제목과 같은 어느정도 크기가 제한되어 있는 문자열을 담을 때 사용하면 좋다.

##### TextField(**options)
&nbsp;&nbsp;&nbsp;&nbsp;문자열 필드로 CharField보다 큰 데이터를 담을 수 있다. max_length를 지정하면 Textarea 위젯이 자동적으로 폼 필드에 반영되지만, 이는 모델이나 데이터베이스에서 담당하는게 아니라 CharField에서 max_lenght를 사용하라고 하는데, 무슨 말인지 모르겠다. 일단은 TextField는 CharField보다 큰 모델이니 포스트의 내용을 담기에 적합하다.

##### DateTimeField(auto_now=False, auto_now_add=False, **options)
&nbsp;&nbsp;&nbsp;&nbsp;날짜와 시간을 담당하는 필드로 Python의 datetime.datetime 인스턴스를 참조한다고 한다. extra arguemnts를 DateField랑 동일하게 사용한다고 한다. 다만 DateField는 Python의 datetime.date 인스턴스를 참조한다. DateField는 날짜를 받고, DateTimeField는 날짜와 시간까지 받을 수 있는 것 같다.

##### ForeignKey(to, on_delete, **options)
&nbsp;&nbsp;&nbsp;&nbsp;다대일 관계를 형성하는 관계형 필드이다 to에 관계를 형성할 모델을 대입해야 하며, on_delete에는 삭제에 관한 옵션을 부가한다. to에서는 다른 app의 model도 관계를 형성할 수 있으며, 이 경우에는 full application label을 사용해야 한다. 즉, app name.model을 통해서 불러올 수 있다.   
&nbsp;&nbsp;&nbsp;&nbsp;ForeignKey에서는index를 자동으로 생성시킨다. db의 id값을 자동으로 만들어주는 것 같은데, 이는 db_index를 False로 설정함으로서 사용을 중지할 수 있다. 하지만 굳이 id값을 자동으로 만들어주는 기능을 False로 바꿀 필요는 없을 것 같다.   
&nbsp;&nbsp;&nbsp;&nbsp;Django는 to옵션에 설정한 model을 필드 이름에 "_id"를 추가해서 데이터베이스를 추가한다.   
&nbsp;&nbsp;&nbsp;&nbsp;다음으로, FOreignKey에는 삭제할 경우 어떤 동작을 취해야 하는지를 on_delete 옵션을 통해 알려준다.   
&nbsp;&nbsp;&nbsp;&nbsp;ForeignKey.on_delete는 ForeignKey에 의해 참조된 데이터베이스가 삭제될 때 어떤 동작을 해야 하는지를 알려준다. DB상에서 참조무결성을 유지하기 위해서 삭제될 때 어떻게 했으면 좋은지를 옵션으로 주는 것이다.

&nbsp;&nbsp;&nbsp;&nbsp;CASCADE는 ForeignKey가 바라보는 값이 삭제될 때, ForeignKey를 포함하는 데이터베이스를 삭제하는 것이다. 행(row) 단위로 삭제한다고 생각하면 된다.

&nbsp;&nbsp;&nbsp;&nbsp;PROTECT는 ForeignKey가 바라보는 값이 삭제될 때 삭제되지 않도록 ProtectedError를 발생시킨다.

&nbsp;&nbsp;&nbsp;&nbsp;RESTRICT는 Django 3.1에 새로 추가된 옵션이다. ForeignKey가 바라보는 값이 삭제될 때 RestrictedError를 발생시킨다. PROTECT와는 다르게 특정 조건에 한해서는 삭제가 가능한 것 같은데, API를 봐도 무슨 소린지 잘 모르겠다. 나중에 쓸 때가 있을 때 다시 공부해야 겠다.

&nbsp;&nbsp;&nbsp;&nbsp;SET_NULL은 null=True일때, ForeignKey가 바라보는 값이 삭제될 경우, null값으로 변환시킨다.

&nbsp;&nbsp;&nbsp;&nbsp;SET_DEFAULT는 ForeignKey가 바라보는 값이 삭제될 때, default값으로 바꾼다. 옵션으로 default 값을 주었을 때 가능하다. 

&nbsp;&nbsp;&nbsp;&nbsp;SET()은 ForeignKey가 바라보는 값이 삭제될 때 SET에 설정된 함수의 결과값을 호출한다.

&nbsp;&nbsp;&nbsp;&nbsp;DO_NOTHING ForeignKey가 바라보는 값이 삭제되더라도 아무것도 하지 않는다.

&nbsp;&nbsp;&nbsp;&nbsp;ForeignKey.limit_choices_to 는 ModelForm 또는 admin 사용시에 사용 가능한 선택지를 제한적으로 보여준다고 한다. { } dictionary 형태로 사용이 가능하며, 1인 관계에 질의를 해서 필드와 필드값이 충족된 Model만 관계짓는다.

    staff_member = models.ForeignKey(
        User,
        on_delete=models.CASCADE,
        limit_choices_to={'is_staff': True},
    )

&nbsp;&nbsp;&nbsp;&nbsp;위의 경우, 관계가 1인 User의 필드값에서 is_staff=True 인 것만 해당 model의 staff_member로 관계지어지는 것이다.

&nbsp;&nbsp;&nbsp;&nbsp;ForeignKey.related_name 는 정참조가 아닌 역참조에서 해당 db를 호출할 수 있도록 해준다. 역참조관계에서도[classname]_set이라는 속성을 통해서 접근이 가능하며, related_name을 통해서는 사용자가 지정한 옵션으로 참조를 가능하게 해준다. related_name이 필수적인 경우는, 참조하고 있는 컬럼이 2개 이상이라 [classname]_set으로는 찾기 어려운 경우에 해당한다. 해당하는 경우에는 related_name을 따로 주면 구분해서 호출할 수 있다.

ex ) Post.objects.all() 이 아니라 Post.[classname]_set 혹은 related_name.all()로 호출이 가능하다.

&nbsp;&nbsp;&nbsp;&nbsp;관계를 형성하기를 원하지 않는다면 related_name을 '+'값 혹은 '+'로 끝나도록 설정하면 된다고 한다.

&nbsp;&nbsp;&nbsp;&nbsp;ForeignKey.related_query_name 는 Django QuerySet에서 사용이 가능하다. 역참조에서 Query문을 사용하려고 할 때 필터값을 related_query_name을 주어 필터를 사용할 수 있다.   
ex ) Post.objects.filter("""related_query_name"""= " ")

&nbsp;&nbsp;&nbsp;&nbsp;이외에 ForeignKey.to_field , ForeignKey.db_constraint , ForeignKey.swappable 가 ForeignKey의 여러 옵션값으로 남아있으나, 아직 공부가 덜 된 상태여서 더 공부를 한 다음에 추가하도록 하겠다.
