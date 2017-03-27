---
layout: default
title: 在deepin下编写Boost.Asio代码的解决步骤
---
<a href="https://wangxiaozhi123.github.io">返回</a>
<h1>{{ page.title }}</h1>
<p>{{ page.date | date_to_string }}</p>
最近部署程序和平时使用的Linux都转移到了Debian系

在安装Boost时 发现了一点坑。

若是以往使用centos fedora。可以直接使用

<code>yum install boost</code>

同理我使用apt-get包管理器去安装Boost

<code>sudo apt-get install libboost-dev</code>

然后使用g++ -lboost_system发现总是找不到boost_system

尝试各种方法无果。 最后发现还必须得单独安装libboost-system-dev

<code>sudo apt-cache search libboost | grep system</code>

<code>sudo apt-get install libboost-system-dev</code>

终于体会到了apt包与yum包完全不一样的风格

随后就通过了编译体验到了Boost.Asio特有的风格。开始了我的Asio库的学习之旅

    #include <boost/asio.hpp>
    #include <iostream>

    void handler(const boost::system::error_code &ec)
    {
      std::cout << "5 s." << std::endl;
    }

    int main()
    {
      boost::asio::io_service io_service;
      boost::asio::deadline_timer timer(io_service, boost::posix_time::seconds(5));
      timer.async_wait(handler);
      io_service.run();
    }


结果为等5s输出"5 s."
