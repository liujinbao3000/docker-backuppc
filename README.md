
1. 部署
   ```
   docker run -itd \
       --name backuppc \
       --restart unless-stopped \
       --publish 8080:8080 \
       --volume /var/local/docker/backuppc/etc/backuppc:/etc/backuppc \
       --volume /var/local/docker/backuppc/home/backuppc:/home/backuppc \
       --volume //var/local/docker/backuppc/data/backuppc:/data/backuppc \
       --volume /var/local/docker/backuppc/log:/data/backuppc/log \
       --env TZ=Asia/Shanghai \
       --mount /etc/localtime:/etc/localtime:ro \
       --env 'USE_SSL=true'
       liujinbao3000/backuppc
   ```
1. 部署选项
   选项|说明
   -|-
   -env TZ=Asia/Shanghai|设置时区
   --env 'USE_SSL=true'|允许https(使用自签名证书)

2. 映射卷说明
   映射卷|说明
   -|-
   /etc/backuppc|存储BackupPC配置，尤其是config.pl和主机配置
   /home/backuppc|backuppc用户的主目录，运行您的BackucPC实例，并且特别包含一个.ssh目录，该目录包含用于通过SSH协议进行备份的SSH密钥
   /data/backuppc|包含BackupPC池，因此您需要备份自己以及日志
3. 访问
   1. http://IP:8080
   2. 用户名：backuppc 密码：password
4. 常用操作
   1. 改中文
      Edit Config -- CGI -- Language -- zh_CN -- Save -- 刷新浏览器
   2. 增加客户机
      增删客户机--添加--host(能在服务器上ping通的域名或netbios名)；user：backuppc--添加--保存
   3. 客户机增加备份
      选择客户机名--客户机名--修改服务器配置--传输--传输设置：XferMethod 以rsyncd为例--Rsyncd设置：RsyncShareName(rsync共享名,可插入多个);RsyncdUserName(rsyncd的用户名);RsyncdPasswd(rsync的密码)--保存
   4. 客户机手动备份
      客户机主页--开始完全备份--开始完全备份--服务器--状态;查看备份状态
      客户机主页--浏览备份
5. 注意事项
   1. 默认ping值超过20被视为不在线，不进行备份，默认值在`修改服务器设置--备份设置--客户机查找：PingMaxMsec`
   2. rsync方式备份：进入容器内部，切换成backuppc用户，使用`ssh-copy-id`上传公钥到客户机的`root`用户内，测试
   3. rsync方式备份：只能选目录，不知道怎么选文件
   4. 增加客户机时: 在容器内要能ping通客户机的名称,修改`/etc/hosts/`文件的话,重启容器会还原,这时可以在创建容器时使用`--add-ip test.pc:1.1.1.1`.test.pc为主机名;1.1.1.1为IP地址.