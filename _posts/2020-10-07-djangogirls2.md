---
title: 파이썬 - djangogirls(2)/설정하기2
category:
- Python
tag:
- Django
---

데이터베이스 소프트웨어는 다양하게 존재한다. 그 중 Django에는 기본적으로 sqlite3를 지원한다. sqlite3는  Public Domain Software라고 한다. 그냥 오픈소스에 무료로 사용할 수 있는 것 같다. 게다가 설치시 setup이나 Admin과 같이 다른 DB를 설치할때 필요한 서버 프로세스 설정들이 필요없다. 임베디드(내장된) SQL 데이터베이스 엔진으로 Django에 이미 탑재되어 있다. 
차후에는 sqlite3가 아닌 mysql이나 oracle과 같은 데이터베이스 소프트웨어를 연동시킬 일이 있을 것이다. 그때 DB를 더 자세하게 살펴보겠다.
sqlite3는 mysite/settings.py 안에 이미 설치되어 있다.

    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.sqlite3',
            'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
        }
    }


사용법은 간단하다. 이미 탑재되어 있기 때문에 python에 명령만 내리면 된다. manage.py가 있는 디렉토리로 이동해서 python manage.py migrate를 입력하면 된다. 이후에 python manage.py runserver를 실행하면 정상적으로 웹서버를 시작할 수 있다.

이후에, Djangogirls는 Model을 만든다. 여기서 객체 지향 프로그래밍(OOP)에 대한 간략한 설명이 나온다. 물론 정확하게 이해하고 있는바는 없지만, 그래도 간단하게 정리하고 넘어가면, 객체 지향 프로그램은 개발자가 필요로 하는 자료구조, 데이터 등을 먼저 설계한 다음에 이들을 순서와 흐름에 맞게 개발하는 방식이다. 
흔히 객체 지향 프로그래밍과 절차 지향 프로그래밍을 대립적으로 생각하기 쉬운데, 프로그래밍이라고 하는 근본적인 진행 과정이 있기 때문에, '절차'와 '객체'라고 하는 단어에 방점이 찍히기보다는 '절차 지향'과 '객체 지향'으로 나누어서 이해해야 할 것 같다. 이후 공부를 더 하다보면 다양한 프로그래밍에 대해 다룰 기회가 있을 것 같다.

객체는 속성과 행위로 구분된다. 여기서 속성은 객체 속성, 행위는 메서드라고 하는데, 부르는 이름이 다양한 것 같다. 객체 속성은 필드라고도 부르는 것 같고, 메서드는 함수라고도 부르는 것 같다. 본 tutorial에서는 간단한 게시판을 만드는 프로젝트를 진행한다. 따라서 여기서 만든 모델은 Post로 게시글을 만든다. 그 하위에 제목, 내용, 글쓴이, 작성일, 게시일을 만드는데, 우리가 흔히 사용했던 엑셀의 스프레드시트라고 생각하면 쉽다. 2차원 배열의 표를 만든다고 생각하면 되고, 1개의 표를 만든다고 생각하면 된다. 이를 표처럼 나타내면 다음과 같다.

- Post Model의 구성

|ID | 제목  |내용|글쓴이| 작성일 | 게시일 |
| -------- | -------- | -------- | -------- | -------- |-------- |
| 0     |  post1     | text     | author     | 2020-10-07     | 2020-10-07     |

이런 식으로 우리가 알고 있는 2차원 배열로 DB인 sqlite3에 하나씩 저장한다고 보면 된다. 모든 데이터들은 이런 방식으로 저장이 되며, 이를 생성하고, 읽고, 수정하고, 삭제하는 것이 기본적인 DB의 수행 과정이라고 보면 된다. 이후에 join이나 다양한 걸 배우겠지만, 인터넷으로 배우기만 해서 아직 써보지는 못했다.

이후에 Djangogirls는 프로젝트를 시작한다. manage.py가 존재하는 디렉토리로 이동해서 python manage.py startapp blog를 실행하여 blog app을 만든다. 그러면 디렉토리 구성이 다음과 같아진다.

    djangogirls
    ├── mysite <<startproject를 통해서 만든 프로젝트 이름
    |       __init__.py
    |       settings.py
    |       urls.py
    |       wsgi.py
    ├── manage.py
    └── blog <<start blog를 통해서 만든 애플리케이션의 이름
        ├── migrations
        |       __init__.py
        ├── __init__.py
        ├── admin.py
        ├── models.py
        ├── tests.py
        └── views.py

이후, myste/settings.py에 이 app을 추가해야 한다. INSTALLED_APPS에 추가한 app의 이름인 'blog'를 추가한다.

    INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
        'blog', <<app name 추가
    ]

이걸로 app을 사용하기 위한 준비는 끝이났다. 다음에는 Model에 Post class를 코딩하기 시작한다. 이는 다음에 하도록 하겠다.
