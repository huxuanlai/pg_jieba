# pg_jieba
[![Lang](https://img.shields.io/badge/Language-C%2FC%2B%2B-green.svg)]()
[![BSD](https://img.shields.io/badge/License-BSD-green.svg)]()
[![Extension](https://img.shields.io/badge/Extension-PostgreSQL-green.svg)]()

pg_jieba is a PostgreSQL extension for full-text search of Chinese.  
It implements by importing cppjieba.  

## NOTE
It should work with PostgreSQL 10  
It is tested with PostgreSQL 10 on Ubuntu 16.04.3 LTS

PREPARE
-------
Make sure PostgreSQL is installed and command `pg_config` could be runnable.   
  
Install Postgres:  

* Option:  
  download from http://www.postgresql.org/download/

* Option:  
  Ubuntu 16.04.3 LTS: 

  ```
  sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
  sudo apt-get install wget ca-certificates
  wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
  sudo apt-get update
  sudo apt-get upgrade
  sudo apt-get install -y postgresql-10
  dpkg -L postgresql-10
  echo "export PATH=/usr/lib/postgresql/10/bin:\$PATH" >> /etc/profile
  mkdir -p /opt/pg
  chown -R postgres:postgres /opt/pg
  su - postgres
  cd
  initdb -D /opt/pg/my_pg_data -E UTF8 --locale=C -U postgres
  pg_ctl -D /opt/pg/my_pg_data -l /opt/pg/my_pg_data/server.log start
  psql -h localhost -U postgres -d postgres
  ```

INSTALL
-------

1. **Downloads**

  ```
  git clone https://github.com/jaiminpan/pg_jieba
  ```

2. **Init submodule**
  ```
  cd pg_jieba

  # initilized sub-project
  git submodule update --init --recursive
  ```

3. **Compile**
  
  ```
  cd pg_jieba

  mkdir build
  cd build
  apt-get install -y postgresql-server-dev-10
  cmake -DPostgreSQL_TYPE_INCLUDE_DIR=/usr/include/postgresql/ ..
  vim CMakeFiles/pg_jieba.dir/flags.make
  为CXX_INCLUDES添加-I/usr/include/postgresql/10/server/

  # if postgresql is installed customized, Try cmd like following  
  # cmake -DCMAKE_PREFIX_PATH=/PATH/TO/PGSQL_INSTALL_DIR ..

  make
  make install 
  # if got error when doing "make install"
  # try "sudo make install"
  ```

HOW TO USE & EXAMPLE
-------

  ```
  jieba=# create extension pg_jieba;
  CREATE EXTENSION

  jieba=#  select * from to_tsvector('jiebacfg', '小明硕士毕业于中国科学院计算所，后在日本京都大学深造');
                                                   to_tsvector
  --------------------------------------------------------------------------------------------------------------
   '中国科学院':5 '于':4 '后':8 '在':9 '小明':1 '日本京都大学':10 '毕业':3 '深造':11 '硕士':2 '计算所':6 '，':7
  (1 row)

  jieba=#  select * from to_tsvector('jiebacfg', '李小福是创新办主任也是云计算方面的专家');
                                          to_tsvector
  -------------------------------------------------------------------------------------------
   '专家':11 '主任':5 '也':6 '云计算':8 '创新':3 '办':4 '方面':9 '是':2,7 '李小福':1 '的':10
  (1 row)
  ```

## USER DEFINED DICTIONARY
jieba.user.dict.utf8
  ```
  云计算
  韩玉鉴赏
  蓝翔 nz
  ```

operation
  ```
  cd /PATH/TO/POSTGRESQL_INSTALL/share/postgresql/tsearch_data
  OR
  cd /PATH/TO/POSTGRESQL_INSTALL/share/tsearch_data

  cp 'YOUR DICTIONARY' jieba.user.dict.utf8
  ```

## ONLINE TEST
You can test for result by following link (Suggest opened by Chrome)
http://cppjieba-webdemo.herokuapp.com/

## HISTORY
https://github.com/jaiminpan/pg_jieba/blob/master/HISTORY

## THANKS

jieba project by SunJunyi
https://github.com/fxsjy/jieba

cppjieba project by WuYanyi
https://github.com/yanyiwu/cppjieba
