---
layout: post
title: "A Basic Flask + HAProxy + MySQL integration"
categories: [programming]
date: 2021-03-22
---

![Architecture Diagram](https://cdn-images-1.medium.com/max/800/0*u4KUBkRkX0BHVYEd.png)

Documenting how to spin up a quick PoC of integrating a Flask app with MySQL through HAProxy. Our flask app will run on a gunicorn server locally.

## Environment:

OS: Ubuntu 20.04.2 LTS

Python/Flask versions: Python 2.7.17, Flask 1.1.2, Werkzeug 1.0.1

Gunicorn version: gunicorn (version 19.6.0)

HAProxy version: HA-Proxy version 1.8.8–1ubuntu0.11 2020/06/22

### Key Gotchas here before we start

1. Connections are made in TCP so we use HAProxy here in layer 4 mode.

2. Our Flask app uses SQLAlchemy to make connections, and engine would be pymysql.

3. HAProxy doesn't have context of understanding 'mysql traffic' so it acts as a TCP forwarder/load balancer and there is no connection pooling at proxy level.

## Step 1 — Setup mysql-server

Install MySQL server and start service:

```sql
sudo apt install mysql-server
sudo service mysql start
```

Get inside the MySQL shell:

```bash
sudo mysql
```

Create required test user and grant perms:

> CREATE USER 'haproxy_user'@'localhost' IDENTIFIED BY 'tester';
> GRANT ALL PRIVILEGES ON * . * TO 'haproxy_user'@'localhost';
> FLUSH PRIVILEGES;

Create test db and table:

```sql
create database test;
create table test.todos (id int, item varchar(255), status varchar(255));
insert into test.todos values (1, "task1", "pending");
insert into test.todos values (2, "task2", "done");
```

Confirm that you're able to login to mysql with the test user:

```bash
mysql -u haproxy_user -ptester
```

MySQL will be running on port 3306 on your localhost now.

## Step 2 — Setup HAProxy

Update the haproxy.cfg file present in /etc/haproxy/ as follows:

```bash
global
        log /dev/log    local0
        log /dev/log    local1 notice
        chroot /var/lib/haproxy
        stats socket /run/haproxy/admin.sock mode 660 level admin expose-fd listeners
        stats timeout 30s
        user haproxy
        group haproxy
        daemon
        maxconn 100
        # Default SSL material locations
        ca-base /etc/ssl/certs
        crt-base /etc/ssl/private
        # Default ciphers to use on SSL-enabled listening sockets.
        # For more information, see ciphers(1SSL). This list is from:
        #  https://hynek.me/articles/hardening-your-web-servers-ssl-ciphers/
        # An alternative list with additional directives can be obtained from
        #  https://mozilla.github.io/server-side-tls/ssl-config-generator/?server=haproxy
        ssl-default-bind-ciphers ECDH+AESGCM:DH+AESGCM:ECDH+AES256:DH+AES256:ECDH+AES128:DH+AES:RSA+AESGCM:RSA+AES:!aNULL:!MD5:!DSS
        ssl-default-bind-options no-sslv3
defaults
        log     global
        timeout connect 5s
        timeout client  300s
        timeout server  300s
        errorfile 400 /etc/haproxy/errors/400.http
        errorfile 403 /etc/haproxy/errors/403.http
        errorfile 408 /etc/haproxy/errors/408.http
        errorfile 500 /etc/haproxy/errors/500.http
        errorfile 502 /etc/haproxy/errors/502.http
        errorfile 503 /etc/haproxy/errors/503.http
        errorfile 504 /etc/haproxy/errors/504.http
listen msyql
        bind 127.0.0.1:5555
        mode tcp
        option srvtcpka
        balance roundrobin
        server mysql1 127.0.0.1:3306 maxconn 10
```

The above includes some default values and in particular the mysql binding that we've created for our connections.

Start the haproxy service

```bash
sudo service haproxy start
```

## Step 3 — Setup flask app

Create a file **app.py**

```python
from flask import Flask
from sqlalchemy import create_engine

app = Flask(__name__)
db_uri = 'mysql+pymysql://haproxy_user:tester@localhost:5555'
engine = create_engine(db_uri, pool_size=1, pool_recycle=-1)
query = 'select * from test.todos'

@app.route("/")
def main():
  return "Welcome!\n"

@app.route("/todos")
def list_todos():
  print(engine.pool.__dict__)
  with engine.connect() as conn:
    db_res = conn.execute(query)
    res = {}
    for row in db_res:
      res[row[0]] = {'item': row[1], 'status': row[2]}
  return res

@app.route("/sleep/<s>")
def slow_query(s):
  with engine.connect() as conn:
    db_res = conn.execute('SELECT SLEEP({})'.format(s))
  return {'slept':s}

if __name__ == '__main__':
  app.run()
```

This App exposes a few endpoints -

/sleep/<s> will run a query which sleeps for s seconds

/todos will execute a query to select all todos from the test db and return the response

Next we need to run this app over couple of instances of gunicorn

```bash
gunicorn -w 1 -k gevent app:app -b 127.0.0.1:8000 &
gunicorn -w 1 -k gevent app:app -b 127.0.0.1:8001 &
```

These are our app servers, now to execute a query from either of these, just do a curl

```bash
curl localhost:8000/sleep/10 &
```

This will execute a query that sleeps in MySQL for 10 seconds. Meanwhile you can go to mysql shell and check the running queries -

```sql
SHOW PROCESSLIST;
```

And MySQL will show you one connection made for the above curl.
