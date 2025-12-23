from typing import List, Union

class Matrix:
    def __init__(self, data: List[List[Union[int, float]]]) -> None:
        if not data:
            raise ValueError("Matrix cannot be empty")
        
        row_length = len(data[0])
        for row in data:
            if len(row) != row_length:
                raise ValueError("All rows must have the same length")
        
        self._data: List[List[Union[int, float]]] = data
        self._rows: int = len(data)
        self._cols: int = row_length
    
    def __add__(self, other: 'Matrix') -> 'Matrix':
        if self._rows != other._rows or self._cols != other._cols:
            raise ValueError("Matrices must have the same dimensions for addition")
        
        result: List[List[Union[int, float]]] = []
        for i in range(self._rows):
            row: List[Union[int, float]] = []
            for j in range(self._cols):
                row.append(self._data[i][j] + other._data[i][j])
            result.append(row) 
        return Matrix(result)
    
    def __mul__(self, other: Union['Matrix', int, float]) -> 'Matrix':
        if isinstance(other, (int, float)):
            result: List[List[Union[int, float]]] = []
            for i in range(self._rows):
                row: List[Union[int, float]] = []
                for j in range(self._cols):
                    row.append(self._data[i][j] * other)
                result.append(row)
            return Matrix(result)
        
        elif isinstance(other, Matrix):
            if self._cols != other._rows:
                raise ValueError("Invalid matrix dimensions for multiplication")
            result = []
            for i in range(self._rows):
                row: List[Union[int, float]] = []
                for j in range(other._cols):
                    element: Union[int, float] = 0
                    for k in range(self._cols):
                        element += self._data[i][k] * other._data[k][j]
                    row.append(element)
                result.append(row)
            return Matrix(result)
        
        else:
            raise TypeError("Unsupported operand type for multiplication")
    
    def __rmul__(self, scalar: Union[int, float]) -> 'Matrix':
        return self * scalar
    
    def transpose(self) -> 'Matrix':
        result: List[List[Union[int, float]]] = []
        for j in range(self._cols):
            row: List[Union[int, float]] = []
            for i in range(self._rows):
                row.append(self._data[i][j])
            result.append(row)
        return Matrix(result)
    
    def determinant(self) -> Union[int, float]:
        if self._rows != self._cols:
            raise ValueError("Determinant is only defined for square matrices")
        n = self._rows
        if n == 1:
            return self._data[0][0]
        if n == 2:
            return (self._data[0][0] * self._data[1][1] - 
                    self._data[0][1] * self._data[1][0])
        det: Union[int, float] = 0
        for j in range(n):
            det += ((-1) ** j) * self._data[0][j] * self._minor(0, j)
        return det
    
    def _minor(self, row: int, col: int) -> Union[int, float]:
        minor_data: List[List[Union[int, float]]] = []
        for i in range(self._rows):
            if i == row:
                continue
            minor_row: List[Union[int, float]] = []
            for j in range(self._cols):
                if j == col:
                    continue
                minor_row.append(self._data[i][j])
            minor_data.append(minor_row)
        if len(minor_data) == 1:
            return minor_data[0][0]
        return Matrix(minor_data).determinant()
    
    def get_shape(self) -> tuple[int, int]:
        return (self._rows, self._cols)
    
    def __repr__(self) -> str:
        return f"Matrix({self._data})"