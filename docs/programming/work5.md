# 5. Паттерн «Декоратор»

## Цель работы

Реализовать программу для получения курсов валют с сайта Центробанка России с использованием паттерна «Декоратор». Декораторы позволяют преобразовывать данные в различные форматы (JSON, YAML, CSV).

## Постановка задачи

Программа получает курсы валют с API ЦБ РФ и сохраняет их в трёх форматах:

- **JSON** — универсальный формат
- **YAML** — более читаемый для человека
- **CSV** — табличный формат

## Что такое паттерн «Декоратор»

**Декоратор** — это структурный паттерн проектирования, который позволяет динамически добавлять объектам новую функциональность, оборачивая их в полезные «обёртки».

## Структура проекта
```
project/
├── main.py # основная программа
├── rates.json # создаётся при запуске
├── rates.yaml # создаётся при запуске
└── rates.csv # создаётся при запуске
```
## Листинг программы

### Файл `main.py`

```python
import csv
import json
from abc import ABC, abstractmethod

import requests
import yaml

class Component(ABC):
    @abstractmethod
    def operation(self):
        pass
    
    @abstractmethod
    def save(self, filename: str):
        pass

class CurrencyComponent(Component):
    URL = "https://www.cbr-xml-daily.ru/daily_json.js"
    
    def operation(self):
        response = requests.get(self.URL, timeout=10)
        return response.json()
    
    def save(self, filename: str):
        data = self.operation()
        with open(filename, "w", encoding="utf-8") as file:
            json.dump(data, file, ensure_ascii=False, indent=4)

class Decorator(Component):
    def __init__(self, component: Component):
        self.component = component
    
    def operation(self):
        return self.component.operation()
    
    def save(self, filename: str):
        self.component.save(filename)

class YamlDecorator(Decorator):
    def operation(self):
        data = self.component.operation()
        return yaml.dump(data, allow_unicode=True)
    
    def save(self, filename: str):
        yaml_data = self.operation()
        with open(filename, "w", encoding="utf-8") as file:
            file.write(yaml_data)

class CsvDecorator(Decorator):
    def operation(self):
        data = self.component.operation()
        result = "CharCode,Name,Value\n"
        for value in data["Valute"].values():
            result += f"{value['CharCode']},{value['Name']},{value['Value']}\n"
        return result
    
    def save(self, filename: str):
        data = self.component.operation()
        with open(filename, "w", newline="", encoding="utf-8") as file:
            writer = csv.writer(file)
            writer.writerow(["CharCode", "Name", "Value"])
            for value in data["Valute"].values():
                writer.writerow([value["CharCode"], value["Name"], value["Value"]])

if __name__ == "__main__":
    component = CurrencyComponent()
    
    print("JSON:")
    print(component.operation())
    
    yaml_component = YamlDecorator(component)
    print("YAML:")
    print(yaml_component.operation())
    
    csv_component = CsvDecorator(component)
    print("CSV:")
    print(csv_component.operation())
    
    component.save("rates.json")
    yaml_component.save("rates.yaml")
    csv_component.save("rates.csv")
```

## Результаты работы

При запуске программа:

- Выводит курсы валют в форматах JSON, YAML и CSV
- Создаёт три файла: `rates.json`, `rates.yaml`, `rates.csv`

## Вывод

Реализована программа получения курсов валют с использованием паттерна «Декоратор». Декораторы позволяют легко добавлять новые форматы вывода без изменения основного кода.

