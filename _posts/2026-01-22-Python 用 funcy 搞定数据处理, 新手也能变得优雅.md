---
title: "Python 用 funcy 搞定数据处理, 新手也能变得优雅"
date: 2026-01-22 09:22:00 +0800
categories: [Python, lib]   # 分类 (支持多级) 
tags: [Python, Python-lib]               				# 标签 (不限数量) 
description: "[funcy](https://funcy.readthedocs.io/en/stable/) 是一个功能强大的 Python 库, 提供了许多实用的函数式编程工具, 帮助我们更优雅地处理数据. 本文将介绍 funcy 的一些常用功能, 让新手也能轻松上手."  # 文章描述 (SEO)
author: "YoungZhongque"
pin: false                             		# 是否置顶 (true / false) 

# 封面图 (社交媒体预览图最佳尺寸 1200x630) 
# image:
  # path: /assets/img/sample-cover.jpg     # 站点内路径
  # alt: "封面图描述"
  # lqip: /assets/img/lqip/sample.jpg      #  (可选) 低质量占位图

# 是否启用目录 (左侧浮动目录) 
toc: true
toc_label: "目录"
toc_icon: "list-ul"

# 文章版权 / 转载声明 (可选) 
copyright:
  license: CC BY-NC-SA 4.0
  holder: "YoungZhongque"
---

本文源自: \[Python小甲鱼\] - [告别多层for循环! Python用funcy一行搞定数据处理, 新手也能秒变优雅 ](https://mp.weixin.qq.com/s/Q2uyUrvuVNJvqOk_1YShAw) 

GitHub: [Suor-funcy](https://github.com/Suor/funcy) 

## 1. funcy 功能

是 [循环简化工具包], 解决新手3大痛点：

- 不用写多层for循环: 展平列表、分组、切块，一行函数直接搞定

- 不用套复杂判断: 值转换、异常处理，内置函数自动兼容

- 代码可读性拉满: 函数名就是功能 (flatten=展平、group_by=分组)



---

## 2. 第一步: 安装+验证

**安装命令**: 

```shell
pip install funcy
```

**验证安装**: 

```python
from funcy import flatten

# 新手头疼的嵌套列表
nested_list = [[1, 2], [3, [4, 5]], 6]
# 一行展平，不用写循环
flat_list = list(flatten(nested_list))
print("展平后的列表：", flat_list)  # 输出：[1,2,3,4,5,6]
```



---

## 3. 核心案例

### 3.1. 字典转换 (不用 try-except, 异常自动忽略) 

**非 funcy 写法**:

```python
data = {'user_a': '18', 'user_b': 'unknown', 'user_c': '25'}
result = {}
for key, value in data.items():
    try:
        result[key] = int(value)  # 尝试转成整数
    except ValueError:
        result[key] = None  # 失败返回None
print(result)  # 输出：{'user_a':18, 'user_b':None, 'user_c':25}
```

**funcy 写法**: 

```python
from funcy import walk_values, silent

data = {'user_a': '18', 'user_b': 'unknown', 'user_c': '25'}
# walk_values：遍历所有值；silent(int)：转int失败返回None，不报错
result = walk_values(silent(int), data)
print(dict(result))  # 输出：{'user_a':18, 'user_b':None, 'user_c':25}
```

> [!Note]
>
> 1. `walk_keys` 能遍历字典的键
> 2. `silent` 能包装任意函数, 失败都返回 None



### 3.2. 列表处理

**1. 展平嵌套列表 (Flatten)** 

- **常用写法 (for 循环)**: 

  ```python
  nested_list = [[1, 2], [3, [4, 5]]]
  flat_list = []
  for sublist in nested_list:
      for item in sublist:
          flat_list.append(item)
  ```

- **funcy 优雅写法 (一行)**:

  ```python
  from funcy import flatten
  nested_list = [[1, 2], [3, [4, 5]]]
  flat_list = list(flatten(nested_list))
  ```

**2. 按条件分组 (Group By)** 

- **常用写法 (以奇偶数分组)**:

  ```python
  nums = range(6)
  groups = {0: [], 1: []}
  for i in nums:
      groups[i % 2].append(i)
  ```

- **funcy 优雅写法**:

  ```python
  from funcy import group_by
  nums = range(6)
  groups = group_by(lambda x: x % 2, nums)
  ```

**3. 去重并保持顺序 (Distinct)** 

- **常用写法**:

  ```python
  s = 'hello world'
  seen = set()
  result = []
  for c in s:
      if c not in seen:
          seen.add(c)
          result.append(c)
  result = "".join(result)
  ```

- **funcy 优雅写法**:

  ```python
  from funcy import distinct
  s = 'hello world'
  result = "".join(distinct(s))
  ```

**4. 列表切块 (Chunks)** 

- **常用写法 (每 3 个一组)**:

  ```python
  nums = range(10)
  chunks = [nums[i:i+3] for i in range(0, 10, 3)]
  ```

- **funcy 优雅写法**:

  ```python
  from funcy import chunks
  nums = range(10)
  chunks = list(chunks(3, nums))
  ```

**5. 筛选符合条件的元素 (Filter)** 

- **常用写法 (筛选偶数)**:

  ```python
  nums = [1, 2, 3, 4, 5]
  even_nums = []
  for n in nums:
      if n % 2 == 0:
          even_nums.append(n)
  ```

- **funcy 优雅写法**:

  ```python
  from funcy import lfilter
  nums = [1, 2, 3, 4, 5]
  even_nums = lfilter(lambda x: x % 2 == 0, nums)
  ```



### 3.3. 异常抑制

**非 funcy 方法**: 

```python
import os

# 想删除文件，不怕文件不存在
try:
    os.remove("test.txt")
except FileNotFoundError:
    pass  # 文件不存在就忽略
```

**funcy 方法**:

```python
import os
from funcy import suppress		# suppress: 上下文管理

# 忽略FileNotFoundError，一行搞定
with suppress(FileNotFoundError):
    os.remove("test.txt")
```

> [!Note]
>
> 可同时忽略多个异常: `suppress (FileNotFoundError, PermissionError)` 



### 3.4. 函数重试 

(网络请求失败自动重试, 不用手动写循环)

**非 funcy 方法**:

```python
import time
import requests

def call_api():
    for _ in range(3):  # 重试3次
        try:
            response = requests.get("https://httpbin.org/delay/1")
            return response
        except Exception:
            time.sleep(0.1)  # 间隔0.1秒
    raise Exception("重试失败")
```

**funcy方法**:

```python
from funcy import retry
import requests

# 装饰器：重试3次，每次间隔0.1秒
@retry(tries=3, timeout=0.1)
def call_api():
    response = requests.get("https://httpbin.org/delay/1")
    return response

# 调用函数，失败自动重试
call_api()
```

> [!Note]
>
> 适合爬虫, API 调用等, 避免网络波动导致的程序崩溃



### 3.5. 类属性缓存

(耗时计算只执行一次, 提升性能)

**非 funcy 写法**: 

```python
class User:
    def __init__(self, user_id):
        self.user_id = user_id
        self._profile = None  # 缓存变量

    def get_profile(self):
        if self._profile is None:
            # 模拟耗时查询（比如查数据库）
            print("查询数据库...")
            self._profile = {"id": self.user_id, "name": "小明"}
        return self._profile

user = User(1)
user.get_profile()  # 输出：查询数据库...
user.get_profile()  # 直接返回缓存，不查数据库
```

**funcy 写法**: 

```python
from funcy import cached_property

class User:
    def __init__(self, user_id):
        self.user_id = user_id

    # 装饰器：第一次调用计算，之后直接返回缓存
    @cached_property
    def profile(self):
        print("查询数据库...")
        return {"id": self.user_id, "name": "小明"}

user = User(1)
print(user.profile)  # 输出：查询数据库... + 结果
print(user.profile)  # 直接返回缓存，不查数据库
```

> [!Note]
>
> 不用写缓存, 还能自动处理属性访问逻辑



## 4. 常见问题 & 特点

**1. funcy 和 boltons有什么区别?** 

funcy 更专注“函数式编程”, 简化循环和流程控制; boltons 功能更全 (含文件、调试等); 新手根据场景选：处理列表/字典用funcy, 需要多工具场景用boltons

**2. 性能怎么样?** 

日常数据集 (几万行/个) 完全没问题, 函数内部优化得比手动for循环更高效; 千万级数据建议测试后使用,  or 拆分步骤

**3. 适合哪些场景**

- 数据处理：列表/字典的筛选、转换、分组

- 爬虫开发：API重试、数据清洗

- 日常开发：简化循环逻辑、异常处理、性能优化 



**优点**: 

- 零门槛: 函数名直观，新手看示例就能用，不用学函数式编程概念

- 省代码: 一行替代多层for循环+判断，代码量减少80%

- 易维护: 逻辑清晰，别人一看就懂，减少bug

- 功能实用: 覆盖日常数据处理90%的场景

缺点:

- 功能单一: 主要聚焦列表/字典处理, 没有boltons的多场景覆盖

- 需记忆函数: 常用函数要记一下, 但高频的就10个左右, 容易掌握







