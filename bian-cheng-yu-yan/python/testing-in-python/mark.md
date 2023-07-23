# Mark

## 参数化

参数化测试让我们可以给测试传入多个数据集，使用pytest.marker来实现

```python
@pytest.mark.parametrize(("test_input", "expected"), [(1, 1), (2, 4), (3, 9), (4, 16)])
def test_square_args(test_input, expected):
    assert square(test_input) == expected
```

pytest会根据@mark.parametrize中提供的参数一个一个执行测试函数。

参数化可以减少测试函数的数量。对于add()函数，我们不需要对于每个输入类型创建测试函数，只需要通过参数化的测试就可以实现

```python
import pytest


def add(x, y):
    return x + y


@pytest.mark.parametrize(
    ("item1", "item2", "result"), 
    [("Tea", "Cup", "TeaCup"), 
    (2, 4, 6), 
    (20.4, 1, 21.4)]
)
def test_add(item1, item2, result):
    assert add(item1, item2) == result
```

## 测试函数的meta data

pytest自带一些markers，可以用来标记测试函数:

* skip
* skipif
* xfail

```python
@pytest.mark.skip(reason="no way of testing this!")
def test_the_unknown():
    pass


@pytest.mark.skipif(sys.version_info < (3, 3), reason="requires python 3.3+")
def test_3_3_function():
    pass


@pytest.mark.xfail
def test_do_not_test():
    assert False
```

## 自定义Markers

你可以创建自己的pytest.mark来作为选择测试函数的tag

```python
@pytest.mark.integration
def test_long_running():
    time.sleep(1)
    assert True
```

使用marker来选择测试函数

<pre class="language-bash"><code class="lang-bash">python3.11 -m pytest -m "integration and not unittests" tests
<strong>
</strong><strong>python3.11 -m pytest -m "not integration" tests
</strong></code></pre>

列出所有的markers：

```bash
python3.11 -m pytest --markers
```

你可以把你创建的marker注册到pytest.ini文件中

```ini
[pytest]
markers =
    integration: integration tests
```
