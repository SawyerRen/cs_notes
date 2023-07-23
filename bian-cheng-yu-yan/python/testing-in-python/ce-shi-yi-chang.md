# 测试异常

一个greeting函数：

```python
def greet(name):
    if not isinstance(name, str):
        raise TypeError(f"{name} is not a string")

    return f"Hello, {name}!"
```

我们可以使用`pytest.raises`来测试异常

```python
import pytest

def test_greet_with_invalid_number():
    with pytest.raises(TypeError):
        greet(42)
```

pytest.raises会返回一个ExceptionInfo对象，这个对象的属性主要有type, value和traceback。

```python
def test_greet_with_invalid_number():
    with pytest.raises(TypeError) as exc_info:
        greet(42)

    assert str(exc_info.value) == "42 is not a string"
```

我们可以用match来更好的处理错误信息

```python
def test_greet_with_invalid_number():
    with pytest.raises(TypeError, match=r".* is not a string") as exc_info:
        greet(42)
```
