---
title: 通用hash方法
categories:
- C++知识
tags:
- hash
- 常数优化
---

由于unordered_map, unordered_set等无序关联式容器内置的hash函数有限，pair，tuple，array等类型无法作为key值，需要自己写hash函数（值得一提的是bitset有特化的hash函数可以直接作为键值），这些类型是可以可以作为关联式容器例如set、map的key值的（vector也可以），但复杂度多一个log，因此记录一个比较简单通用的hash方法。

适用于int，long，char等数据类型的pair，tuple，array

```
struct bithash {
    template<class T>size_t operator()(T&& t) const
    { return hash<string_view> {} ({ (char const*)&t, sizeof t }); }
};
```