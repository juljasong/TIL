# Flask
- [docs](https://flask.palletsprojects.com/en/3.0.x/quickstart/)
- Micro Web Framework 
    1. API Server를 가볍게 구현 가능
    2. Docker나 Kubernetes를 이용해 여러 개의 컨테이너를 이용하여 스케쥴링이 용이
    3. **쉽고, 짧은 코드.**

## Web Framework
- Java : JSP, Servlet, Spring
- php : Laravel, Codeignitor
- ruby : Ruby on rails
- node : Express.js
- python : Django, FastAPI

## Setting
1. Python 설치
   - https://www.python.org/downloads/
   - Python 설치 확인 :  ```$ pip ```
2. Flask 설치
   - ```$ pip install flask```
4. server.py 작성
    ```python
    from flask import Flask

    app = Flask(__name__)


    @app.route('/')
    def index():
        return 'hi'


    app.run #(port=5001, debug=True) : 포트 지정 가능, 디버그 모드
    ```
4. ```http://127.0.0.1:5000/``` 접속 가능
5. 온라인으로도 가능 : Glitch 등의 서비스

## routing
- 사용자의 요청을 처리 할 응답 함수를 연결하는 작업
```python
    @app.route('/user/<username>')
    def show_user_profile(username):
        # show the user profile for that user
        return f'User {escape(username)}'

    @app.route('/post/<int:post_id>')
    def show_post(post_id):
        # show the post with the given id, the id is an integer
        return f'Post {post_id}'

    @app.route('/path/<path:subpath>')
    def show_subpath(subpath):
        # show the subpath after /path/
        return f'Subpath {escape(subpath)}'
```

## Flask-restx
- 참고
  - [docs](https://flask-restx.readthedocs.io/en/latest/quickstart.html)
  - [[Flask] Flask-RESTx](https://velog.io/@2jinu/Flask-Flask-RESTx)
- REST API를 쉽게 구축할 수 있도록 도와주는 패키지
- swagger 제공 : 기본 url
- namespace : 관련된 엔드포인트를 그룹화하고 구조화하여 API 코드를 관리
- 설치
   - ```$ pip install flask-restx```

## pymysql
- 설치
  - ```$ pip install pymysql```
```python

from pymysql import Pymysql

db = Pymysql.connect(host='127.0.0.1', user='root', password='root', db='mungstore', charset='utf8')

```
- 사용법
  1. pymysql 모듈 import 
  2. MySQL db 연결: pymysql.connect() 메서드로 Connection 객체 생성
  3. Connection 객체로부터 cursor() 메서드로 Cursor 객체 생성
  4. Cursor 객체의 execute() 메서드로 sql 쿼리를 mysql db에 전송
  5. SELECT 쿼리의 경우 fetchall(), fetchone(), fetchmany() 메서드를 사용하여 데이터를 가져옴
     - pd.read_sql() 을 사용하면 데이터를 바로 DataFrame으로 불러올 수 있음
  6. INSERT/UPDATE/DELETE DML(Data Manipulation Language) 쿼리의 경우 Connection 개체의 commit() 메서드를 사용하여 데이터 확정
  7. 종료: Connection 객체의 close() 메서드를 사용해 db 연결을 종료


## sqlalchemy
- 설치
  - ```$ pip install sqlalchemy```
- 사용법
  1. sqlalchemy create_engine(sql db 연결 라이브러리)을 import
  2. MySQL db 연결: create_engine() 메서드로 engine 객체 생성. connect() 메서드로 db 연결
  3. DataFrame → MySQL 내보내기: df.to_sql() 메서드 사용
  4. 종료: close() 메서드로 db 연결 종료