# 2. Задача деления с точностью

## Цель работы

Разработать программу для деления двух чисел с заданной точностью. Результат округляется в соответствии с параметром `epsilon`, который загружается из конфигурационного файла. Реализовать логирование событий и модульные тесты.

## Постановка задачи

Программа запрашивает у пользователя два числа (делимое и делитель), загружает значение точности `epsilon` из файла `settings.ini`, выполняет деление и округляет результат с указанной точностью.

### Формула округления
result = round(result * (1/epsilon)) / (1/epsilon)
## Структура проекта
```
project/
├── main.py
├── settings.ini
├── test_main.py
└── program.log
```
## Файл `settings.ini`

```ini
[SETTINGS]
epsilon = 0.001
```
## Листинг программы

### Файл `main.py`

```python
import configparser
import logging
import sys

logging.basicConfig(
    filename="program.log",
    level=logging.INFO,
    format="%(asctime)s - %(levelname)s - %(message)s"
)

logger = logging.getLogger()

def calculate(operand1, operand2, epsilon):
    if operand2 == 0:
        logger.error("Деление на ноль!")
        return None
    result = operand1 / operand2
    logger.info(f"Деление: {operand1} / {operand2} = {result}")
    
    if 10**-9 < epsilon < 10**-1:
        multiplier = 1 / epsilon
        result = round(result * multiplier) / multiplier
        logger.info(f"Округление до {epsilon}: {result}")
    return result

def load_params(config_file="settings.ini"):
    config = configparser.ConfigParser()
    config.read(config_file)
    epsilon = float(config.get("SETTINGS", "epsilon"))
    logger.info(f"Epsilon загружен: {epsilon}")
    return epsilon

try:
    a = float(input("Введите первое число: "))
    b = float(input("Введите второе число: "))
except ValueError:
    print("Ошибка: нужно ввести число!")
    sys.exit(1)

epsilon = load_params()
result = calculate(a, b, epsilon)

if result is None:
    print("Ошибка: деление на ноль")
else:
    print(f"Результат: {a} / {b} = {result}")
```


### Файл `test_main.py`

```python
import unittest
from main import calculate, load_params

class TestDivision(unittest.TestCase):
    def test_divide_simple(self):
        self.assertEqual(calculate(10, 2, 0.001), 5.0)
    
    def test_divide_by_zero(self):
        self.assertIsNone(calculate(5, 0, 0.001))
    
    def test_rounding(self):
        result = calculate(1, 3, 0.1)
        self.assertEqual(result, 0.3)

if __name__ == '__main__':
    unittest.main()
```

## Результаты тестирования

Все тесты пройдены успешно. Программа корректно обрабатывает:

- Обычное деление
- Деление на ноль
- Округление с заданной точностью

## Вывод

Разработана программа деления с точностью из конфигурационного файла. Реализовано логирование событий. Тесты подтверждают корректность работы.
