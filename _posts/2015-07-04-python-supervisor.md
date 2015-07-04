---
layout: post
category: Python
tagline: "Supporting tagline"
tags: [Python]
---
{% include JB/setup %}

supervisor是一个进程监控程序。
监控程序的执行，在程序出错终止后，supervisor可以启动程序。
##一、下载/安装
    https://pypi.python.org/pypi/supervisor
    doc: http://supervisord.org/
    pip安装sudo pip install supervisor
    测试安装是否成功,执行echo_supervisord_conf命令    

##二、配置
    创建配置文件，命令
    sudo echo_supervisord_conf > /etc/supervisord.conf
    如果你没有权限生成/etc/supervisord.conf文件，你可以用echo_supervisord_conf > supervisord.conf 命令将文件生成在其他目录下，通过supervisord命令的-c参数指定conf文件
    修改配置文件在supervisord.conf文件的最后添加:
    [program:programe_name] #程序的名称
    command = command_line #需要执行的命令
    process_name = %(programe_name)s    #进程名称
    numprocs = 1 #启动几个进程
    directory = /opt/soft/service #程序或命令将切换到此目录下执行
    autostart = true #supervisor启动的时候是否随着同时启动命令的执行
    autorestart = true #当程序退出的时候，这个programe会自动重启
    startsecs = 3 #程序重启是停留在running状态的秒数
    startretries = 10 #启动失败是的最多重试次数
    exitcodes = 0 #正常退出码
    stopwaitsecs = 10 #发送SIGKILL前的等待时间
    redirect_stderr = true #重定向stderr到stdout
    stopsignal = QUIT #KILL进程的信号(默认是TERM)

    supervisor监控的进程必须是非daemon方式运行的。

    配置web服务
    在supervisord.conf文件添加或者将注释掉的配置放开(;开头的是注释行)
    [inet_http_server]
    port=127.0.0.1:9001
    username=admin
    password=admin
    启动supervisor
    supervisrod -c /etc/supervisord.conf
    通过浏览器访问http://127.0.0.1:9001
    

##三、命令
    supervisord #启动supervisor服务
    supervisorctl #打开supervisor命令行
    在命令行中可以用help 查看命令帮助，status查看supervisord中程序的状态
    supervisord运行的日志存储在/tmp/supervisord.log
    supervisorctl stop programxxx 停止某一个进程(programxxx), programxxx为[program:chatdemon]里配置的值，这里就是chatdemon
    supervisorctl start programxxx 启动某个进程
    supervisorctl restart programxxx 重启某个进程
    supervisorctl stop all 停止全部进程，注: start、restart、stop都不会加载最新的配置文件
    supervisorctl reload 加载最新的配置文件，并按新的配置启动和管理所有的进程
    supervisorctl reread 当一个服务由自启动修改为手动启动是执行以下就可以了

    如何添加进程而不启动所有的服务进程
        1)修改supervisord.conf文件
        2)supervisorctl reread
        3)supervisorctl add xxservice

    如何删除进程而不启动所有的服务进程
        1)修改supervisord.conf文件
        2)supervisorctl reread
        3)supervisorctl update
    
    不带参数运行supervisord是以daemon方式运行的。
    当supervisord以非damon方式运行时，杀掉supervisord后，被监控的进程也退出执行。
    而以daemon方式运行的supervisord，杀掉supervisord后，被监控进程无影响。

