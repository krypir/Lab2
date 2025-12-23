from typing import Dict, Any, List, Union

def create_matrix(data: List[List[Union[int, float]]]) -> Dict[str, Any]:
    if not data:
        raise ValueError("Matrix cannot be empty")
    row_length = len(data[0])
    for row in data:
        if len(row) != row_length:
            raise ValueError("All rows must have the same length")
    return {
        "data": data,
        "rows": len(data),
        "cols": row_length
    }

def matrix_add(m1: Dict[str, Any], m2: Dict[str, Any]) -> Dict[str, Any]:
    if m1["rows"] != m2["rows"] or m1["cols"] != m2["cols"]:
        raise ValueError("Matrices must have the same dimensions")
    result: List[List[Union[int, float]]] = []
    for i in range(m1["rows"]):
        row: List[Union[int, float]] = []
        for j in range(m1["cols"]):
            row.append(m1["data"][i][j] + m2["data"][i][j])
        result.append(row)
    return create_matrix(result)

def matrix_multiply(m1: Dict[str, Any], m2: Dict[str, Any]) -> Dict[str, Any]:
    if m1["cols"] != m2["rows"]:
        raise ValueError("Invalid matrix dimensions")
    result: List[List[Union[int, float]]] = []
    for i in range(m1["rows"]):
        row: List[Union[int, float]] = []
        for j in range(m2["cols"]):
            element: Union[int, float] = 0
            for k in range(m1["cols"]):
                element += m1["data"][i][k] * m2["data"][k][j]
            row.append(element)
        result.append(row)
    return create_matrix(result)

def matrix_scalar_multiply(matrix: Dict[str, Any], scalar: Union[int, float]) -> Dict[str, Any]:
    result: List[List[Union[int, float]]] = []
    for i in range(matrix["rows"]):
        row: List[Union[int, float]] = []
        for j in range(matrix["cols"]):
            row.append(matrix["data"][i][j] * scalar)
        result.append(row)
    return create_matrix(result)

def matrix_transpose(matrix: Dict[str, Any]) -> Dict[str, Any]:
    result: List[List[Union[int, float]]] = []
    for j in range(matrix["cols"]):
        row: List[Union[int, float]] = []
        for i in range(matrix["rows"]):
            row.append(matrix["data"][i][j])
        result.append(row)
    return create_matrix(result)

def matrix_determinant(matrix: Dict[str, Any]) -> Union[int, float]:
    if matrix["rows"] != matrix["cols"]:
        raise ValueError("Determinant is only defined for square matrices")
    n = matrix["rows"]
    data = matrix["data"]
    if n == 1:
        return data[0][0]
    if n == 2:
        return data[0][0] * data[1][1] - data[0][1] * data[1][0]
    det: Union[int, float] = 0
    for j in range(n):
        det += ((-1) ** j) * data[0][j] * _minor(matrix, 0, j)
    return det

def _minor(matrix: Dict[str, Any], row: int, col: int) -> Union[int, float]:
    minor_data: List[List[Union[int, float]]] = []
    for i in range(matrix["rows"]):
        if i == row:
            continue
        minor_row: List[Union[int, float]] = []
        for j in range(matrix["cols"]):
            if j == col:
                continue
            minor_row.append(matrix["data"][i][j])
        minor_data.append(minor_row)
    if len(minor_data) == 1:
        return minor_data[0][0]
    return matrix_determinant(create_matrix(minor_data))

def matrix_shape(matrix: Dict[str, Any]) -> tuple[int, int]:
    return (matrix["rows"], matrix["cols"])