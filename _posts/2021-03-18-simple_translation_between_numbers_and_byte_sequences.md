---
layout: post
title:  "C++实现简单的序列化与反序列化"
categories: C/C++
tags: C++
author: ZhangJie
---

* content
{:toc}
在开发网络应用程序时，由于TCP是面向字节流的，因此需要将待传输的数据序列化成字节数组，当对方接收到字节数组时再根据接口协议反序列化成相应的内容。本文通过C++进行了简单的封装，可以方便地实现字节数组的序列化与反序列化。




### 代码实现

```cpp
#pragma once

#include <stdint.h>
#include <vector>

class ZBuffer
{
public:
    static const size_t kInitialSize = 1024;

    explicit ZBuffer(size_t initialSize = kInitialSize)
        : buffer_(initialSize)
        , writeIndex_(0)
        , readIndex_(0) {}

    ~ZBuffer() {}

    void writeInt64(int64_t x) { write(&x, sizeof(x)); }
    void writeInt32(int32_t x) { write(&x, sizeof(x)); }
    void writeInt16(int16_t x) { write(&x, sizeof(x)); }
    void writeInt8(int8_t x)   { write(&x, sizeof(x)); }

    void writeUInt64(uint64_t x) { write(&x, sizeof(x)); }
    void writeUInt32(uint32_t x) { write(&x, sizeof(x)); }
    void writeUInt16(uint16_t x) { write(&x, sizeof(x)); }
    void writeUInt8(uint8_t x)   { write(&x, sizeof(x)); }

    void writeBytes(uint8_t* data, size_t size) { write(data, size); }

    int64_t readInt64() {
        int64_t x = 0;
        ::memcpy(&x, beginRead(), sizeof(x));
        readIndex_ += sizeof(x);
        return x;
    }

    int32_t readInt32() {
        int32_t x = 0;
        read(&x, sizeof(x));
        return x;
    }

    int16_t readInt16() {
        int16_t x = 0;
        read(&x, sizeof(x));
        return x;
    }

    int8_t readInt8() {
        int8_t x = *beginRead();
        readIndex_ += sizeof(x);
        return x;
    }

    uint64_t readUInt64() {
        uint64_t x = 0;
        ::memcpy(&x, beginRead(), sizeof(x));
        readIndex_ += sizeof(x);
        return x;
    }

    uint32_t readUInt32() {
        uint32_t x = 0;
        read(&x, sizeof(x));
        return x;
    }

    uint16_t readUInt16() {
        uint16_t x = 0;
        read(&x, sizeof(x));
        return x;
    }

    uint8_t readUInt8() {
        uint8_t x = *beginRead();
        readIndex_ += sizeof(x);
        return x;
    }

    uint8_t* readBytes(size_t len)
    { 
        uint8_t* bytes = beginRead();
        readIndex_ += len;
        return bytes;
    }

    uint8_t* begin() {
        return &*buffer_.begin();
    }

    const uint8_t* begin() const {
        return &*buffer_.begin();
    }

    size_t readerIndex() {
        return readIndex_;
    }

    size_t writerIndex() {
        return writeIndex_;
    }

private:
    void write(const uint8_t* data, size_t len) {
        ensureWritableBytes(len);
        std::copy(data, data + len, beginWrite());
        writeIndex_ += len;
    }

    void write(const void* data, size_t len) {
        write(static_cast<const uint8_t*>(data), len);
    }

    void read(void* data, size_t len) {
        ::memcpy(data, beginRead(), len);
        readIndex_ += len;
    }

    uint8_t* beginWrite() { return begin() + writeIndex_; }
    const uint8_t* beginWrite() const { return begin() + writeIndex_; }

    uint8_t* beginRead() { return begin() + readIndex_; }
    const uint8_t* beginRead() const { return begin() + readIndex_; }

    size_t writableBytes() const { return buffer_.size() - writeIndex_; }
    size_t readableBytes() const { return writeIndex_ - readIndex_; }

    bool ensureWritableBytes(size_t len) {
        if (writableBytes() < len) {
            makeSpace(buffer_.size() * 2 + len);
        }
        return true;
    }

    void makeSpace(size_t len) {
        std::vector<uint8_t> tmp(len);
        std::copy(buffer_.begin(), buffer_.end(), tmp.begin());
        buffer_.swap(tmp);
    }

private:
    std::vector<uint8_t> buffer_;
    size_t readIndex_;
    size_t writeIndex_;
};
```

### 使用示例

```cpp
#pragma pack(1)

struct St
{
    St(uint64_t a, int8_t b) : a_(a), b_(b) {}
    uint64_t a_;
    int8_t b_;
};

#pragma pack()

int main()
{
    ZBuffer zbuf(4);
    zbuf.writeUInt32(0x1234);
    zbuf.writeInt64(0x5678);
    zbuf.writeUInt8(0xFF);
    zbuf.writeInt16(0xABCD);
    St st(0x5566, 0x88);
    zbuf.writeBytes((uint8_t*)&st, sizeof(St));

    uint32_t a1 = zbuf.readUInt32();
    int64_t a2 = zbuf.readInt64();
    uint8_t a3 = zbuf.readUInt8();
    int16_t a4 = zbuf.readInt16();
    St a5 = *(St*)zbuf.readBytes(sizeof(St));

    return 0;
}
```
