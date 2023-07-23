# Testing in Python

Test Driven Development (TDD) 是一个依赖于循环的短期开发的软件开发过程。循环过程：

1. 给新的功能添加测试
2. 运行测试并且修改代码来通过测试
3. 如果有必要的话，重构代码
4. 重复以上步骤

测试应该遵循[Given-When-Then](https://pythontest.com/strategy/given-when-then-2/)。

比如我们想要测试这个函数

```python
# mortgage.py

def get_monthly_payment(
        principal,
        term,
        rate
):
    return principal / term
```

在测试程序中，我们可以这样写

```python
# test_mortgage.py

from mortgage import get_monthly_payment


def test_get_monthly_payment_0_rate():
    # GIVEN
    rate = 0
    principal = 1000
    term = 10

    # WHEN
    payment = get_monthly_payment(principal, term, rate)

    # THEN
    assert payment == pytest.approx(1100)
```
