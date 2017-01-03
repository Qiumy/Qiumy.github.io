layout: post
title: CentOS7安装Openstack【Keystone篇】
comment: true
tags:
  - Openstack
  - Keystone
  - CentOS7
date: 2017-01-03 19:23:44
---


# 环境准备——MySQL的安装
MariaDB数据库管理系统是MySQL的一个分支，主要由开源社区在维护，采用GPL授权许可。开发这个分支的原因之一是：甲骨文公司收购了MySQL后，有将MySQL闭源的潜在风险，因此社区采用分支的方式来避开这个风险。MariaDB的目的是完全兼容MySQL，包括API和命令行，使之能轻松成为MySQL的代替品。

- 安装

```shell
# yum install mariadb-server mariadb
```

- 常用命令

```shell
systemctl start mariadb  #启动MariaDB
systemctl stop mariadb  #停止MariaDB
systemctl restart mariadb  #重启MariaDB
systemctl enable mariadb  #设置开机启动
```

- 使用

```shell
# mysql -u root -p
```

<!-- more -->

# Keystone安装
keystone是openstack中验证组件。[参考](http://docs.openstack.org/liberty/zh_CN/install-guide-rdo/common/get_started_identity.html)

- 创建数据库并授权

```shell
# mysql -u root -p
MariaDB[(none)] > CREATE DATABASE keystone;
MariaDB[(none)] > GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'localhost' IDENTIFIED BY 'KEYSTONE_DBPASS';
MariaDB[(none)] > GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'%' IDENTIFIED BY 'KEYSTONE_DBPASS';
MariaDB[(none)] > exit;
```

- 生成一个随机值作为管理员的令牌

```shell
$ openssl rand -hex 10
```

- 安装Keystone包及相关组件

```shell
# yum install openstack-keystone httpd mod_wsgi memcached python-memcached
```

- 启动Memcached服务，并且配置它随机启动

```shell
# systemctl enable memcached.service
# systemctl start memcached.service
```

- 编辑文件`\etc\keystone\keystone.conf`,做如下的修改
	- 在`[DEFAULT]`修改`ADMIN_TOKEN`为刚刚的随机数
	```bash
	[DEFAULT]
	···
	admin_token = ADMIN_TOKEN
	```
	- 在`[database]`配置数据库访问
	```
	[database]
	...
	connection = mysql://keystone:KEYSTONE_DBPASS@controller/keystone
	```
	- 在`[memcache]`配置Memcached服务
	```
	[memcache]
	...
	servers = localhost:11211
	```
	- 在`[token]`配置 UUID token provider和Memcached驱动
	```
	[token]
	...
	provider = uuid
	driver = memcache
	```
	- 在`[revoke]`部分，配置SQL 回滚驱动:
	```
	[revoke]
	...
	driver = sql
	```
- 初始化身份认证服务的数据库
```
# su -s /bin/sh -c "keystone-manage db_sync" keystone
```
- 重启Keystone
```
# systemctl keystone restart
```
- 配置`/etc/hosts`文件
```
ServerIP controller
```

# 创建服务实体和API端点
- 配置环境变量
```shell
$ export OS_TOKEN=ADMIN_TOKEN
$ export OS_URL=http://controller:35357/v3
$ export OS_IDENTITY_API_VERSION=3
```

- 创建admin租户、用户、角色
	- 创建admin租户
	```shell
	keystone tenant-create --name admin --description "Admin Tenant"
	```
	- 创建admin用户
	```
	keystone user-create --name admin --pass ADMIN_PASS --email EMAIL_ADDRESS
	```
	- 创建admin角色
	```
	keystone role-create --name admin
	```
	- 添加admin角色到admin用户和租户
	```shell
	keystone user-role-add --user admin --tenant admin --role admin
	```
- 创建Demo租户、用户、角色
	- 创建demo租户
	```shell
	keystone tenant-create --name demo --description "Demo Tenant"
	```
	- 创建demo租户下的用户
	```shell
	keystone user-create --name demo --tenant demo --pass DEMO_PASS --email EMAIL_ADDRESS
	```
	PS：至此demod用户完毕注意：在创建demo tenant的同时，demo  user,demo role会自动创建
- 创建service租户
```
keystone tenant-create --name service --description "Service Tenant"
```
- 创建   service entity and API endpoints
```
keystone service-create --name keystone --type identity --description "OpenStack Identity"
```
- 创建API endpoints
```shell
keystone endpoint-create \
  --service-id $(keystone service-list | awk '/ identity / {print $2}') \
  --publicurl http://controller:5000/v2.0 \
  --internalurl http://controller:5000/v2.0 \
  --adminurl http://controller:35357/v2.0 \
  --region regionOne
```
- 验证操作
	- admin租户获取token
	```
	keystone --os-tenant-name admin --os-username admin --os-password ADMIN_PASS \
	--os-auth-url http://controller:35357/v2.0 token-get
	```
	- 列出租户(tenant)
	```
	keystone --os-tenant-name admin --os-username admin --os-password ADMIN_PASS \
	--os-auth-url http://controller:35357/v2.0 tenant-list
	```
	- 列出用户（user）
	```
	keystone --os-tenant-name admin --os-username admin --os-password ADMIN_PASS \
	--os-auth-url http://controller:35357/v2.0 user-list
	```
	- 列出角色（role）
	```
	keystone --os-tenant-name admin --os-username admin --os-password ADMIN_PASS \
	--os-auth-url http://controller:35357/v2.0 role-list
	```
	- demo 租户获取令牌token
	```
	keystone --os-tenant-name demo --os-username demo --os-password DEMO_PASS \
	--os-auth-url http://controller:35357/v2.0 token-get
	```
	- demo用户无权查看user，会报错误如下：
	```
	keystone --os-tenant-name demo --os-username demo --os-password DEMO_PASS \
	--os-auth-url http://controller:35357/v2.0 user-list
	You are not authorized to perform the requested action: admin_required (HTTP 403)
	```

# 创建 OpenStack 客户端环境脚本
- 创建admin环境变量, 编辑`admin-openrc.sh`
```bash
export OS_TENANT_NAME=admin
export OS_USERNAME=admin
export OS_PASSWORD=ADMIN_PASS
export OS_AUTH_URL=http://controller:35357/v2.0
```
- 创建demo环境变量, 编辑`demo-openrc.sh`
```bash
export OS_TENANT_NAME=demo
export OS_USERNAME=demo
export OS_PASSWORD=DEMO_PASS
export OS_AUTH_URL=http://controller:5000/v2.0
```
- 脚本的使用
```
source admin-openrc.sh
source demo-openrc.sh
```
