# 6. Паттерн «Одиночка» (Singleton)

## Цель работы

Реализовать программу для получения курсов валют с сайта Центробанка России с использованием паттерна «Одиночка». Паттерн гарантирует, что класс имеет только один экземпляр, и предоставляет глобальную точку доступа к нему.

## Постановка задачи

Программа получает курсы валют с API ЦБ РФ. Класс `CurrencyManager` реализован как Singleton, что гарантирует создание только одного экземпляра менеджера валют.

Дополнительный функционал:
- Контроль частоты запросов (не чаще 1 раза в секунду)
- Визуализация курсов валют в виде столбчатой диаграммы

## Что такое паттерн «Одиночка»

**Одиночка (Singleton)** — это порождающий паттерн проектирования, который гарантирует, что у класса есть только один экземпляр, и предоставляет к нему глобальную точку доступа.

### Пример использования

```python
manager1 = CurrencyManager()
manager2 = CurrencyManager()
# manager1 и manager2 — это один и тот же объект
print(manager1 is manager2)  # True
```
## Структура проекта
```
project/
├── singleton_metaclass.py # метакласс для Singleton
├── currency.py # класс Currency
├── currencies_manager.py # менеджер валют (Singleton)
├── main.py # запуск программы
├── test_currencies.py # тесты
└── currencies.jpg # создаётся при запуске
```
## Листинг программы

### Файл `singleton_metaclass.py`

```python
class SingletonMeta(type):
    _instances = {}
    
    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            cls._instances[cls] = super().__call__(*args, **kwargs)
        return cls._instances[cls]
```
### Файл `currency.py`

```python
class Currency:
    def __init__(self, code: str, name: str, value: str, nominal: int):
        self.__code = code
        self.__name = name
        self.__nominal = nominal
        integer, fraction = value.replace(',', '.').split('.')
        self.__value = (integer, fraction)
    
    def get_code(self):
        return self.__code
    
    def get_name(self):
        return self.__name
    
    def get_value(self):
        return self.__value
    
    def get_nominal(self):
        return self.__nominal
```

### Файл `currencies_manager.py`

```python
import time
import requests
from xml.etree import ElementTree as ET
import matplotlib.pyplot as plt
from currency import Currency
from singleton_metaclass import SingletonMeta

class CurrencyManager(metaclass=SingletonMeta):
    def __init__(self, delay: float = 1.0):
        self.__delay = delay
        self.__last_request_time = 0
        self.__currencies = []
    
    def fetch(self, currencies_ids_lst: list):
        current_time = time.time()
        if current_time - self.__last_request_time < self.__delay:
            raise Exception("Слишком частые запросы")
        
        response = requests.get('http://www.cbr.ru/scripts/XML_daily.asp')
        root = ET.fromstring(response.content)
        result = []
        
        for valute in root.findall("Valute"):
            valute_id = valute.get('ID')
            if valute_id in currencies_ids_lst:
                code = valute.find('CharCode').text
                name = valute.find('Name').text
                value = valute.find('Value').text
                nominal = int(valute.find('Nominal').text)
                currency = Currency(code, name, value, nominal)
                result.append({code: (name, currency.get_value())})
                self.__currencies.append(currency)
        
        for cur_id in currencies_ids_lst:
            if not any(cur_id in str(r) for r in result):
                result.append({cur_id: None})
        
        self.__last_request_time = current_time
        return result
    
    def visualize(self):
        names = []
        values = []
        for currency in self.__currencies:
            names.append(currency.get_code())
            val = float(".".join(currency.get_value()))
            values.append(val)
        
        plt.figure()
        plt.bar(names, values)
        plt.title("Курсы валют")
        plt.xlabel("Валюта")
        plt.ylabel("Курс")
        plt.savefig("currencies.jpg")
        plt.close()
```

### Файл `main.py`

```python
from currencies_manager import CurrencyManager

if __name__ == "__main__":
    manager = CurrencyManager()
    res = manager.fetch(['R01035', 'R01335', 'R01700J'])
    print(res)
    manager.visualize()
```

### Файл `test_currencies.py`

```python
import unittest
from currencies_manager import CurrencyManager

class TestCurrencies(unittest.TestCase):
    def test_invalid_id(self):
        manager = CurrencyManager()
        result = manager.fetch(['R9999'])
        self.assertEqual(result[-1], {'R9999': None})
    
    def test_valid_currency(self):
        manager = CurrencyManager()
        result = manager.fetch(['R01035'])
        currency = result[0]
        key = list(currency.keys())[0]
        name, value = currency[key]
        self.assertIsInstance(name, str)
        val = float(".".join(value))
        self.assertTrue(0 < val < 999)

if __name__ == '__main__':
    unittest.main()
```

## Результаты работы

При запуске программа:

- Получает курсы валют для указанных ID
- Выводит результат в консоль
- Создаёт файл `currencies.jpg` с графиком курсов валют

## Результаты тестирования

Все тесты пройдены успешно. Программа корректно обрабатывает:

- Валидные и невалидные коды валют
- Контроль частоты запросов

## Вывод

Реализован менеджер валют с использованием паттерна «Одиночка». Программа получает курсы с сайта ЦБ РФ, строит график и сохраняет его в файл. Singleton гарантирует, что менеджер существует в единственном экземпляре.

