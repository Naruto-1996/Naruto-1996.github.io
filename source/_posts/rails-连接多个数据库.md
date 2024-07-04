---
title: rails 连接多个数据库
date: 2020-12-01 17:08:53
categories:
- rails
tags:
- mysql
- rails
---

#### Rails项目中如何连接多个数据库

##### 1、修改config/database.yml文件

```ruby
default: &default
  adapter: mysql2
  encoding: utf8
  collation: utf8_general_ci
  pool: 5
  host: localhost
  username: root
  password: 123456

#原数据库
development:
  <<: *default
  database: cadae

#你想要连接的另一个数据库
development_main_database:
  <<: *default
  database: cadae_copy

test:
  <<: *default
  database: cadae_test

production:
  <<: *default
  url: 'mysql2://root:666666@localhost:3306/coiex?pool=10&timeout=3000'
  pool: 10
  database: cadae

```

##### 2、新建一个连接类继承 ActiveRecord::Base 

* 比如放在app/models/ 目录下 就叫做 database_connection.rb

```ruby
class DatabaseConnection < ActiveRecord::Base
  #共用连接池，减少数据库连接的消耗
  self.abstract_class = true
  # 这里根据rails运行环境不同自动获取运行环境 转换为symbol类型   
  establish_connection("#{Rails.env}_main_database".to_sym)
  #或者使用
  #establish_connection(:development_main_database)
end

```

* 这里有个小坑 在创建database_connection.rb文件的时候 最好不要创建一个文件夹然后把它放进去 这样可能会找不到这个文件

##### 3、在需要连接另一个数据库的model中继承咱们写的这个类就行了

```ruby
class AccountVerificationResult < DatabaseConnection

end
```

