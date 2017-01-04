layout: post
title: CentOS7安装Openstack【Swift篇】
comment: true
tags:
  - Openstack
  - Swift
  - CentOS7
date: 2017-01-04 20:58:09
---


本文主要关注与OpenStack对象存储的安装
# 控制节点
- ## 基础配置
	- 关闭selinux、防火墙
	- 设置时间同步
	- 配置swift文件
	```shell
	# vi /etc/swift/swift.conf
	[swift-hash]
	swift_hash_path_suffix = `od -t x8 -N 8 -A n </dev/random`
	```
	- 配置日志
		- 创建swift日志配置文件
		```shell
		# vi /etc/rsyslog.d/10-swift.conf
		local0.* /var/log/swift/proxy.log
		```
		- 创建日志目录
		```shell
		# mkdir -p /var/log/swift
		# chown -R swift:swift /var/log/swift
		```
		- 重启日志服务
		```shell
		service rsyslog restart
		```

<!-- more -->

- ## 创建身份认证服务
	- 生效环境变量`admin-openrc.sh` 参见[CentOS7安装Openstack【Keystone篇】](https://qiumy.github.io/2017/01/03/openstack-keystone/)
	- 创建swift用户
	```shell
	keystone user-create --name swift --pass SWIFT_PASS
	```
	- 授予admin角色
	```shell
	keystone user-role-add --user swift --tenant service --role admin
	```
	- 创建swift 服务实例
	```shell
	keystone service-create --name swift --type object-store \
	--description "OpenStack Object Storage"
	```
	- 创建对象服务API endpoints
	```shell
	keystone endpoint-create \
	--service-id $(keystone service-list | awk '/ object-store / {print $2}') \
	--publicurl 'http://controller:8080/v1/AUTH_%(tenant_id)s' \
	--internalurl 'http://controller:8080/v1/AUTH_%(tenant_id)s' \
	--adminurl http://controller:8080 \
	--region regionOne
	```
- ## 安装配置控制节点组件
	- 安装
	```shell
	yum install swift swift-proxy python-swiftclient python-keystoneclient python-keystonemiddleware memcached
	```
	- 创建/etc/swift 目录
	```shell
	mkdir /etc/swift
	```
	- 复制获取代理服务配置文件sample，创建`\etc\swift\proxy-server.conf` [proxy-server.conf-sample](https://github.com/openstack/swift/blob/master/etc/proxy-server.conf-sample)
		- `[DEFAULT]`部分，配置绑定的端口，用户和配置目录
		```shell
		[DEFAULT]
		...
		bind_port = 8080
		user = swift
		swift_dir = /etc/swift
		```
		- `[pipeline:main]`部分，启动合适的modules
		```shell
		[pipeline:main]
		pipeline = authtoken cache healthcheck keystoneauth proxy-logging proxy-server
		```
		- `[app:proxy-server]`部分，启动账户管理
		```shell
		[app:proxy-server]
		...
		allow_account_management = true
		account_autocreate = true
		```
		- `[filter:keystoneauth]`部分，配置操作的角色
		```shell
		[filter:keystoneauth]
		use = egg:swift#keystoneauth
		...
		operator_roles = admin,_member_
		```
		- `[filter:authtoken]`部分，配置身份认证访问
		```shell
		[filter:authtoken]
		paste.filter_factory = keystonemiddleware.auth_token:filter_factory
		...
		auth_uri = http://controller:5000/v2.0
		identity_uri = http://controller:35357
		admin_tenant_name = service
		admin_user = swift
		admin_password = SWIFT_PASS
		delay_auth_decision = true
		```
		- `[filter:cache]`部分，配置memcached 位置
		```shell
		[filter:cache]
		...
		memcache_servers = 127.0.0.1:11211
		```
- ## 配置Ring
	- 进入`/etc/swift`目录
	- 创建Ring

	```shell
	# swift-ring-builder account.builder create 10 3 1
	# swift-ring-builder container.builder create 10 3 1
	# swift-ring-builder object.builder create 10 3 1
	```

	- 向环里添加存储设备
	```shell
	# swift-ring-builder account.builder add r1z1-172.18.216.188:6002/sdb1 100
	# swift-ring-builder account.builder add r1z1-172.18.216.33:6002/sdb1 100
	# swift-ring-builder account.builder add r1z2-172.18.216.38:6002/sdb1 200
	# swift-ring-builder container.builder add r1z1-172.18.216.188:6001/sdb1 100
	# swift-ring-builder container.builder add r1z1-172.18.216.33:6001/sdb1 100
	# swift-ring-builder container.builder add r1z2-172.18.216.38:6001/sdb1 200
	# swift-ring-builder object.builder add r1z1-172.18.216.188:6000/sdb1 100
	# swift-ring-builder object.builder add r1z1-172.18.216.33:6000/sdb1 100
	# swift-ring-builder object.builder add r1z2-172.18.216.38:6000/sdb1 200
	```

	- 平衡环
	```shell
	# swift-ring-builder account.builder rebalance
	# swift-ring-builder container.builder rebalance
	# swift-ring-builder object.builder rebalance
	```
	PS:这三条命令会产生以gz结尾的文件,gz文件是所有节点要用到的环文件，可以用scp拷贝到其它各节点
- ## 启动服务
	- 创建cache目录，设置权限
	```shell
	# mkdir -p /var/cache/swift
	# chown -R swift:swift /var/cache/swift/
	```
	- 设置/etc/swift目录权限
	```shell
	# chown -R swift:swift /etc/swift
	```
	- 启动服务
	```shell
	# service memcached start
	# chkconfig memcached on
	# swift-init proxy start
	```

# 存储节点
- ## 基础配置
	- 关闭selinux、防火墙
	- 设置时间同步
- ## 安装配置存储节点组件
	- 安装
	```shell
	# yum install –y python-setuptools xinetd xfsprogs
	# yum install -y openstack-swift-account openstack-swift-container openstack-swift-object rsync
	```
	- 配置存储
		- 创建/dev/sdb1分区，过程省略
		- 格式化磁盘为xfs格式
		```shell
		# mkfs.xfs -i size=1024 /dev/sdb1
		```
		- 添加开机自启动项
		```shell
		# echo "/dev/sdb1 /swift/node1 xfs loop,noatime,nodiratime,nobarrier,logbufs=8 0 0" >> /etc/fstab
		```
		- 创建挂载目录
		```shell
		# mkdir -p /swift/node1
		```
		- 挂载磁盘
		```shell
		# mount /swift/node1
		```
		- 设置目录权限
		```shell
		# chown -R swift:swift /swift
		```
	- 配置`swift.conf`文件, 从代理节点拷贝过来即可，使用scp命令
	- 配置rsync,创建`/etc/rsync.conf`文件
	```shell
	# vi /etc/rsync.conf
	uid = swift
	gid = swift
	log file = /var/log/rsyncd.log
	pid file = /var/run/rsyncd.pid
	address = 127.0.0.1
	[account]
	max connections = 2
	path = /swift/node1
	read only = false
	lock file = /var/lock/account.lock
	[container]
	max connections = 2
	path = /swift/node1
	read only = false
	lock file = /var/lock/container.lock
	[object]
	max connections = 2
	path = /swift/node1
	read only = false
	lock file = /var/lock/object.lock
	```
	启用rsync
	```shell
	# sed -i 's/yes/no/' /etc/xinetd.d/rsync
	# sed -i 's/IPv6/IPv4/' /etc/xinetd.d/rsync
	```
	启动xinetd
	```shell
	# service xinetd start
	# chkconfig xinetd on
	```
- ## 配置存储服务
	- 配置account服务
	```shell
	[DEFAULT]
	bind_ip = 172.18.216.188
	bind_port = 6002
	user = swift
	swift_dir = /etc/swift
	devices = /swift/node1
	mount_check = false
	[pipeline:main]
	pipeline = healthcheck recon account-server
	[app:account-server]
	use = egg:swift#account
	#recon_cache_spath = /var/cache/swift
	[account-replicator]
	vm_test_node=no
	[account-auditor]
	[account-reaper]
	[filter:recon]
	use = egg:swift#recon
	recon_cache_path = /var/cache/swift
	[filter:healthcheck]
	use = egg:swift#healthcheck
	```
	- 配置container服务
	```shell
	[DEFAULT]
	bind_ip = 172.18.216.188
	bind_port = 6001
	user = swift
	swift_dir = /etc/swift
	devices = /swift/node1
	mount_check = false
	[pipeline:main]
	pipeline = healthcheck recon container-server
	[app:container-server]
	use = egg:swift#container
	[container-replicator]
	vm_test_mode=no
	[container-updater]
	[container-auditor]
	[container-sync]
	[filter:recon]
	use = egg:swift#recon
	recon_cache_path = /var/cache/swift
	[filter:healthcheck]
	use = egg:swift#healthcheck
	```
	- 配置object服务
	```shell
	[DEFAULT]
	bind_ip = 172.18.216.188
	bind_port = 6000
	user = swift
	swift_dir = /etc/swift
	devices = /swift/node1
	mount_check = false
	[pipeline:main]
	pipeline = healthcheck recon object-server
	[app:object-server]
	use = egg:swift#object
	[object-replicator]
	vm_test_mode=no
	[object-updater]
	[object-auditor]
	[object-reconstructor]
	[filter:recon]
	use = egg:swift#recon
	recon_cache_path = /var/cache/swift
	recon_lock_path = /var/lock
	[filter:healthcheck]
	use = egg:swift#healthcheck
	```
- ## 拷贝代理节点生成的环文件 scp
- ## 配置日志
	- 创建swift日志配置文件
	```shell
	# vi /etc/rsyslog.d/10-swift.conf
	local0.* /var/log/swift/proxy.log
	```
	- 创建日志目录
	```shell
	# mkdir -p /var/log/swift
	# chown -R swift:swift /var/log/swift
	```
	- 重启日志服务
	```shell
	service rsyslog restart
	```
- ## 启动服务
	- 创建cache目录，设置权限
	```shell
	# mkdir -p /var/cache/swift
	# chown -R swift:swift /var/cache/swift/
	```
	- 设置/etc/swift目录权限
	```shell
	# chown -R swift:swift /etc/swift
	```
	- 启动服务
	```shell
	swift-init all start
	```
# 安装验证
PS:注意先source环境变量
- 在客户端查看swift验证
```shell
swift stat
```
- 在客户端上传文件到swift中
```shell
swift upload container file
```

## 参考资料
- [OpenStack-Swift GitHub仓库](https://github.com/openstack/swift)
- [OpenStack【juno】入门系列教程](http://www.aboutyun.com/thread-11722-1-1.html)
- [OpenStack官方文档 中文版](http://docs.openstack.org/liberty/zh_CN/install-guide-rdo/swift.html)