---
layout: post
title:  "实现一个简易的日志记录模块"
categories: C/C++
tags: C++ Logger
author: ZhangJie
---

* content
{:toc}
在软件开发中，日志记录十分重要，好的日志记录能够帮助程序员准确的定位与分析问题。C++目前常见的开源日志库也较多，例如：easyloggingpp，glog等等。这些第三方库都很好用，
但是有些时候为了方便编写日志解析工具进行一些日志分析，我们有必要对日志记录内容进行一些定制，因此，简单起见，我们完全可以实现一个满足自己需求的日志记录模块。




完整程序及测试程序详见：[https://github.com/AnonymousRookie/useful-codes/tree/master/c_and_cplusplus/026-logger/001](https://github.com/AnonymousRookie/useful-codes/tree/master/c_and_cplusplus/026-logger/001)


## 实现思路

主要实现思路：一个消费者多个生产者，生产者负责将需要记录的内容放入一个队列中，消费者从队列中取出并写入文件。采用单例模式确保在我们的程序中只产生一个logger实例对象，
在程序的入口处初始化logger，创建日志文件，启动一个线程，通过条件变量判断队列中是否有数据，有则取出并写入文件。在需要记录log的地方直接调用相关函数将待记录内容放入队列中即可。


## 具体实现

```cpp
class Logger
{
public:
    ~Logger();
    static Logger& GetInstance();
    void SetFileBaseName(const char* fileBaseName);
    void SetRollSize(uint64_t rollSize);
    void AddToQueue(const char* level, const char* file, int line, const char* func, const char* fmt, ...);
    void Start();

private:
    Logger() = default;
    Logger(const Logger&) = delete;
    Logger& operator=(const Logger&) = delete;

    void Stop();
    void ThreadFunc();

private:
    std::string fileBaseName_;
    uint64_t rollSize_{ 1024 * 1024 };  // 1M
    FILE* fp_{};
    std::thread thread_;
    mutable std::mutex mutex_;
    std::condition_variable cv_;
    std::deque<std::string> queue_;
    bool started_{ false };
    bool exit_{ false };
    uint64_t writtenBytes_{ 0 };
};

#ifndef _WIN32
# define LOG_INFO(args...) Logger::GetInstance().AddToQueue("INFO", __FILE__, __LINE__, __func__, ##args)
# define LOG_WARN(args...) Logger::GetInstance().AddToQueue("WARN", __FILE__, __LINE__, __func__, ##args)
# define LOG_ERROR(args...) Logger::GetInstance().AddToQueue("ERROR", __FILE__, __LINE__, __func__, ##args)
#else
# define LOG_INFO(...) Logger::GetInstance().AddToQueue("INFO", __FILE__, __LINE__, __func__, __VA_ARGS__)
# define LOG_WARN(...) Logger::GetInstance().AddToQueue("WARN", __FILE__, __LINE__, __func__, __VA_ARGS__)
# define LOG_ERROR(...) Logger::GetInstance().AddToQueue("ERROR", __FILE__, __LINE__, __func__, __VA_ARGS__)
#endif
```

```cpp
Logger::~Logger()
{
    Stop();
}

Logger& Logger::GetInstance()
{
    static Logger logger;
    return logger;
}

void Logger::SetFileBaseName(const char* fileBaseName)
{
    fileBaseName_ = fileBaseName;
}

void Logger::SetRollSize(uint64_t rollSize)
{
    rollSize_ = rollSize;
}

void Logger::Start()
{
    Z_CHECK(false == started_);

    time_t now = time(NULL);
    struct tm t;
    localtime_r(&now, &t);

    char name[256] = { 0 };
    sprintf(name, "%s_%04d%02d%02d%02d%02d%02d.log",
        fileBaseName_.c_str(),
        t.tm_year + 1900, t.tm_mon + 1, t.tm_mday,
        t.tm_hour, t.tm_min, t.tm_sec);

    fp_ = fopen(name, "wt+");
    Z_CHECK(fp_ != nullptr);

    char startInfo[] = "Logger started!\n";
    fwrite((void*)startInfo, strlen(startInfo), 1, fp_);
    thread_ = std::thread(std::bind(&Logger::ThreadFunc, this));
    started_ = true;
}

void Logger::Stop()
{
    exit_ = true;
    cv_.notify_one();
    if (thread_.joinable()) {
        thread_.join();
    }
    if (!fp_) {
        fclose(fp_);
        fp_ = nullptr;
    }
}

void Logger::AddToQueue(const char* level, const char* file, int line, const char* func, const char* fmt, ...)
{
    char msg[256] = { 0 };

    va_list va;
    va_start(va, fmt);
    vsnprintf(msg, sizeof(msg), fmt, va);
    va_end(va);

    time_t now = time(NULL);
    struct tm t;
    localtime_r(&now, &t);
    char content[512] = { 0 };
    sprintf(content, "[%04d-%02d-%02d %02d:%02d:%02d][%s][0x%04x][%s:%d %s] %s\n",
        t.tm_year + 1900, t.tm_mon + 1, t.tm_mday,
        t.tm_hour, t.tm_min, t.tm_sec,
        level,
        std::this_thread::get_id(),
        file, line, func,
        msg);

    printf("%s\n", content);

    std::unique_lock<std::mutex> lock(mutex_);
    queue_.push_back(content);
    cv_.notify_one();
}

void Logger::ThreadFunc()
{
    Z_CHECK(fp_ != nullptr);

    while (!exit_) {
        std::unique_lock<std::mutex> lock(mutex_);
        while (queue_.empty()) {
            if (exit_) {
                return;
            }
            cv_.wait(lock);
        }

        const std::string& log = queue_.front();
        if (writtenBytes_ > rollSize_) {
            fclose(fp_);
            writtenBytes_ = 0;

            time_t now = time(NULL);
            struct tm t;
            localtime_r(&now, &t);

            char name[256] = { 0 };

            sprintf(name, "%s_%04d%02d%02d%02d%02d%02d.log",
                fileBaseName_.c_str(),
                t.tm_year + 1900, t.tm_mon + 1, t.tm_mday,
                t.tm_hour, t.tm_min, t.tm_sec);

            fp_ = fopen(name, "wt+");
        }
        fwrite((void*)log.c_str(), log.length(), 1, fp_);
        fflush(fp_);
        writtenBytes_ += log.size();
        queue_.pop_front();
    }
}
```

## 示例

使用方法较为简单，示例如下：

```cpp

#include "z_logger.h"

int main()
{
    Logger::GetInstance().SetFileBaseName("appname");
    Logger::GetInstance().SetRollSize(10 * 1024 * 1024);  // 10M
    Logger::GetInstance().Start();

    LOG_INFO("info = %d", 1);
    LOG_WARN("warn = %d", 2);
    LOG_ERROR("error = %d", 3);

    return 0;
}
```

运行程序，生成appname_20190805224755.log
```
Logger started!
[2019-08-05 22:46:53][INFO][0x2f10][e:\projects\vs2015\z_logger\z_logger\test.cpp:10 main] info = 1
[2019-08-05 22:46:53][WARN][0x2f10][e:\projects\vs2015\z_logger\z_logger\test.cpp:11 main] warn = 2
[2019-08-05 22:46:53][ERROR][0x2f10][e:\projects\vs2015\z_logger\z_logger\test.cpp:12 main] error = 3
```