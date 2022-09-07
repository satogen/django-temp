## Django　＋　Docker　初期設定
## 手順
### DockerFileの作成
```
FROM python:3.9-buster 
ENV PYTHONUNBUFFERED 1
RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt
ADD . /code/
```

※FROM python:3　にするとpython3.10がインストールされ、Djangoの一部ライブラリがインポートできないエラーが生じるので注意

### requirements.txtの作成
```
Django>=1.8,<2.0
psycopg2
```
必要なモジュールを追加していく

新たに追加したモジュールを反映させたい場合
```bash
docker-compose run web pip install -r requirements.txt
```
### docker-compose.ymlの作成
```
version: '3'
services:

  # データベース
  db:
    image: postgres
    environment:
      - POSTGRES_DB=postgres
      - POSTGRES_USER=root
      - POSTGRES_PASSWORD=password

  web:
    build: .
    command: python3 manage.py runserver 0.0.0.0:8000
    volumes:
      - .:/code
    ports:
      - "8000:8000"
    depends_on:
      - db
```

### Djangoプロジェクトの作成
```
docker-compose run web django-admin.py startproject composeexample .
```
composeexample は任意の名前で問題ない

### DB設定
DBをデフォルトから変更
```./composeexample/settings.py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'postgres',
        'USER': 'root',
        'PASSWORD': 'password',
        'HOST': 'db',
        'PORT': 5432,
    }
}
```
### サーバ起動

```
docker-compose up
```


### 参考サイト

- クィックスタート: Compose と Django[https://docs.docker.jp/compose/django.html]

