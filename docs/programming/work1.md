# 1. Two Sum (Поиск двух чисел)

## Цель работы

Разработать программу на Python для поиска двух элементов массива, сумма которых равна заданному числу. Реализовать модульные тесты для проверки корректности работы.

## Постановка задачи

Дан массив целых чисел `nums` и целое число `target`. Нужно вернуть индексы двух чисел, сумма которых равна `target`.

### Условия

- Существует ровно одно решение
- Один и тот же элемент нельзя использовать дважды
- Ответ можно вернуть в любом порядке

### Примеры

| Ввод | Вывод |
|------|-------|
| `nums = [2,7,11,15], target = 9` | `[0,1]` |
| `nums = [3,2,4], target = 6` | `[1,2]` |
| `nums = [3,3], target = 6` | `[0,1]` |

## Алгоритм решения

Используется метод полного перебора:

1. Получить длину массива
2. Внешним циклом перебрать первый элемент
3. Внутренним циклом перебрать все элементы после первого
4. Если сумма двух элементов равна `target` — вернуть их индексы
5. Если пара не найдена — вернуть пустой список

## Листинг программы

### Файл `main.py`

```python
from typing import List

class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        n = len(nums)
        for i in range(n):
            for j in range(i + 1, n):
                if nums[i] + nums[j] == target:
                    return [i, j]
        return []
```

### Файл `test_main.py`

```python
import unittest
from main import Solution

class TestSolution(unittest.TestCase):
    def setUp(self):
        self.solution = Solution()

    def test_basic_case(self):
        result = self.solution.twoSum([2, 7, 11, 15], 9)
        self.assertIn(result, [[0, 1], [1, 0]])

    def test_negative_numbers(self):
        result = self.solution.twoSum([-1, -2, -3, -4, -5], -8)
        self.assertIn(result, [[2, 4], [4, 2]])

    def test_duplicate_numbers(self):
        result = self.solution.twoSum([3, 3, 4, 5], 6)
        self.assertIn(result, [[0, 1], [1, 0]])

    def test_no_solution(self):
        result = self.solution.twoSum([1, 2, 3, 4], 10)
        self.assertEqual(result, [])

if __name__ == '__main__':
    unittest.main()
```



## Результаты тестирования
Все тесты пройдены успешно. Программа корректно обрабатывает:

Обычные случаи

Отрицательные числа

Повторяющиеся элементы

Отсутствие решения

## Вывод
Реализована программа поиска двух элементов массива, сумма которых равна заданному числу. Разработаны тесты, подтверждающие корректность работы.
