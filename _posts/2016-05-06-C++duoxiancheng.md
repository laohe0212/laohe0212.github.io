---
layout: default
title: C++多线程编程
---
<a href="https://wangxiaozhi123.github.io">返回</a>
<h1>{{ page.title }}</h1>
<p>{{ page.date | date_to_string }}</p>
<hr>
###一直对于多线程不太熟悉，甚至对很多多线程代码有误解。通过查阅资料和亲自尝试对多线程才稍微有些了解。

####下面请看代码

    // condition_variable example
    #include <iostream>           // std::cout
    #include <thread>             // std::thread
    #include <mutex>              // std::mutex, std::unique_lock
    #include <condition_variable> // std::condition_variable
    
    std::mutex mtx;
    std::condition_variable cv;
    bool ready = false;
    
    void print_id (int id) {
      std::unique_lock<std::mutex> lck(mtx);
      while (!ready) cv.wait(lck);
      // ...
      std::cout << "thread " << id << '\n';
    }

    void go() {
      std::unique_lock<std::mutex> lck(mtx);
      ready = true;
      cv.notify_all();
    }

    int main ()
    {
      std::thread threads[10];
      // spawn 10 threads:
      for (int i=0; i<10; ++i)
        threads[i] = std::thread(print_id,i);

      std::cout << "10 threads ready to race...\n";
      go();                       // go!

      for (auto& th : threads) th.join();
    
      return 0;
    }

#####1.创建线程对象后线程立刻执行函数。我原以为是调用了`for (auto& th : threads) th.join();`这句才会执行线程代码。把这句去掉后线程一样会执行，只不过会抛出异常。

#####2.`while (!ready) cv.wait(lck);`这行代码会使unique_lock解锁使得其它线程不被阻塞，等到被唤醒后重新上锁。至于为什么用while不用if，应该是为了防止虚假唤醒。标准库的thread是否会有虚假唤醒的情况我没有找到资料（如果各位看官知道的话恳请告诉我一下），但是在Linux下std::thread是通过posix的线程实现的，我想应该存在虚假唤醒的情况。

#####3.把`cv.notify_all();`这句换成`cv.notify_one();`，最后结果是出现一部分线程能输出而不是只有一个。结果也很好理解。出现的n个线程当中只有一个是被唤醒的其他的线程是没来得及被等待直接执行，也就是说是在go函数之后执行。
