# Question

Today we’re going to build a basic spreadsheet application like Google sheets or Excel but much simpler. Our spreadsheet, let’s call it OpenSheet, will only support cells which hold either integers or formulas that sum two cells.

You are tasked with writing a program that handles this functionality for OpenSheet. You can make any decisions you want regarding how this program is organized, but there must be some sort of setter/getter methods that can be called by the application for any given cell. All inputs will be strings.

For setting you can expect two inputs: the cell location and the cell value.

Example of how your setter could look

```
set_cell("C1", "45")
set_cell("B1", "10")
set_cell("A1", "=C1+B1")
```

For getting you will be provided one input that is the cell location.

Example of how your getter could look

```
get_cell("A1") # should return 55 in this case
```

Assumptions:

- In memory storage
- All cell location inputs will be well formed (no need to validate in code)
- All cell value inputs will be well formed (no need to validate in code)
- Cells value inputs are either a summation of two other cells or an int
- Empty cells are treated as zero when accessed

# Solutions

One of the solutions found on the internet is as below, so basically

1. store different types of cells separately
2. replace the formula cell with valued cell for evaluation when getting the cell

```python
import re

class Spreadsheet:

    def __init__(self):
        self.cells = {}
        self.formula_cells = {}

    def set_cell(self, cell, value):
        self._clear_all(cell)
        if value.startswith("="):
            self.formula_cells[cell] = value
        else:
            self.cells[cell] = value

    def _evaluate_formula(self, value):
        formula = value
        for cell in re.findall(r'[A-Z][0-9]+', value):
            formula = formula.replace(cell, self.cells[cell])
        return eval(formula[1:])

    def get_cell(self, cell):
        if cell in self.cells:
            return int(self.cells[cell])
        elif cell in self.formula_cells:
            value = self.formula_cells[cell]
            return self._evaluate_formula(value)
        return 0

    def _clear_all(self, cell):
        if cell in self.cells:
            self.cells.pop(cell)
        if cell in self.formula_cells:
            self.formula_cells.pop(cell)

ss = Spreadsheet()

ss.set_cell("A1", "13")
ss.set_cell("A2", "14")
assert ss.get_cell("A1") == 13
ss.set_cell("A3", "=A1+A2")
assert ss.get_cell("A3") == 27
```

> [!WARNING]
> The problem with this solution is that it doesn’t consider the case that the items in formula cell could be another formula cell, like:

```python
ss.set_cell("A1", "13")
ss.set_cell("A2", "14")
ss.set_cell("A3", "=A1+A2")

# will raise KeyError: 'A3'
ss.set_cell("A4", "=A3+A1") 
```

And the fix is to call “_evaluate_formula” recursively

```python
    def _evaluate_formula(self, value):
        formula = value
        for cell in re.findall(r'[A-Z][0-9]+', value):
            cell_value = 0
            if cell in self.cells:
                cell_value = self.cells[cell]
            elif cell in self.formula_cells:
                cell_value = self._evaluate_formula(self.formula_cells[cell])
            formula = formula.replace(cell, str(cell_value))
        return eval(formula[1:])
```

> [!WARNING]
> Another corner case is “circular dependency”

```python
ss.set_cell("A1", "13")
ss.set_cell("A2", "14")
ss.set_cell("A3", "=A1+A2")

# will raise RecursionError
ss.set_cell("B1", "=A1+C1")
ss.set_cell("C1", "=B1+A2")
ss.set_cell("B2", "=B2+A3")
```

To fix this, record the visited cell when evaluating each time and raise an error if “circular dependency” is found:

```python
    def __init__(self):
        ...
        self.visited = set()

    def _evaluate_formula(self, value):
        formula = value
        for cell in re.findall(r'[A-Z][0-9]+', value):
            cell_value = 0
            if cell in self.cells:
                cell_value = self.cells[cell]
            elif cell in self.formula_cells:
                if cell in self.visited:
                    raise ValueError("Circular Dependency!")
                self.visited.add(cell)
                cell_value = self._evaluate_formula(self.formula_cells[cell])
            formula = formula.replace(cell, str(cell_value))
        return eval(formula[1:])

    def get_cell(self, cell):
        self.visited.clear()
        if cell in self.cells:
            return int(self.cells[cell])
        elif cell in self.formula_cells:
            value = self.formula_cells[cell]
            return self._evaluate_formula(value)
        return 0
```

> [!TIP]
> Another enhancement could be done to improve performance is by catching the value instead of recalculated every time. This adds some complexity b/c once one cell change, all the value of cells depends on it will change as well, and the cached value will be invalid anymore, to achieve this we need to maintain the dependencies, given a cell knows which cells deps on it.

Here is another solution, deps is used to maintain the cell sets that rely on specific cell, so that once we update this specific cell, we can clear the cache for those cells rely on it. Another thing need to notice is that when removing the cache, there could be chance that it has “circular dependency” as well, so should check that too.

```python
from collections import defaultdict

class Cell:

    def __init__(self, key, expr):
        self.key = key
        self.value = self.left_key = self.right_key = None
        self._parse(expr)

    def _parse(self, expr):
        if expr.isdigit():
            self.value = int(expr)
        else:
            self.left_key, self.right_key = expr[1:].split("+")

class Spreadsheet:

    def __init__(self):
        self.key_to_cell = {}
        self.visited = set()
        self.cache = {}
        self.deps = defaultdict(set)

    def set_cell(self, key, expr):
        if key in self.key_to_cell:
            self._clean_cache(key)
            self._remove_deps(self.key_to_cell[key])
            self.key_to_cell.pop(key)

        cell = Cell(key, expr)
        self._add_deps(cell)
        self.key_to_cell[key] = cell

    def _add_deps(self, cell):
        if cell.value is None:
            self.deps[cell.left_key].add(cell.key)
            self.deps[cell.right_key].add(cell.key)

    def _remove_deps(self, cell):
        if cell.value is None:
            self.deps[cell.left_key].remove(cell.key)
            self.deps[cell.right_key].remove(cell.key)

    def _clean_cache(self, key):
        self.visited.clear()
        self._do_clean_cache(key)
        
    def _do_clean_cache(self, key):
        if key in self.cache:
            self.cache.pop(key)
            
        if key not in self.deps: return
        if key in self.visited: return

        for k in self.deps[key]:
            self.visited.add(k)
            self._do_clean_cache(k)

    def get_cell(self, key):
         self.visited.clear()
         try:
            return self._evaluate(key)
         except ValueError as e:
            print(f"error fetching value for {key}: {e}")
            return 0

    def _evaluate(self, key):
        if key not in self.key_to_cell:
            return 0
        
        if key in self.cache:
            return self.cache[key]
        
        cell = self.key_to_cell[key]
        if cell.value is not None:
            return cell.value
        
        if cell.key in self.visited:
            raise ValueError("Circular Dependencies!")
            return 0

        self.visited.add(cell.key)
        res = self._evaluate(cell.left_key) + self._evaluate(cell.right_key)
        self.cache[key] = res
        return res

ss = Spreadsheet()

ss.set_cell("A1", "13")
ss.set_cell("A2", "14")
assert ss.get_cell("A1") == 13
ss.set_cell("A3", "=A1+A2")
assert ss.get_cell("A3") == 27

ss.set_cell("A4", "=A3+A1")
assert ss.get_cell("A4") == 40

ss.set_cell("B1", "=A1+C1")
ss.set_cell("C1", "=B1+A2")
ss.get_cell("B1")

ss.set_cell("A4", "=A2+A1")
assert ss.get_cell("A4") == 27

ss.set_cell("A2", "15")
assert ss.get_cell("A4") == 28
```