# Fixtures1

想象一下场景：我们要测试一下函数`bigger_than_a_breadbox`

```python
class Box:
    def __init__(self, height, width, length):
        self.height = height
        self.width = width
        self.length = length

    def volume(self):
        return self.height * self.width * self.length

    def bigger_than_a_breadbox(self):
        BREADBOX_VOLUME = 16 * 8 * 9
        return self.volume() > BREADBOX_VOLUME
```

我们可以用fixture来设定盒子的大小

```python
import pytest
from box import Box


@pytest.fixture(name="shipping_container")
def shipping_container_fixture():
    return Box(height=102, width=96, length=240)


@pytest.fixture(name="jewelry_box")
def jewelry_box_fixture():
    return Box(height=2, width=7, length=11)


@pytest.fixture(name="shoebox")
def shoebox_fixture():
    return Box(height=6, width=5, length=19)


def test_bigger_than_breadbox(shipping_container):
    assert shipping_container.bigger_than_a_breadbox()


def test_not_bigger_than_breadbox(jewelry_box, shoebox):
    assert not jewelry_box.bigger_than_a_breadbox()
    assert not shoebox.bigger_than_a_breadbox()
```

我们还可以在fixtures中使用fixtures

```python
import pytest
from box import Box


@pytest.fixture(name="breadbox")
def breadbox_fixture():
    return Box(height=8, width=16, length=9)


@pytest.fixture(name="double_breadbox")
def double_breadbox_fixture(breadbox):
    breadbox.width *= 2
    return breadbox


def test_double_breadbox(double_breadbox):
    assert double_breadbox.bigger_than_a_breadbox()
```
