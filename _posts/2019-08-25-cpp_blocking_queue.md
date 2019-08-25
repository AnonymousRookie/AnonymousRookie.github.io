---
layout: post
title:  "BlockingQueue(阻塞队列)的实现"
categories: C/C++
tags: C++ BlockingQueue
author: ZhangJie
---

* content
{:toc}
阻塞队列，在生产者/消费者的场景中使用较多，例如：一个配置文件生成工具中，多个线程同时工作，不同线程分别生成不同的文件，程序运行过程中需要将运行信息及时地显示到软件界面，那么我们就可以借助于BlockingQueue，多个线程将运行时信息put到BlockingQueue中，另一个线程一直不断地从BlockingQueue中take数据，有则取出并显示到界面。在Java中，BlockingQueue是一个现成的接口，实现原理比较简单，只需要一个普通队列，一个互斥器，一个条件变量，分别实现put，take方法即可。




完整程序及测试程序详见：[https://github.com/AnonymousRookie/useful-codes/tree/master/c_and_cplusplus/027-blocking_queue/001](https://github.com/AnonymousRookie/useful-codes/tree/master/c_and_cplusplus/027-blocking_queue/001)



## 具体实现

```cpp
template<typename T>
class BlockingQueue
{
public:
    BlockingQueue() {}
    ~BlockingQueue() {}
    void put(const T& x) {
        std::unique_lock<std::mutex> lock(mutex_);
        queue_.push_back(x);
        notEmpty_.notify_one();
    }
    T take() {
        std::unique_lock<std::mutex> lock(mutex_);
        while (queue_.empty()) {
            notEmpty_.wait(lock);
        }
        T front(queue_.front());
        queue_.pop_front();
        return front;
    }
    size_t size() const {
        std::unique_lock<std::mutex> lock(mutex_);
        return queue_.size();
    }
private:
    mutable std::mutex mutex_;
    std::deque<T> queue_;
    std::condition_variable notEmpty_;
};
```

## 使用示例

使用方法较为简单，示例如下：

```cpp
BlockingQueue<int> blockingQueue;
void produce()
{
    int num = 0;
    for (;;) {
        blockingQueue.put(num++);
        std::this_thread::sleep_for(std::chrono::seconds(1));
    }
}
void consume()
{
    for (;;) {
        int num = blockingQueue.take();
        std::cout << "take num: " << num << std::endl;
    }
}
int main()
{
    std::thread produce1(produce);
    std::thread produce2(produce);
    std::thread consume(consume);
    produce1.join();
    produce2.join();
    consume.join();
    return 0;
}
```
