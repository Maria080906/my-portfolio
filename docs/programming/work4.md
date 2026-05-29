# 4. Итераторы и сопрограммы

## Цель работы

Реализовать итератор для фильтрации чисел Фибоначчи из списка и сопрограмму (генератор) для выдачи первых N чисел Фибоначчи.

## Постановка задачи

1. **Итератор** `FibonacchiLst` принимает список чисел и при переборе возвращает только те, которые являются числами Фибоначчи.
2. **Сопрограмма** `my_genn` принимает через `send(n)` число `n` и возвращает список первых `n` чисел Фибоначчи.

## Что такое числа Фибоначчи

Последовательность, где каждое следующее число равно сумме двух предыдущих:
0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, ...

## Проверка, является ли число числом Фибоначчи

Число `x` является числом Фибоначчи, если `5*x² + 4` или `5*x² - 4` является полным квадратом.

## Структура проекта
project/
├── fibonacci_lst.py # итератор
├── gen_fib.py # сопрограмма
├── test_fib_it.py # тесты для итератора
└── test_fib.py # тесты для генератора
## Листинг программы

### Файл `fibonacci_lst.py` (итератор)

```python
from typing import Iterable, List

def is_fibonacci_number(x: int) -> bool:
    if x < 0:
        return False
    def is_square(num: int) -> bool:
        root = int(num ** 0.5)
        return root * root == num
    return is_square(5 * x * x + 4) or is_square(5 * x * x - 4)

class FibonacchiLst:
    def __init__(self, data: Iterable[int]) -> None:
        self.data = list(data)
        self.index = 0
    
    def __iter__(self):
        return self
    
    def __next__(self) -> int:
        while self.index < len(self.data):
            current = self.data[self.index]
            self.index += 1
            if is_fibonacci_number(current):
                return current
        raise StopIteration
```

### Файл `gen_fib.py` (сопрограмма)

```python
from typing import Generator, List

def fib_sequence(n: int) -> List[int]:
    result = []
    a, b = 0, 1
    for _ in range(n):
        result.append(a)
        a, b = b, a + b
    return result

def my_genn() -> Generator[List[int], int, None]:
    n = yield
    while True:
        if n < 0:
            raise ValueError("n должно быть >= 0")
        fib_list = fib_sequence(n)
        n = yield fib_list
```

### Файл `test_fib_it.py` (тесты итератора)

```python
from fibonacci_lst import FibonacchiLst

def test_normal_case():
    result = list(FibonacchiLst([0, 1, 2, 3, 4, 5, 6, 7, 8, 9]))
    assert result == [0, 1, 2, 3, 5, 8]

def test_empty_list():
    result = list(FibonacchiLst([]))
    assert result == []

def test_single_element():
    result = list(FibonacchiLst([4]))
    assert result == []
    result = list(FibonacchiLst([1]))
    assert result == [1]
```
### Файл `test_fib.py` (тесты сопрограммы)

```python
import pytest
from gen_fib import my_genn

def test_fib_positive():
    gen = my_genn()
    next(gen)
    assert gen.send(3) == [0, 1, 1]
    assert gen.send(5) == [0, 1, 1, 2, 3]

def test_fib_zero():
    gen = my_genn()
    next(gen)
    assert gen.send(0) == []

def test_fib_negative():
    gen = my_genn()
    next(gen)
    with pytest.raises(ValueError):
        gen.send(-1)
```
## Результаты тестирования

Все тесты пройдены успешно. Программа корректно обрабатывает:

- Обычные списки (возвращает только числа Фибоначчи)
- Пустой список
- Элементы, не являющиеся числами Фибоначчи
- Получение первых N чисел Фибоначчи (n = 0, 3, 5)
- Ошибку при отрицательном n

## Вывод

Реализованы итератор для фильтрации чисел Фибоначчи и сопрограмма для генерации последовательности. Тесты подтверждают корректность работы в различных сценариях.
