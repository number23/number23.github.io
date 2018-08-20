{:title "setup exim on debian"
 :layout :post
 :tags  ["Linux", "Exim"]}

我的debian版本：6.0.3，使用系统自带的exim，如果你的系统还没有安装exim，先装这几个包：
```
Shell:~ >: aptitude search exim | grep '^i'
i   exim4                           - metapackage to ease Exim MTA (v4) installa
i   exim4-base                      - support files for all Exim MTA (v4) packag
i   exim4-config                    - configuration for the Exim MTA (v4)
i   exim4-daemon-light              - lightweight Exim MTA (v4) daemon
```
设置你的/etc/hosts, /etc/resolv.conf
```
Shell:~ >: cat /etc/resolv.conf
nameserver 127.0.0.1
nameserver 8.8.8.8
nameserver 8.8.4.4
```
可以装一下dnsmasq，避开那些讨厌的dns问题
```
Shell:~ >: aptitude search dnsmasq | grep '^i'
i   dnsmasq                         - A small caching DNS proxy and DHCP/TFTP se
i A dnsmasq-base                    - A small caching DNS proxy and DHCP/TFTP se
```
##### 开始配置
假设我的域名为：number23.no.de，可以先写在/etc/hosts里，
<!--more-->
```
sudo dpkg-reconfigure exim4-config
```
接下来就是按Enter选择了：
    第一个, General type of mail configuration: internet site
    第二个, System mail name: number23.no.de
    第三个, IP-addresses to listen on for incoming SMTP connections: 空白
    第四个, Other destinations for which mail is accepted: localhost.localdomain:localhost:number23.no.de
    第五个, Domains to relay mail for: 空白
    第六个, Machines to relay mail for: 127.0.0.0/8:192.168.0.0/16:10.0.0.0/8
    第七个, Keep number of DNS-queries minimal: No
    第八个, Delivery method for local mail: /var/mail
    第九个, Split configuration into small files: Yes

看看弄出来的配置文件：
```
Shell:~ >: cat /etc/exim4/update-exim4.conf.conf
dc_eximconfig_configtype='internet'
dc_other_hostnames='localhost.localdomain:localhost:number23.no.de'
dc_local_interfaces=''
dc_readhost=''
dc_relay_domains=''
dc_minimaldns='false'
dc_relay_nets='127.0.0.0/8:192.168.0.0/16:10.0.0.0/8'
dc_smarthost=''
CFILEMODE='644'
dc_use_split_config='true'
dc_hide_mailname=''
dc_mailname_in_oh='true'
dc_localdelivery='mail_spool'
```
启动它：Shell:~ >: sudo /etc/init.d/exim4 start，具体选项详细参考man update-exim4.conf 和exim[官方文档](http://www.exim.org/exim-html-current/doc/html/spec_html/index.html)。

##### 优化选项
在 /etc/exim4/conf.d/main/02_exim4-config_options 的最后面加上这些：
```
smtp_accept_max=256
smtp_accept_queue_per_connection=0
smtp_accept_queue=128
smtp_accept_reserve=20
deliver_queue_load_max=20
queue_only_load=20
queue_run_max=30
smtp_enforce_sync=false
message_size_limit=20M
```
修改MAIN_IGNORE_BOUNCE_ERRORS_AFTER， MAIN_TIMEOUT_FROZEN_AFTER，
```
Shell:/etc/exim4/conf.d/main >: exim4 -bP | grep after
ignore_bounce_errors_after = 1d
timeout_frozen_after = 2d
```

#### 注意
如果Split configuration into small files, 选择No的话, 这些修改都不会有效, exim -bP 查看所有选项。  
smtp_enforce_sync: SMTP同步错误, 参考[这里](http://usneitn.blogspot.com/2009/02/smtp-protocol-synchronization-error.html)

##### qpopper/pop 优化选项
```
Shell:~ >: grep pop /etc/inetd.conf
##pop-3	stream	tcp	nowait	root	/usr/sbin/tcpd	/usr/sbin/in.qpopper -f /etc/qpopper.conf
pop-3	stream	tcp	nowait.512	root	/usr/sbin/tcpd	/usr/sbin/in.qpopper -F -S -s -R -T 600
```

    nowait.512: 每分钟最多可接受的连接请求数目
    -F: enable-fast-update
    -S: enable-server-mode
    -s: status
    -R: none dns lookup
    -T: timeout
