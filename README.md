# hello-django-2020spring

first Django app
=============
https://docs.djangoproject.com/ko/3.0/intro/tutorial01/
Django 튜토리얼을 따라해보면서 Polls Application을 만들어 보자

Develop Polling Application
-사람들이 설문 내용을 보고 직접 투표할 수 있는 개방된 사이트
-관리자가 설문을 추가, 변경, 삭제할 수 있는 관리용 사이트

![Alt text](https://images.velog.io/images/kylie/post/16b389eb-6be9-42e5-9795-da4232a88a2e/django%20cycle.jpg)




## Django Cycle 이해하기 
client -> Web Server -> WSGI ->REQUEST ->urls.py에서 사용자가 요청한 특정 주소를 파싱 ->view 에서 역할에 맞는 기능 개발 (views.py->models.py->DB ->models.py->views.py)-> Template (.html) -> RESPONSE ->WSGI->Web Server ->client. 
---------------------------------------
# Django Tutorial Part 1 

파이썬 가상환경 만들기

$pip install django 
장고 설치

$python -m django --version 
장고 버전 확인

$django-admin startproject mysite 
현재 directory에서 mystie directory 생성

결과:
mysite/ : root directory
mysite/ manage.py 
mysite/ mysite/ :프로젝트를 위한 실제 python 패키지들
mysite/mysite/ __init__.py 
mysite/mysite/ settings.py 
mysite/mysite/ urls.py 
mysite/mysite/ wsgi.py 

mysite/로 이동  
$python manage.py runserver 
개발 서버 시작 
+)개발 서버에 운영환경 사용X 

### Project vs App
App: a web application that does something
project: contains mutiple apps 
장고는 app의 기본 디렉토리 구조를 자동 생성

$python manage.py startapp polls
root 디렉토리(mysite/)에 polls 디렉토리 생성
결과:
mysite/polls/
mysite/polls/migrations/__init__.py
mysite/polls/__init__.py
mysite/polls/admin.py
mysite/polls/apps.py 
mysite/polls/models.py
mysite/polls/tests.py
mysite/polls/views.py

### 첫 번째 뷰 작성하기
mysite/polls/urls.py 에서 뷰 호출 -> mysite/polls/views.py 에서 return HttpResponse

최상위URLconf에서 polls.urls 모듈 연결시키기
mysite/urls.py에 path('polls/',include('polls.urls')) 추가
+) 다른 url 패턴 포함할 때마다 include() 항상 사용 
include() 함수는 다른 URLconf들을 참조할 수 있도록 도와줍니다. Django가 함수 include()를 만나게 되면, URL의 그 시점까지 일치하는 부분을 잘라내고, 남은 문자열 부분을 후속 처리를 위해 include 된 URLconf로 전달합니다.

path(route,view , kwargs,name) 


---------------------------------------
# Django Tutorial Part 2 

### DB 설치 
mysite/settings.py 에 DB 설치
여기선 sqlite 사용 'ENGINE': 'django.db.backends.sqlite3', 추가 

$python manage.py migrate
mysite/settings.py의 DB 세팅하기 

### 모델 생성 
모델이란 부가적인 메타데이터를 가진 데이터베이스의 구조(layout)를 말함
polls/models.py 에 class Question ,class Choice 생성
CharField : 문자 필드 + max_length 필수
DateTimeField : 날짜와 시간 필드
필드의 인스턴스 이름:  machine-friendly format 

### 모델 활성화
mysite/settings.py의 INSTALLED_APPS에 'polls.apps.PollsConfig' 추가하기
이를 통해 Django polls앱이 포함된 것을 알게 된다.

$python manage.py makemigrations polls
makemigrations 을 실행시킴으로서, 당신이 모델을 변경시킨 사실과(이 경우에는 새로운 모델을 만듦) 이 변경사항을 migration으로 저장시키고 싶다는 것을 Django에게 알려준다.

$ python manage.py migrate
데이터베이스에 모델과 관련된 테이블 생성하기 
+)
models.py 에서모델을 변경
python manage.py makemigrations을 통해 이 변경사항에 대한 마이그레이션을 만드세요.
python manage.py migrate 명령을 통해 변경사항을 데이터베이스에 적용하세요.

### Django API 사용하기
$ python manage.py shell
Django에서 동작하는 모든 명령을 대화식 Python 쉘에서 그대로 시험

쉘에 진입한 후, database API 사용

객체표현위해 class에 __str__()메소드 추가 
```
class Question(models.Model):
    # ...
    def __str__(self):
        return self.question_text
```
        

### Django 관리자 생성하기
$ python manage.py createsuperuser
id,pwd생성

### 개발 서버 시작
$ python manage.py runserver
http://127.0.0.1:8000/admin/으로 접근하면 superuser 계정으로 로그인

##admin사이트에서 poll app 변경 가능하게 만들기
polls/admin.py에 admin.site.register(Question) 추가 choice도 같은 방식으로 추가

---------------------------------------
# Django Tutorial Part 3

### View
뷰는 Django 어플리케이션이 일반적으로 특정 기능과 템플릿을 제공하는 웹페이지의 한 종류이다.
polls/views.py 에 새로운 뷰 추가 ( detail, results, vote)
polls/urls.py에 path()호출하여 새로운 뷰를 polls.urls모듈로 연결

각 뷰는 두 가지 중 하나를 하도록 되어 있다. 요청된 페이지의 내용이 담긴 HttpResponse 객체를 반환하거나, 혹은 Http404 같은 예외를 발생하게 해야한다.

polls/templates/polls/index.html 만들고
polls/views.py에서 
1. return HttpResponse(template.render(context, request))
2. 지름길: shortcut render()사용하여 
	return render(request, 'polls/index.html', context)


### 404 error 
지름길 :get_object_or_404()

### html에 템플릿 시스템 사용하기 

## 템플릿에서 하드코딩된 URL 제거하기
polls/index.html에서
```<li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>```   
대신에   
```<li><a href="{% url 'polls:detail' question.id %}">{{ question.question_text }}</a></li>```   
&&   
polls/urls.py에 app_name = 'polls' 앱 이름 추가   
사용하기   

---------------------------------------
# Django Tutorial Part 4

### Write a minimal form
투표 상세 템플릿("polls/detail.html")을 수정하여, 템플릿에 HTML <form> 요소를 포함시킨다.
```{% csrf_token %}``` : 웹 보안 
forloop.counter : for 태그가 반복을 한 횟수
  

### polls/views.py에 vote()함수 추가
request.POST 는 키로 전송된 자료에 접근할 수 있도록 해주는 사전과 같은 객체이다. 이 경우, ```request.POST['choice'] ```는 선택된 설문의 ID를 문자열로 반환한다. request.POST 의 값은 항상 문자열들이다.

### polls/views.py에 results()함수 추가
어떤 이가 설문조사에 설문을 하고난 뒤에는, vote() 뷰는 설문조사 결과 페이지로 리다이렉트합니다. 그 뷰를 작성한다.
 
### polls/results.html 템플릿 만들기

### 제너릭 뷰 사용하기: 적은 코드가 더 좋다.
URLconf수정 polls/urls.py에서  <question_id> 가 <pk> 로
views 수정
