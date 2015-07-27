# django-marcador 튜토리얼 따라하기

## heroku 연동

* Heroku 회원 가입

* 운영체제에 맞는 Heroku Toolbeit 설치

* 로그인
```sh
$ heroku login
Enter your Heroku credentials.
Email: python@example.com
Password:
Authentication successful.
```

* App 생성 및 Git 연동
app name 을 생략하면 자동으로 생성된다.
heroku create [app name] 명령을 실행하는 위치가 git local repository 위치 이면 app 생성 이후에 remote heroku 가 추가됨.

```sh
$ heroku create test-tykim
Creating test-tykim... done, stack is cedar-14
https://test-tykim.herokuapp.com/ | https://git.heroku.com/test-tykim.git
Git remote heroku added.
```

만약 git local repository 위취가 아니라면, app 만 생성되고, remote heroku는 별도로 추가해야함.

```sh
$ heroku git:remote [app name]
Git remote heroku added.
```

git remote 리스트 보기
```sh
$ git remote -v
heroku  https://git.heroku.com/test-tykim.git (fetch)
heroku  https://git.heroku.com/test-tykim.git (push)
```

git remote 추가
```sh
$ git remote add heroku https://git.heroku.com/test-tykim.git
```

git remote URL 변경
```sh
$ git remote set-url heroku https://git.heroku.com/test-tykim.git
```

git remote 삭제
```sh
$ git remote rm heroku
```

* App 삭제
```sh
$ heroku destroy [app name]

 !    WARNING: Potentially Destructive Action
 !    This command will destroy test-tykim (including all add-ons).
 !    To proceed, type "test-tykim" or re-run this command with --confirm test-tykim

> test-tykim
Destroying test-tykim (including all add-ons)... done
```

* App 배포전 준비사항
  - requiremenets.txt
  - Procfile
    > web: gunicorn mysite.wsgi --log-file -
  - runtime.txt
    > python-3.4.2

* 로컬에서 실행해보기
  - 윈도우에서는 gunicorn 이 동작 할 수 없으므로 Procfile 의 내용을 `web: python manage.py runserver 0.0.0.0:5000` 로 수정한다.
```sh
$ foreman start
```

* App 배포
```sh
$ git push heroku master
Counting objects: 17, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (12/12), done.
Writing objects: 100% (17/17), 3.00 KiB | 0 bytes/s, done.
Total 17 (delta 0), reused 0 (delta 0)
remote: Compressing source files... done.
remote: Building source:
remote:
remote: -----> Python app detected
remote: -----> Installing runtime (python-2.7.10)
remote: -----> Installing dependencies with pip
remote:        Collecting Django==1.8.3 (from -r requirements.txt (line 1))
remote:          Downloading Django-1.8.3-py2.py3-none-any.whl (6.2MB)
remote:        Installing collected packages: Django
remote:        Successfully installed Django-1.8.3
remote: You are using pip version 7.0.3, however version 7.1.0 is available.
remote: You should consider upgrading via the 'pip install --upgrade pip' command.
remote:
remote: -----> Preparing static assets
remote:        Collectstatic configuration error. To debug, run:
remote:        $ heroku run python manage.py collectstatic --noinput
remote:
remote: -----> Discovering process types
remote:
remote: -----> Compressing... done, 39.9MB
remote: -----> Launching... done, v4
remote:        https://test-tykim.herokuapp.com/ deployed to Heroku
remote:
remote: Verifying deploy.... done.
To https://git.heroku.com/test-tykim.git
 * [new branch]      master -> master
```

* App 가동
인스턴스 수에 따라 비용이 추가 발생할 수 있음
```sh
$ heroku ps:scale web=1
```

* 웹사이트 열기
```sh
$ heroku opan
```

* 로그 보기
```sh
$ heroku logs
```

* 프로세스 상태 보기
```sh
$ heroku logs
=== web (Free): `gunicorn mysite.wsgi --log-file -`
web.1: idle 2015/07/21 12:48:01 (~ 1h ago)
```

* console 명령 실행
```sh
$ heroku run bash
```

* WSGI 에서 static 서비스 하기
 1. dj-static 설치
 2. setting.py 에 STATIC_ROOT = 'staticfiles' 추가
 3. wsgi.py 수정
```python
from dj_static import Cling
application = Cling(get_wsgi_application())
```

* urlpatterns 를 이용하여 서비스 하기 (추천되지 않음)
```python
from django.conf import settings
from django.conf.urls import include, url
from django.conf.urls.static import static
from django.contrib import admin

urlpatterns = [
    url(r'^admin/', include(admin.site.urls)),
]

urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
```