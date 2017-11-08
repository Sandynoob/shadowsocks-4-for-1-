shadowsocks-4-for-1-
sudo -i获取账户root权限
依次输入
wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/Sandynoob/shadowsocks-4-for-1-/master/shadowsocks-all.sh
chmod +x shadowsocks-all.sh
./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log
卸载
./shadowsocks-all.sh uninstall

各版本默认配置文件（显示和编辑配置文件代码 vi   然后可多用户配置）
Shadowsocks-Python 版：
/etc/shadowsocks-python/config.json

ShadowsocksR 版：
/etc/shadowsocks-r/config.json

Shadowsocks-Go 版：
/etc/shadowsocks-go/config.json

Shadowsocks-libev 版：
/etc/shadowsocks-libev/config.json
多用户配置示例：

{
"server":"0.0.0.0",
"server_ipv6": "[::]",
"local_address":"127.0.0.1",
"local_port":1080,
"port_password":{
    "8989":"password1",
    "8990":"password2",
    "8991":"password3"
},
"timeout":300,
"method":"aes-256-cfb",
"protocol": "origin",
"protocol_param": "",
"obfs": "plain",
"obfs_param": "",
"redirect": "",
"dns_ipv6": false,
"fast_open": false,
"workers": 1
}
启动脚本后面的参数含义，从左至右依次为：启动，停止，重启，查看状态。

Shadowsocks-Python 版：
/etc/init.d/shadowsocks-python start | stop | restart | status

ShadowsocksR 版：
/etc/init.d/shadowsocks-r start | stop | restart | status

Shadowsocks-Go 版：
/etc/init.d/shadowsocks-go start | stop | restart | status

Shadowsocks-libev 版：
/etc/init.d/shadowsocks-libev start | stop | restart | status
Shadowsocks查看服务器上面的 用户连接数 方法
首先，我们是使用netstat命令来查询当前服务器（VPS）的连接数据。

以下方法适用于 ShadowsocksR(Python) 的服务端，因为ShadowsocksR服务端由 Python 编写，并且默认开启ipv6，所以很容易过滤出一些信息。
显示出由Python建立完成的TCP链接列表或连接数

# 显示所有进出链接
netstat -anp |grep 'ESTABLISHED' |grep 'python'
# 仅显示链接服务器的用户连接
netstat -anp |grep 'ESTABLISHED' |grep 'python' |grep 'tcp6'
# 仅显示链接服务器的用户连接数量
netstat -anp |grep 'ESTABLISHED' |grep 'python' |grep 'tcp6' |wc -l
# 仅显示链接服务器的用户连接并写入到文件
netstat -anp |grep 'ESTABLISHED' |grep 'python' |grep 'tcp6'>>/root/log.txt
 
# 如果你是多用户版(多个端口)的服务端，那么你可以用这个命令
# 显示当前链接服务器的用户的SS端口
netstat -anp |grep 'ESTABLISHED' |grep 'python' |grep 'tcp6' |awk '{print $4}' |sort -u
# CentOS6系统用这个，CentOS7用上面那个。
netstat -anp |grep 'ESTABLISHED' |grep 'python' |grep 'tcp' | grep '::ffff:' |awk '{print $4}' |sort -u
# 显示当前链接服务器的用户的SS端口数量
netstat -anp |grep 'ESTABLISHED' |grep 'python' |grep 'tcp6' |awk '{print $4}' |sort -u |wc -l
# CentOS6系统用这个，CentOS7用上面那个。
netstat -anp |grep 'ESTABLISHED' |grep 'python' |grep 'tcp' | grep '::ffff:' |awk '{print $4}' |sort -u |wc -l
 
# 显示当前所有链接SS的用户IP
netstat -anp |grep 'ESTABLISHED' |grep 'python' |grep 'tcp6' |awk '{print $5}' |awk -F ":" '{print $1}' |sort -u
# 显示当前所有链接SS的用户IP数量
netstat -anp |grep 'ESTABLISHED' |grep 'python' |grep 'tcp6' |awk '{print $5}' |awk -F ":" '{print $1}' |sort -u |wc -l
第一条 命令是 把所有的进出链接都列出来，Shadowsocks客户端在链接服务器后 本地的IP和端口 是 “进” 的，服务器在接收到Shadowsocks客户端的数据后会去访问指定的网站或IP，而这是 “出”的。这一条命令就是把这 一进一出 的信息都列出来，可以知道 客户端用户连接的是哪个 SS端口，还有客户 自身的IP和端口。

第二条 命令是 仅显示链接服务器的用户的链接，也就是上面一段话中说的 “进”，这个命令不会吧服务器在访问的网站或IP给列出来，纯粹用来看 Shadowsocks用户的链接信息。

第三条 命令是 仅显示链接服务器的用户连接数量，有时候链接 当前服务器的 Shadowsocks用户很多，你只想知道有几个人链接，那就用这一条命令（这个链接数量不是准确的，具体看下面的注意说明）。

第四条 命令是 仅显示链接服务器的用户连接并写入到文件，有时候在ssh上面查看大量文本不是一个好主意，所以保存到某个目录，然后自己去用sftp下载下来看信息是个不错的选择。

第五条 命令是 显示当前链接服务器的用户的SS端口，这个命令会去除重复的，最后显示的只有 正在链接SS服务器的用户的SS端口。

第六条 命令是 显示当前链接服务器的用户的SS端口数量，这个就是显示 数量。

第七条 命令是 显示当前所有链接SS的用户IP，这个命令会去除重复的，最后显示的只有 正在链接SS服务器的用户的IP。

第八条 命令是 显示当前所有链接SS的用户IP数量，这个就是显示 数量。
