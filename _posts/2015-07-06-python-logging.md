---
layout: post
category: Python
tilte: supervisor
tagline: "Supporting tagline"
tags: [Python]
---
{% include JB/setup %}

###一、介绍
日志用于跟踪和记录系统的日常运行
常用于:
    在控制台输出信息
    记录重要事件，发生在程序正常运行中: logging.info()或logging.debug()
    发出警告, 在预设的特定的运行情况下: logging.warning()或logging.warn()
    报告错误，一个特定的运行时事件: 异常处理
    报告运行错误: logging.error()/loggin.exception()或logging.critical()

Python的日志模块是logging，包含在标准库中
日志级别大小关系
CRITICAL > ERROR > WARNING > INFO > DEBUG
DEBUG: 详细的嘻嘻你，通常用于诊断问题
INFO: 输出普通运行信息
WARNING: 记录发生了异常情况，或表面一些问题即将发生(例如:磁盘空间低)
ERROR: 严重问题，软件没有正常执行一下功能
CRITICAL: 一个严重的错误，表明程序本身无法继续执行

也可以自己定义日志级别
默认logging将日志输出到标准输出，级别是WARNING，当级别在WARNING或之上的级别才会被跟踪。有两种方式记录跟踪，控制台输出和输出到文件中。

###二、配置
logging.basicConfig函数个参数说明:
    filename: 指定生成日志文件的名称(可以按日期生成，每天/每月一个文件)。如果配置了，将自动创建一个Filehandler作为handler
    filemode: 指定日志文件的打开模式'w'或'a'。默认值为a,表示日志信息以追加的方法添加到日志文件中。如果为'w'，那么程序每次启动时都会创建一个新的日志文件。
    datefmt: 指定时间格式，同time.strftime()
    level: 指定日志级别，默认是logging.warning
    stream: 指定日志的输出流，可以指定输出到sys.stderr, sys.stdout或者文件，默认输出到sys.stderr,当stream和filename同时指定时，stream被忽略
    format: 指定输出的格式和内容，format可以定义很多格式化的信息，配置如下:
        %(levelno)s: 日志级别数值
        %(levenname)s: 日志级别名称
        %(pathname)s: 当前执行程序的路径，就是sys.argv[0]
        %(filename)s: 当前执行程序名称
        %(funcName)s: 当前执行函数名称
        %(lineno)s: 输出日志的行号
        %(asctime)s: 日志输出时间
        %(thread)d: 线程ID
        %(threadName)s: 线程名称
        %(process)d: 进程ID
        %(message)s: 日志信息

日志的格式化字符串
方式一: logging.warning('name:%s msg:%s', 'hello', 'logging')
方式二: logging.warning('name:%s msg:%s' % ('hello', 'logging'))
方式三: logging.warning('name:{0} msg:{1}'.format('hello', 'logging'))
方式四:
from string import Template
msg = Template('name: $who msg:$what')
logging.warning(msg.substitue(who='hello', what='logging'))

改变消息格式
logging.basicConfig(format='%(levelname)s:%(message)s')
logging.warning('msg')


日志的格式，和输出方式通过logging.basicConfig函数进行配置
如:
logging.basicConfig(
    level=logging.DEBUG,
    format='%(asctime)s: %(filename)s[line:%(lineno)d] %(levelname)s %(message)s',
    datefmt='%Y-%m-%d/%H:%M:%S',
    filename='demo.log',
    filemode='a'
)

logging.debug('debug')
logging.info('info')
logging.warning('warning')


日志输出到文件的同时在终端显示
import logging

logging.basicConfig(
    level=logging.DEBUG,
    format='%(asctime)s %(filename)s [line:%(lineno)d] %(levelname)s %(message)s',
    datefmt='%Y-%m-%d/%H:%M:%S',
    filename='demo.log',
    filemode='w'
)

#定义一个StreamHandler， 将INFO级别或更高的日志信息打印到标准错误终端，并将其添加的日志处理对象
console = logging.StreamHandler()
console.setLevel(logging.INFO)
formatter = logging.Formatter('%(name)-12s:%(levelname)-8s %(message)s')
console.setFormatter(formatter)
logging.getLogger('').addHandler(console)

logging.debug('This is debug message')
logging.info('This is info message')
logging.warning('This is warning message')

日志文件大小和数量控制(日志回滚)
import logging
from logging.handlers import RotatingFileHandler

\###################################################
\#定义一个RotatingFileHandler, 最多备份5个日志文件,每个日志文件最大10M
rthandler = RotatingFileHandler('demo.log', maxBytes=10*1024*1024, backupCount=5)
rthandler.setLevel(logging.INFO)
formatter = logging.Formatter('%(name)-12s:%(levelname)-8s %(message)s)
rthandler.setFormatter(formatter)
logging.getLogger('').addHandler(rthandler)

logging默认有一个日志处理的对象，其他处理对象可以通过addHandler方法添加。
handler有如下几种:
    logging.StreamHandler: 日志输出到标准流中，sys.stderr, sys.stdout或者日志文件
    logging.FileHanlder: 日志输出到文件

    日志回滚方式，实际中通常使用RotatingFileHandler和TimedRotatingFileHandler
    logging.handlers.BaseRotatingHandler
    logging.handlers.RotatingFileHandler
    logging.handlers.TimedRotatingFileHandler

    logging.handlers.SocketHandler: 远程输出日志到TCP/IP
    logging.handlers.DatagramHandler: 远程输出日志到UDP sockets
    logging.handlers.SMTPHandler: 远程输出日志到邮件地址
    logging.handlers.SysLogHandler: 日志输出到syslog
    logging.handlers.NTEventLogHandler: 远程输出日志到Windows NT/2000/XP的事件日志
    logging.handlers.MemoryHandler: 日志输出到内存中的制定buffer
    logging.handlers.HTTPHandler: 通过GET或POST远程输出到HTTP服务器
因为StreamHandler和FileHandler是最常用的日志处理方式，所以直接包含在了logging模块中，其他处理方式则包含着logging.handlers模块中。

通过logging.config模块配置日志
\#logger.conf
\###############################################
[loggers]
keys=root,example01,example02
[logger_root]
level=DEBUG
handlers=hand01,hand02
[logger_example01]
handlers=hand01,hand02
qualname=example01
propagate=0
[logger_example02]
handlers=hand01,hand03
qualname=example02
propagate=0
\###############################################
[handlers]
keys=hand01,hand02,hand03
[handler_hand01]
class=StreamHandler
level=INFO
formatter=form02
args=(sys.stderr,)
[handler_hand02]
class=FileHandler
level=DEBUG
formatter=form01
args=('myapp.log', 'a')
[handler_hand03]
class=handlers.RotatingFileHandler
level=INFO
formatter=form02
args=('myapp.log', 'a', 10*1024*1024, 5)
\###############################################
[formatters]
keys=form01,form02
[formatter_form01]
format=%(asctime)s %(filename)s[line:%(lineno)d] %(levelname)s %(message)s
datefmt=%a, %d %b %Y %H:%M:%S
[formatter_form02]
format=%(name)-12s: %(levelname)-8s %(message)s
datefmt=

使用实例1
import logging
import logging.config

logging.config.fileConfig('logger.conf')
logger = logging.getLogger('example01')

logger.debug('This is debug message')
logger.info('This is info message')
logger.warning('This is warning message')


使用实例2
import logging
import logging.config

logging.config.fileConfig("logger.conf")
logger = logging.getLogger("example02")

logger.debug('This is debug message')
logger.info('This is info message')
logger.warning('This is warning message')


###三、高级配置
logging常用方法
logging.info(msg[,*args,[*,kwargs]])
logging.debug(msg[,*args,[*,kwargs]])
logging.warning(msg[,*args,[*,kwargs]])
logging.error(msg[,*args,[*,kwargs]])
logging.critical(msg[,*args,[*,kwargs]])
logging.exception(msg[,*args,[*,kwargs]])
logging.log(level, msg[,*args,[*,kwargs]])
logging.disable(level) \#禁用所有日志当其级别在level级别及以下，当出现需要暂时截流日志输出下来整个应用程序，这个函数是有用的。
logging.addLevelName(lel, levename) #自定义级别，lel是int数字，levelname是级别名称
级别整数如下:
CRITICAL 50
ERROR    40
WARNING  30
INFO     20
DEBUG    10
NOTSET   0

logging.getLevelName(lvl) \#通过级别整数返回级别名称


日志组件包括: logger、handlers、filters、formatters
Logger: Logger对象很重要。首先，它暴露给应用的几个方法可以在运行时写log，其次，Logger对象按照log信息的严重程度或者根据filter对象来决定如何处理log信息(默认的过滤功能)。最后Loger还负责把log信息传送给相关的loghandlers。日志的记录工作主要有Logger来完成。在调用getLogger时要提供Logger的名称(注:多次使用相同名称来调用getLogger，返回的是同一个Logger对象的引用)，Logger实例之间有层次关系，这些关系通过Logger名称来体现。
如:
    p = logging.getLogger('root')
    c1 = logging.getLogger('root.c1')
    c2 = logging.getLogger('root.c2')
getLogger()返回日志对象层次关系中的根Logger
指定对象名称: logger = logging.getLogger('demo')
无名对象: logger = logging.getLogger()
推荐方式: logger = logging.getLogger(__name__)
logger有如下属性和方法:
    logger.propagate \#1
    loggger.setLevel(lvl) \#设置日志级别，忽略低于此级别的日志。
    logger.debug(msg, [,*args[, **kwargs]]) \#处理DEBUG级别的日志。参数msg是信息的格式字符串，args和kwargs分别是格式参数。args作用于msg，是使用字符串格式操作符(注:这意味着可以使用关键字的格式字符串，连同一个字典参数)。kwargs:两个关键字参数,exc_info/extra
    logger.info(msg, [,*args[, **kwargs]]) \#同上
    logger.warnning(msg, [,*args[, **kwargs]]) \#同上
    logger.error(msg, [,*args[, **kwargs]]) \#同上
    logger.critical(msg, [,*args[, **kwargs]]) \#同上
    logger.log(lvl, msg,[,*args,[,**kwargs]]) \#设置日志级别，同时设置日志格式
    logger.exception(msg[,*args]) \#以ERROR级别记录日志消息，异常跟踪消息将被自动添加到消息里。Logger.exception通常用在异常处理块中excpet部分。
    logger.addFilter(filt) \#添加过滤器
    logger.removeFilter(filt) \#删除过滤器
    logger.filter(record) \#过滤

Handler: Handler对象负责分配合适的log信息(基于log信息的严重程度)到handler指定的目的地。Logger最新可以用addHandler()方法添加一个或多个handler。一个常用的场景，是一个应用希望把所有的log信息都写入log文件，所有error级别以上的log信息都发送到stdout，所有critical的log信息通过email发出。这个场景要求三个不同handler处理，每个handler负责把特定的log信息发送到指定的地方。
StreamHandler：在logging包中，日志输出流，如果sys.stdout/std.stderr
FileHandler: 日志输出到磁盘文件，它继承了StreamHandler输出功能。
    calss logging.FileHandler(filename, mode='a', encoding=None, delay=False)。如果设置delay=True,那么文件在第一次调用emit()时才打开。
    它有close():关闭一个文件;emit(record):输出文件的记录。

handler方法:
    handler = logging.Handler() \#创建Handler对象
    hanlder.__init__(logging.DEBUG) \#通过设置level来初始化Handler实例
    handler.createLock() \#初始化一个线程锁可以用来序列化访问底层I/O功能，这可能不是线程安全的。
    handler.acquire() \#获取线程锁通过handler.createLock()
    handler.release() \#释放线程锁通过获取handler.acquire()
    handler.setLevel(logging.DEBUG) \#设置临界值，如果Logging信息级别小于它则被忽略，当一个handler对象被创建，级别没有被设置，导致所有的信息会被处理。
    handler.setFormatter('%(levelname)s, %(message)s') #设置格式
    handler.addFilter(filter) \#添加指定的过滤器
    handler.removeFilter(filter) \#删除过滤器
    handler.filter(record) \#通过设置过滤器适用于记录并返回真值如果是要处理的记录
    handler.flush() \#确保所有的日志已经被刷新
    handler.close() \#释放所使用的资源
    handler.handle(record) \#有条件地发出指定的日志记录，这取决于过滤器可能被添加到处理程序。
    handler.handlerError(record) \#处理错误
    handler.format(record) \#格式输出
    handler.emit(record)


filter: 过滤选择哪些日志输出
format: 设置日志格式
方法:
    fm = logging.Formatter('%(levenname)s:%(message)s', '%Y-%m-%d/%H:%M:%S')
    fm.format() \#日志格式化
    fm.formatTime() \#时间格式化
    fm.formatException() \#异常格式化


###四、推荐配置

\#!/usr/bin/env python
\#-*- coding:utf-8 -*-

import logging
import datetime
import os

DIRNAME = os.path.dirname(os.path.abspath(__file__))
print DIRNAME
LOGDIR = os.path.join(DIRNAME,'log')
LOGFILE = datetime.datetime.now().strftime('%Y-%m-%d')+'.log'
logging.basicConfig(level=logging.DEBUG,
                    format='',
                    datefmt='%Y-%m-%d/%H:%M:%S',
                    filename = os.path.join(LOGDIR,LOGFILE),
                    filemode='a'
                    )

fileLog = logging.FileHandler(os.path.join(LOGDIR,LOGFILE),'w')
formatter = logging.Formatter('%(asctime)s %(name)s[line:%(lineno)d]:%(levelname)s %(message)s')
fileLog.setFormatter(formatter)

logging.getLogger('demo').addHandler(fileLog)
logging.getLogger('demo').setLevel(logging.DEBUG)
logging.getLogger('demo').info(u'项目已启动：')


基于：
http://www.cnblogs.com/BeginMan/p/3328671.html
http://www.cnblogs.com/BeginMan/p/3335110.html
http://www.cnblogs.com/dkblog/archive/2011/08/26/2155018.html
整理
