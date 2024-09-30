---
title: 💉 SQL Injection & Command Injection
summary: Let's talk about SQL Injection & Command Injection! It's important
date: 2024-09-30
authors:
  - admin
tags:
  - Second Brain
  - Markdown
image:
  caption: 'Image credit: [**Unsplash**](https://unsplash.com)'
---

```jsx
/*
아래 쿼리 질의는 다음과 같은 의미를 가지고 있습니다.
- SELECT: 조회 명령어
- *: 테이블의 모든 컬럼 조회
- FROM accounts: accounts 테이블 에서 데이터를 조회할 것이라고 지정
- WHERE user_id='dreamhack' and user_pw='password': user_id 컬럼이 dreamhack이고, user_pw 컬럼이 password인 데이터로 범위 지정
즉, 이를 해석하면 DBMS에 저장된 accounts 테이블에서 이용자의 아이디가 dreamhack이고, 비밀번호가 password인 데이터를 조회
*/
SELECT * FROM accounts WHERE user_id='dreamhack' and user_pw='password'
```

```jsx
/*
아래 쿼리 질의는 다음과 같은 의미를 가지고 있습니다.
- SELECT: 조회 명령어
- *: 테이블의 모든 컬럼 조회
- FROM accounts: accounts 테이블 에서 데이터를 조회할 것이라고 지정
- WHERE user_id='admin': user_id 컬럼이 admin인 데이터로 범위 지정
즉, 이를 해석하면 DBMS에 저장된 accounts 테이블에서 이용자의 아이디가 admin인 데이터를 조회
*/
SELECT * FROM accounts WHERE user_id='admin'
```

### Blind SQL Injection

→ 위에서 SQL Injection을 통해 의도하지 않은 결과를 반환해 인증을 우회했다. 해당 공격은 인증 우회 외에 DB의 데이터를 알아낼 수 있다. 이때 사용할 수 있는 공격 기법으로는 Blind SQL Injection이 있다. 해당 공격은 스무고개 게임과 유사한 방식으로 데이터를 알아낼 수 있다.

- Question #1. dreamhack 계정의 비밀번호 첫 번째 글자는 'x' 인가요?
    - Answer. 아닙니다
- Question #2. dreamhack 계정의 비밀번호 첫 번째 글자는 'p' 인가요?
    - Answer. 맞습니다 (첫 번째 글자 = `p`)
- Question #3. dreamhack 계정의 비밀번호 두 번째 글자는 'y' 인가요?
    - Answer. 아닙니다.
- Question #4. dreamhack 계정의 비밀번호 두 번째 글자는 'a'인가요?
    - Answer. 맞습니다. (두 번째 글자 = `a`)

위와 같은 형태로 DBMS가 답변 가능한 형태로 질문하면서 계정의 비밀번호를 알아냄.

```sql
# 첫 번째 글자 구하기
SELECT * FROM user_table WHERE uid='admin' and substr(upw,1,1)='a'-- ' and upw=''; # False
SELECT * FROM user_table WHERE uid='admin' and substr(upw,1,1)='b'-- ' and upw=''; # True
# 두 번째 글자 구하기
SELECT * FROM user_table WHERE uid='admin' and substr(upw,2,1)='d'-- ' and upw=''; # False
SELECT * FROM user_table WHERE uid='admin' and substr(upw,2,1)='e'-- ' and upw=''; # True 
```

```sql
substr(string, position, length)
substr('ABCD', 1, 1) = 'A'
substr('ABCD', 2, 2) = 'BC'
```

```sql
SELECT * FROM users WHERE userid="{userid}" AND userpassword="{userpassword}";
```

```sql
/*
ID: admin"--, PW: DUMMY
userid 검색 조건만을 처리하도록, 뒤의 내용은 주석처리하는 방식
*/
SELECT * FROM users WHERE userid="admin"-- " AND userpassword="DUMMY"

/*
ID: admin" or "1 , PW: DUMMY
userid 검색 조건 뒤에 OR (또는) 조건을 추가하여 뒷 내용이 무엇이든, admin 이 반환되도록 하는 방식
*/
SELECT * FROM users WHERE userid="admin" or "1" AND userpassword="DUMMY"

/*
ID: admin, PW: DUMMY" or userid="admin
userid 검색 조건에 admin을 입력하고, userpassword 조건에 임의 값을 입력한 뒤 or 조건을 추가하여 userid가 admin인 것을 반환하도록 하는 방식
*/
SELECT * FROM users WHERE userid="admin" AND userpassword="DUMMY" or userid="admin"

/*
ID: " or 1 LIMIT 1,1-- , PW: DUMMY
userid 검색 조건 뒤에 or 1을 추가하여, 테이블의 모든 내용을 반환토록 하고 LIMIT 절을 이용해 두 번째 Row인 admin을 반환토록 하는 방식
*/
SELECT * FROM users WHERE userid="" or 1 LIMIT 1,1-- " AND userpassword="DUMMY"
```

## NoSQL

### MongoDB

→JSON 형태인 도큐먼트(Document)를 저장함.

- 스키마를 따로 정의하지 않아 컬렉션에 대한 정의가 필요하지 않다
- JSON 형식으로 쿼리를 작성할 수 있다.
- _id 필드가 Primary Key 역할을 한다

```sql
$ mongosh
> db.user.insertOne({uid: 'admin', upw: 'secretpassword'})
{ acknowledged: true, insertedId: ObjectId("5e71d395b050a2511caa827d")}
> db.user.find({uid: 'admin'})
[{ "_id" : ObjectId("5e71d395b050a2511caa827d"), "uid" : "admin", "upw" : "secretpassword" }]
```

### Redis

**Redis**는 키-값(Key-Value)의 쌍을 가진 데이터를 저장합니다. 제일 큰 특징은 다른 데이터베이스와 다르게 메모리 기반의 DBMS입니다. 메모리를 사용해 데이터를 저장하고 접근하기 때문에 읽고 쓰는 작업을 다른 DBMS보다 훨씬 빠르게 수행합니다. 따라서 다양한 서비스에서 임시 데이터를 캐싱하는 용도로 주로 사용하고 있습니다.

```sql
$ redis-cli
127.0.0.1:6379> SET test 1234 # SET key value
OK
127.0.0.1:6379> GET test # GET key
"1234"
```

**CouchDB**

**CouchDB** 또한 MongoDB와 같이 JSON 형태인 도큐먼트(Document)를 저장합니다. 이는 웹 기반의 DBMS로, REST API 형식으로 요청을 처리합니다.

- **인젝션(Injection)**: 악의적인 데이터를 프로그램에 입력하여 이를 시스템 명령어, 코드, 데이터베이스 쿼리 등으로 실행되게 하는 기법. 웹 애플리케이션을 대상으로 하는 인젝션 공격은 SQL Injection, command injection등이 있음.
- **커맨드 인젝션(Command Injection)**: 인젝션의 종류 중 하나. 시스템 명령어에 대한 인젝션을 의미함. 취약점이 발생하는 원인은 단순하지만, 매우 치명적인 공격으로 이어질 수 있음. 개발자는 이용자의 입력을 반드시 검사해야 하며, 되도록 `system` 관련 함수의 사용을 자제해야 함.
- **메타 문자(Meta Character)**: 특수한 의미를 가진 문자. 예를 들어, 셸 프로그램에서 `;`를 사용하면 여러 개의 명령어를 순서대로 실행시킬 수 있음.

```bash
$ echo `echo theori` -> `` 안에 들어있는 명령어를 실행
theori

$ echo $(echo theori) -> 위와 같이 명령어 치환
theori

$ echo hello && echo theori -> &&은 명령어 연속 실행
hello
theori

$ cat / || echo theori -> 명령어 연속 실행
cat: /: Is a directory
theori

$ echo hello ; echo theori -> 명령어 구분(+ 연속 실행 기능)
hello
theori

$ echo id | /bin/sh -> 파이프 : 앞 명령어 결과가 뒷 명령어의 입력으로 실행
uid=1001(theori) gid=1001(theori) groups=1001(theori)
```

### 실습

이 문제의 목표는 서버 내부에 있는 `flag.py` 파일 내의 flag를 획득하는 것입니다. 사이트에 접속해 보면 시스템 명령어를 통해 특정 Host에 ping 패킷을 보내는 기능을 제공하고 있음을 확인할 수 있습니다.

### 실습 링크 : https://dreamhack.io/wargame/challenges/44

| **기능명** | **설명** |
| --- | --- |
| `/ping` | 입력받은 특정 Host에 Ping 패킷 전송을 수행합니다. |

**아래 코드**는 `/ping` 페이지를 구성하는 코드입니다.

```python
@APP.route('/ping', methods=['GET', 'POST'])
def ping():
    if request.method == 'POST':
        host = request.form.get('host') # host라는 이름의 폼 데이터를 가져옴
        cmd = f'ping -c 3 "{host}"' # host 값을 사용하여 ping 명령 실행
        try:
            output = subprocess.check_output(['/bin/sh', '-c', cmd], timeout=5)
            # subprocess.check_output 함수로 /bin/sh에서 cmd 명령어 수행
            # 그 결과를 output에 저장
            return render_template('ping_result.html', data=output.decode('utf-8'))
            # 예외가 발생하지 않으면 결과 출력을 utf-8로 디코딩 후 ping_result.html 템플릿과 함께 랜더링하여 반
        except subprocess.TimeoutExpired:
            return render_template('ping_result.html', data='Timeout !')
        except subprocess.CalledProcessError:
            return render_template('ping_result.html', data=f'an error occurred while executing the command. -> {cmd}')

    return render_template('ping.html')
```
