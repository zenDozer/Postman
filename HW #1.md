# Postman HOMEWORK 1

## Создать запросы в Postman.

```
Protocol: http
IP: 162.55.220.72
Port: 5005
```

### EP_1
- Method: GET
- EndPoint: /get_method
- request url params: 
    name: str
    age: int

```
url params:
    name: Oleksandr
    age: 42

url:
http://162.55.220.72:5005/get_method?name=Oleksandr&age=42
```

- response:
```json
[
    "Oleksandr",
    "42"
]
```

************

### EP_2
- Method: POST
- EndPoint: /user_info_3
- request form data: 
    name: str
    age: int
    salary: int

```
http://162.55.220.72:5005/user_info_3

Body form-data:
name: Oleksandr
age: 42
salary: 500
```

- response:
```json
{
    "age": "42",
    "family": {
        "children": [
            [
                "Alex",
                24
            ],
            [
                "Kate",
                12
            ]
        ],
        "u_salary_1_5_year": 2000
    },
    "name": "Oleksandr",
    "salary": 500
}
```

************

### EP_3
- Method: GET
- EndPoint: /object_info_1
- request url params: 
    name: str
    age: int
    weight: int

 ```
http://162.55.220.72:5005/object_info_1?name=Oleksandr&age=42&weight=81
 ```

- response:
```json
{
    "age": 42,
    "daily_food": 0.972,
    "daily_sleep": 202.5,
    "name": "Oleksandr"
}
```

************

### EP_4
- Method: GET
- EndPoint: /object_info_2
- request url params: 
    name: str
    age: int
    salary: int

```
url params:
    name: Oleksandr
    age: 42
    salary: 500

url:
http://162.55.220.72:5005/object_info_2?name=Oleksandr&age=42&salary=500
```

- response:
```json
{
    "person": {
        "u_age": 42,
        "u_name": [
            "Oleksandr",
            500,
            42
        ],
        "u_salary_5_years": 2100.0
    },
    "qa_salary_after_1.5_year": 1650.0,
    "qa_salary_after_12_months": 1350.0,
    "qa_salary_after_3.5_years": 1900.0,
    "qa_salary_after_6_months": 1000,
    "start_qa_salary": 500
}
```

************

### EP_5
- Method: GET
- EndPoint: /object_info_3
- request url params: 
    name: str
    age: int
    salary: int

```
url params:
    name: Oleksandr
    age: 42
    salary: 500

url:
http://162.55.220.72:5005/object_info_3?name=Oleksandr&age=42&salary=500
```

- response: 
```json
{
    "age": "42",
    "family": {
        "children": [
            [
                "Alex",
                24
            ],
            [
                "Kate",
                12
            ]
        ],
        "pets": {
            "cat": {
                "age": 3,
                "name": "Sunny"
            },
            "dog": {
                "age": 4,
                "name": "Luky"
            }
        },
        "u_salary_1_5_year": 2000
    },
    "name": "Oleksandr",
    "salary": 500
}
```


************

### EP_6
- Method: GET
- EndPoint: /object_info_4
- request url params: 
    name: str
    age: int
    salary: int

```
url params:
    name: Oleksandr
    age: 42
    salary: 500

url:
http://162.55.220.72:5005/object_info_4?name=Oleksandr&age=42&salary=500
```

- response: 
```json
{
    "age": 42,
    "name": "Oleksandr",
    "salary": [
        500,
        "1000",
        "1500"
    ]
}
```


************

### EP_7
- Method: POST
- EndPoint: /user_info_2
- request form data: 
    name: str
    age: int
    salary: int

```
http://162.55.220.72:5005/user_info_2

Body form-data:
name: Oleksandr
age: 42
salary: 500
```

- response:
```json
{
    "person": {
        "u_age": 42,
        "u_name": [
            "Oleksandr",
            500,
            42
        ],
        "u_salary_5_years": 2100.0
    },
    "qa_salary_after_1.5_year": 1650.0,
    "qa_salary_after_12_months": 1350.0,
    "qa_salary_after_3.5_years": 1900.0,
    "qa_salary_after_6_months": 1000,
    "start_qa_salary": 500
}
```