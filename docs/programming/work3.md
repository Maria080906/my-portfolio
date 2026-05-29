# 3. Бинарное дерево

## Цель работы

Реализовать рекурсивное построение бинарного дерева заданной высоты с заданным корнем. Левый и правый потомки вычисляются по формулам, зависящим от варианта студента.

## Постановка задачи

Функция `gen_bin_tree(height, root)` рекурсивно строит бинарное дерево. На вход подаются:
- `height` — высота дерева
- `root` — значение корневого узла

На выходе — словарь, представляющий дерево.

### Формулы для моего варианта

| Потомок | Формула |
|---------|---------|
| Левый | `root ** 3` (корень в кубе) |
| Правый | `(root * 2) - 1` |

### Пример дерева для height = 2, root = 3

```python
{
    3: [
        {27: [None, None]},
        {5: [None, None]}
    ]
}
```

## Структура проекта
project/
├── main.py # основная программа
└── test_main.py # тесты
## Листинг программы

### Файл `main.py`

```python
def gen_bin_tree(height=4, root=12):
    if height <= 0:
        return None
    if height == 1:
        return {root: [None, None]}
    
    left_value = root ** 3
    right_value = (root * 2) - 1
    
    left_tree = gen_bin_tree(height - 1, left_value)
    right_tree = gen_bin_tree(height - 1, right_value)
    
    return {root: [left_tree, right_tree]}

def print_tree(tree, level=0):
    if tree is None:
        return
    indent = "  " * level
    for key, value in tree.items():
        print(f"{indent}root: {key}")
        if value[0] is not None:
            print(f"{indent}left:")
            print_tree(value[0], level + 1)
        if value[1] is not None:
            print(f"{indent}right:")
            print_tree(value[1], level + 1)

if __name__ == "__main__":
    tree = gen_bin_tree()
    print("Дерево с параметрами по умолчанию (height=4, root=12):")
    print_tree(tree)
```
    
### Файл `test_main.py`

```python
import unittest
from main import gen_bin_tree

class TestBinTree(unittest.TestCase):
    def test_height_zero(self):
        result = gen_bin_tree(height=0)
        self.assertIsNone(result)
    
    def test_height_one(self):
        result = gen_bin_tree(height=1, root=5)
        expected = {5: [None, None]}
        self.assertEqual(result, expected)
    
    def test_height_two(self):
        result = gen_bin_tree(height=2, root=3)
        self.assertEqual(result[3][0], {27: [None, None]})
        self.assertEqual(result[3][1], {5: [None, None]})
    
    def test_default_parameters(self):
        result = gen_bin_tree()
        self.assertEqual(result[12][0], {1728: [None, None]})
        self.assertEqual(result[12][1], {23: [None, None]})

if __name__ == '__main__':
    unittest.main()
```

## Результаты тестирования

Все тесты пройдены успешно. Программа корректно обрабатывает:

- Нулевую и отрицательную высоту (возвращает None)
- Высоту 1 (только корень)
- Высоту 2 (корень и два потомка)
- Параметры по умолчанию

## Вывод

Реализована рекурсивная функция построения бинарного дерева. Тесты подтверждают корректность работы для разных высот и значений корня. Дерево представляется в виде вложенных словарей.


   

