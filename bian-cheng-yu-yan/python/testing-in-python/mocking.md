# Mocking

假设以上场景：你需要测试touch\_directory函数，这个函数创建一个目录。如果目录存在或者被成功创建，返回True，如果创建失败，函数返回False。

```python
# touch_dir.py
import os


def touch_directory(dir_path):
    """Create a directory if it does not exist already."""
    if os.path.exists(dir_path):
        return True

    try:
        os.mkdir(dir_path)
        return True

    except PermissionError:
        return False
```

## Mocking的Use case

单元测试需要有以下特性：

* 快速的运行时间
* 没有额外的以来
* 对代码路径的覆盖

上面的代码中，mkdir这个函数让我们很难满足这些特性，相似的情况还有数据库连接和服务连接。

### 选择1

可以可以自己来模拟这些函数的执行。

```python
import touch_dir


class PathDouble:
    def exists(self, path):
        print("Calling PathDouble.exists")
        return False


class OsDouble:
    def mkdir(self, path):
        print("Calling OsDouble.mkdir")
        return True


def test_directory_does_not_exists_creation_successful():
    # GIVEN
    touch_dir.os = OsDouble()  # patch os with our double
    touch_dir.os.path = PathDouble()  # patch path with our double
    # WHEN
    result = touch_dir.touch_directory("mydir")  # calls our doubles
    # THEN
    assert result is True
```

### 选择2：Mock

Mock对象天生就是一个模拟，我们可以用它来

* 模拟函数并返回一个指定的值
* 模拟属性并返回一个指定的值
* Mock对象的所有函数和属性，如果没有定义的话，都会返回另一个Mock对象

## Mock对象

### Mock的return values

Mock对象的return\_value参数可以设定Mock作为一个函数模拟的时候的返回值

```python
m = mock.Mock(return_value=False)
m()                           # returns False
m(1,2, some_key="some value") # returns False
```

我们可以用来模拟函数

同时，我们可以判断一个Mock对象有没有被调用过

```python
m = mock.Mock()
m.assert_called_once()  # AssertionError: Expected 'mock' to have been called once. Called 0 times.
m()                     # returns a Mock
m.assert_called_once()  # succeeds
```

### Side Effects

Mock对象可以抛出异常，返回一系列值或者调用函数来提供一个返回值。我们可以通过side\_effect关键词来设置，side\_effect的值可以是一个可以遍历的结构，可以是一个异常，或者一个函数

```python
mock_iterable  = mock.Mock(side_effect = [1, 2, 3])        # returns values one at a time
mock_exception = mock.Mock(side_effect = TypeError)        # mock throws an exception
mock_function  = mock.Mock(side_effect = lambda x : 2 * x) # mock now requires an argument
```

我们可以用来模拟异常和更复杂的函数结果

### 特殊方法

Mock对象不会实现特殊方法，类似len, `subscript`, `add`/`subtract` 这些方法是不行的。

```python
len(mock.Mock())  # TypeError: object of type 'Mock' has no len()
```

但是MagicMock，一个Mock的子类，实现了这些特殊方法

```python
len(mock.MagicMock())  #  returns 0
```

### 限制Mock和MagicMock

我们可以使用一个类型来作为MagicMock的参数，来限制他可以实现的特殊方法，比如我们可以传入int类型

```python
int_magic = mock.MagicMock(int)
-int_magic     # calls __neg__(), returns a MagicMock
len(int_magic) # TypeError
```

我们还可以通过unnitest.mock.seal来防止Mock对象创建新的属性

```python
m = mock.Mock()
m.a          # returns a Mock
unittest.mock.seal(m)
m.b          # Attribute Error
```

## Patch.object

unittest.mock.patch.object可以给一个对象的属性打补丁，并在返回后恢复原状。

* 生成一个mock对象，可以用autospec关键词来限制
* 比手动补丁更安全
* 支持context manager和decorator

### `patch.object` Context Manager

```python
from unittest import mock
from unittest.mock import patch
import touch_dir


def test_directory_does_not_exist_creation_successful():
    # GIVEN
    with patch.object(touch_dir.os.path, "exists", autospec=True) as mock_exists:
        with patch.object(touch_dir.os, "mkdir", autospec=True) as mock_mkdir:
            mock_exists.return_value = False
            # WHEN
            actual_result = touch_dir.touch_directory("mydir")
            # THEN
            assert actual_result is True
```

### `patch.object` Decorator

```python
from unittest import mock
from unittest.mock import patch
import touch_dir


@patch.object(touch_dir.os.path, "exists", autospec=True)
@patch.object(touch_dir.os, "mkdir", autospec=True)
def test_directory_does_not_exist_creation_successful_decorator(
    mock_mkdir, mock_exists
):
    # GIVEN
    mock_exists.return_value = False
    # WHEN
    actual_result = touch_dir.touch_directory("mydir")
    # THEN
    assert actual_result is True
```
