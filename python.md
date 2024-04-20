# Python基础
## 可变参数

### 位置参数（`*args`）

当在函数定义中使用 `*args`时，它将捕获所有传递给函数的位置参数，并将它们作为一个元组（tuple）存储。

例：

```python
def TYPE(*args):
    total = 0
    type_ = type(args)
    return type_
print(TYPE(1)) #<class 'tuple'>
```

### 关键字参数（`**kwargs`）

当在函数定义中使用 `**kwargs` 时，它将捕获所有传递给函数的关键字参数，并将它们作为一个字典(dict)存储。

```python
def print_info(**kwargs):
    print(type(kwargs))
    for key, value in kwargs.items():
        print(f"{key}: {value}")

print_info(name="Alice", age=30, city="New York")

# 传入字典
info = {'name': "Alice", 'age': 30, 'city': "New York"}
print_info(**info)
```

