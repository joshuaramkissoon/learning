# Testing in Python

## Test Driven Development
TDD is a software development process that relies on the repition of short development cycles. The typical development cycle for TDD is:
1. Add tests for a new or updated part of the functionality
2. Run all tests and verify that the new tests fail
3. Write or modify code to pass the tests
4. Run the tests to verify that all tests pass
5. Refactor code if necessary
6. Repeat

## Test Suites
At its simplest, all we need for testing are functions that start with `test_`. We can group related `test_` functions in `test_` files and group these files in directories to create test suites. To run a test suite, call `python -m pytest`.

## Testing Exceptions
### SUT: A greeting function

```python
def greet(name):
    if not isinstance(name, str):
        raise TypeError(f'{name} is not a string')
    return f'Hello, {name}!'
```

We can test the exception using `pytest.raises`:

```python
import pytest

def test_greet_with_invalid_number():
    with pytest.raises(TypeError):
        greet(42)

# This test should make sure that greet(42) raises a TypeError
```

It's useful to examine the exception after as well. The error message can be validated using the `match` kwarg in `pytest.raises`:

```python
def test_greet_with_invalid_number():
    with pytest.raises(TypeError, match=r'.* is not a string'):
        greet(42)
```

## Fixtures
Fixtures are functions that run before each test function that it is applied to. They are used to feed data into tests. They are reusable, can be created with a range of initializers and facilitate teardown to release resources once a test is finished. Fixtures can be stored in a `conftest.py` file.

Consider the example of a `Box` class with a `bigger_than_a_breadbox` method.

```python
class Box:
    def __init__(self, height, width, length):
        self.height = height
        self.width = width
        self.length = length
        self.volume = height*width*length

    def bigger_than_a_breadbox(self):
        BREADBOX_VOLUME = 16 * 8 * 9
        return self.volume > BREADBOX_VOLUME
```

We want to be able to create different box objects that can be re-used in our tests. For example, it would be useful to have a reusable `shipping_container` or `shoebox` etc. We can define a fixture and have it return the desired box:

```python
import pytest
from box import Box

@pytest.fixture(name="shipping_container")
def shipping_container_fixture():
    return Box(height=102, width=96, length=240)

def test_bigger_than_breadbox(shipping_container):
    assert shipping_container.bigger_than_a_breadbox()
```

We can pass the `shipping_container` object into the test function and use it easily. We can also use fixtures to construct other fixtures.

### Fixture Teardown
Use `yield` to separate creation from teardown. If nothing needs to be returned or passed to the test function, leave the `yield` as is.

```python
import os

@pytest.fixture(name="write_file")
def write_file_fixture():
    # Setup code
    FILE_NAME = "test.txt"
    print("Open file")
    my_file = open(FILE_NAME, "w")

    # Yield control back to test
    yield my_file

    # Teardown code
    print("Close file")
    my_file.close()
    print("Remove file")
    os.remove(FILE_NAME)
```

## Parametrization
Parametrized testing allows multiple data sets to be tested through the same test. To do this, use `pytest.mark.parametrize`. Pytest will execute the test for each set of arguments provided.

```python
@pytest.mark.parametrize(('test_input', 'expected'), [(1,1), (2,4), (3,9), (4,16)])
def test_square_args(test_input, expected):
    assert square(test_input) == expected
```

Parametrization helps reduce the number of tests since instead of using separate tests for different input types, we can group them into one test function (as seen in add below):

```python
def add(x,y):
    return x + y

@pytest.mark.parametrize(
    ("item1", "item2", "result"), [("Tea", "Cup", "TeaCup"), (2, 4, 6), (20.4, 1, 21.4)]
)
def test_add(item1, item2, result):
    assert add(item1, item2) == result
```

## Skipping Tests
Tests can be skipped using `@pytest.mark.skip` or conditionally skipped using `@pytest.mark.skipif(condition, reason)` where `reason` is a string explaining why the test is being skipped.

# Mocking
Useful article for an intro to mocking, why it's used and a simple example [here](https://medium.com/analytics-vidhya/mocking-in-python-with-pytest-mock-part-i-6203c8ad3606).

## Why Mock?
Mocking is used to produce predictable output of a service or function to test how a function of interest responds to different output states.

**Example**: If you've built a service that collects stock market data and gives information about the top gainers in a sector. You get the stock market information from a third party API and process it to give out the results. To test the code, you don't want to hit the API every time (this will make the tests slower and can have quota implications). A mock replaces a function with a dummy whose behaviour you program. This is also called _patching_.

## Mocking a simple function
Assume we had a function that fetched the operating system.

```python
def get_os():
    return 'Windows' if is_windows() else 'Linux'
```

This calls `is_windows()` which executes some complext logic and takes some time.

```python
def is_windows():
    sleep(5)
    return True
```

Testing this function takes at least 5 seconds. Unit tests should be fast - we should be able to run hundreds of tests in seconds.

**Mocking helps produce predictable output without waiting for complex time-consuming logic to be performed.**

The `pytest-mock` library provides an easy interface for mocking (`unittest.patch` can also be used to replace functions with mocks). Assuming we wanted `is_windows` to return `True` without taking 5 seconds each time, we can patch/mock the `is_windows` function:

```python
import pytest
from simple_mock import get_os

def test_get_os(mocker):
    mocker.patch('simple_mock.is_windows', return_value=True) # Simple_mock has a function named is_windows
    assert get_os() == 'Windows'
```

Pytest-mock provides a fixture called `mocker` that we can usue by passing it as an argument to the test function and using the `patch` method from it. We can also change the return value of the `is_windows` mock to see how our `get_os` function handles it (without having to actually switch to a Linux machine).

When a function is patched, a new mocked function is called and the original function is bypassed.
