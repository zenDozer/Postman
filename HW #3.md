# Postman HOMEWORK 3

## /login
### http://162.55.220.72:5005/login

- POST
- login : str (кроме /)
- password : str

- Body form-data:
```
login: user
password: 123qweasd
```
- response:
```json
{
    "token": "/s34lfgbj/user/jjd909/86960kjkWpqc5438123qweasd201107evny"
}
```

**Приходящий токен необходимо передать во все остальные запросы.**
**дальше все запросы требуют наличие токена.**

```js
var jsonData = pm.response.json();

pm.environment.set("auth_token"); //Создаем переменную в окружении
pm.environment.set("auth_token", jsonData.token); //Задаем значение
```

************

## /user_info
### 2) http://162.55.220.72:5005/user_info

- RAW JSON
- POST
- age: int
- salary: int
- name: str
- auth_token

- Raw JSON:
```json
{
    "age": "{{age}}",
    "salary": "{{salary}}",
    "name": "{{name}}",
    "auth_token": "{{auth_token}}"
}
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
        "u_salary_1_5_year": 2000
    },
    "qa_salary_after_12_months": 1450.0,
    "qa_salary_after_6_months": 1000,
    "start_qa_salary": 500
}
```

**Тесты**
```js
// Парсинг ответа и запроса
var responseJson = pm.response.json();
request_data_raw = JSON.parse(request.data); //преобразование Raw в формат JSON
```

**1) Статус код 200**
```js
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```

**2) Проверка структуры json в ответе.**
```js
var schema = {
    "type": "object",
    "required": [
        "person",
        "qa_salary_after_12_months",
        "qa_salary_after_6_months",
        "start_qa_salary"
    ],
    "properties": {
        "person": {
            "type": "object",
            "required": [
                "u_age",
                "u_name",
                "u_salary_1_5_year"
            ],
            "properties": {
                "u_age": {
                    "type": "integer",
                },
                "u_name": {
                    "type": "array",
                    "minItems": 3,
                },
                "u_salary_1_5_year": {
                    "type": "integer",
                }
            },
        },
        "qa_salary_after_12_months": {
            "type": "number",
        },
        "qa_salary_after_6_months": {
            "type": "integer",
        },
        "start_qa_salary": {
            "type": "integer",
        }
    },
}

pm.test('Schema is valid', function() {
  pm.response.to.have.jsonSchema(schema);
});

//pm.test('Schema is valid', function () {
//    pm.expect(tv4.validate(responseJson, schema)).to.be.true; //Вариант через тини валидатор (мало информативный)
//});
```

**3) В ответе указаны коэффициенты умножения salary, напишите тесты по проверке правильности результата перемножения на коэффициент.**
```js
pm.test("Check u_salary_1_5_year = request salary * 4", function () {
    pm.expect(responseJson.person.u_salary_1_5_year).to.eql(+request_data_raw.salary * 4);
});

pm.test("Check qa_salary_after_12_months = request salary * 2.9", function () {
    pm.expect(responseJson.qa_salary_after_12_months).to.eql(+request_data_raw.salary * 2.9);
});

pm.test("Check qa_salary_after_6_months = request salary * 2", function () {
    pm.expect(responseJson.qa_salary_after_6_months).to.eql(+request_data_raw.salary * 2);
});
```

**4) Достать значение из поля 'u_salary_1.5_year' и передать в поле salary запроса http://162.55.220.72:5005/get_test_user**
```js
//Через окружение
pm.environment.set("u_salary_1_5_year")
pm.environment.set("u_salary_1_5_year", responseJson.person.u_salary_1_5_year)

//Напрямую в запрос из тестов
pm.sendRequest({
    url: "http://162.55.220.72:5005/get_test_user",
    method: 'POST',
    body: {
        mode: 'urlencoded',
        urlencoded: [
            {key: 'age', value: responseJson.person.u_age},
            {key: 'salary', value: responseJson.person.u_salary_1_5_year}, // <---
            {key: 'name', value: responseJson.person.u_name},
            {key: 'auth_token', value: request_data_raw.auth_token},
            ]
    }
    }, function (err, response) {
    console.log(response.json());
});
```

************

## /new_data
### 3) http://162.55.220.72:5005/new_data

- POST
- age: int
- salary: int
- name: str
- auth_token

- Body form-data:
```
age: 42
salary: 500
name: Oleksandr
auth_token: {{token}}
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

**Тесты**
```js
// Парсинг ответа и запроса
var responseJson = pm.response.json();
request_data = request.data;
```

**1) Статус код 200**
```js
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```

**2) Проверка структуры json в ответе.**
```js
var schema = {
    "type": "object",
    "required": [
        "age",
        "name",
        "salary",
    ],
    "properties": {
        "age": {
            "type": "integer",
        },
        "name": {
            "type": "string",
        },
        "salary": {
            "type": "array",
            "minItems": 3,
            },
    },
}

pm.test('Schema is valid', function() {
  pm.response.to.have.jsonSchema(schema);
});
```

**3) В ответе указаны коэффициенты умножения salary, напишите тесты по проверке правильности результата перемножения на коэффициент.**
```js
pm.test("Check salary[0] = request salary", function () {
    pm.expect(+responseJson.salary[0]).to.eql(+request_data.salary);
});

pm.test("Check salary[1] = request salary * 2", function () {
    pm.expect(+responseJson.salary[1]).to.eql(+request_data.salary * 2);
});

pm.test("Check salary[2] = request salary * 3", function () {
    pm.expect(+responseJson.salary[2]).to.eql(+request_data.salary * 3);
});
```

**4) проверить, что 2-й элемент массива salary больше 1-го и 0-го**
```js
pm.test("Check salary[2] > salary[1] and salary[0]", function () {
    pm.expect(+responseJson.salary[2]).to.greaterThan(+responseJson.salary[1]);
    pm.expect(+responseJson.salary[2]).to.greaterThan(+responseJson.salary[0]);    
});
```

************

## /test_pet_info
### 4) http://162.55.220.72:5005/test_pet_info

- POST
- age: int
- weight: int
- name: str
- auth_token

- Body form-data:
```
age: 42
weight: 79
name: Oleksandr
auth_token: {{token}}
```
- response:
```json
{
    "age": 42,
    "daily_food": 0.9480000000000001,
    "daily_sleep": 197.5,
    "name": "Oleksandr"
}
```

**Тесты**
```js
// Парсинг ответа и запроса
var responseJson = pm.response.json();
request_data = request.data;
```

**1) Статус код 200**
```js
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```

**2) Проверка структуры json в ответе.**
```js
var schema = {
    "type": "object",
    "required": [
        "age",
        "daily_food",
        "daily_sleep",
        "name",
    ],
    "properties": {
        "age": {
            "type": "integer",
        },
        "daily_food": {
            "type": "number",
        },
        "daily_sleep": {
            "type": "number",
        },
        "name": {
            "type": "string",
        },
    },
}

pm.test('Schema is valid', function() {
  pm.response.to.have.jsonSchema(schema);
});
```

**3) В ответе указаны коэффициенты умножения weight, напишите тесты по проверке правильности результата перемножения на коэффициент.**
```js
pm.test("Check daily_food = request weight * 0.012", function () {
    pm.expect(+responseJson.daily_food).to.eql(+request_data.weight * 0.012);
});

pm.test("Check daily_sleep = request weight * 2.5", function () {
    pm.expect(+responseJson.daily_sleep).to.eql(+request_data.weight * 2.5);
});
```

************

## /get_test_user
### 5) http://162.55.220.72:5005/get_test_user

- POST
- age: int
- salary: int
- name: str
- auth_token

- Body form-data:
```
age: 42
salary: {{u_salary_1_5_year}}
name: Oleksandr
auth_token: {{token}}
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
        "u_salary_1_5_year": 8000
    },
    "name": "Oleksandr",
    "salary": 2000
}
```

**Тесты**
```js
// Парсинг ответа и запроса
var responseJson = pm.response.json();
request_data = request.data;
```

**1) Статус код 200**
```js
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```

**2) Проверка структуры json в ответе.**
```js
var schema = {
    "type": "object",
    "required": [
        "age",
        "family",
        "name",
        "salary"
    ],
    "properties": {
        "age": {
            "type": "string",
        },
        "family": {
            "type": "object",
            "required": [
                "children",
                "u_salary_1_5_year"
            ],
            "properties": {
                "children": {
                    "type": "array",
                    "minItems": 2,
                },
                "u_salary_1_5_year": {
                    "type": "integer",
                }
            },
        },
        "name": {
            "type": "string",
        },
        "salary": {
            "type": "integer",
        }
    },
}

pm.test('Schema is valid', function() {
  pm.response.to.have.jsonSchema(schema);
});
```

**3) Проверить что занчение поля name = значению переменной name из окружения**
```js
pm.test("name = environment name", function () {
    pm.expect(responseJson.name).to.eql(pm.environment.get("name"));
});
```

**4) Проверить что занчение поля age в ответе соответсвует отправленному в запросе значению поля age**
```js
pm.test("response age = request age", function () {
    pm.expect(responseJson.age).to.eql(request_data.age);
});
```

************

## /currency
### 6) http://162.55.220.72:5005/currency
### Alt - http://54.157.99.22:80/currency

- POST
- auth_token

- Body form-data:
```
auth_token: {{token}}
```
- response:
```json
[
    {
        "Cur_Abbreviation": "USD",
        "Cur_ID": 0,
        "Cur_Name": "US dollars"
    },
    {
        "Cur_Abbreviation": "CAD",
        "Cur_ID": 1,
        "Cur_Name": "Canadian dollars"
    },
    ...
    {
        "Cur_Abbreviation": "ZWL",
        "Cur_ID": 118,
        "Cur_Name": "Zimbabwean Dollar"
    }
]
```

**Тесты**
```js
// Парсинг ответа
var responseJson = pm.response.json();
```

**1) Можете взять любой объект из присланного списка, используйте js random. В объекте возьмите Cur_ID и передать через окружение в следующий запрос.**
```js
//This JavaScript function always returns a random number between min and max (both included):
function getRandomIntInclusive(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min; //Максимум и минимум включаются
}

// Записываем значение Cur_ID рандомного объекта в переменную окружения
pm.environment.set("Cur_ID", responseJson[getRandomIntInclusive(0, Object.keys(responseJson).length - 1)].Cur_ID);

console.log(pm.environment.get("Cur_ID"));
```

************

## /curr_byn
### 7) http://162.55.220.72:5005/curr_byn
### Alt - http://54.157.99.22:80/curr_byn

- POST
- auth_token
- curr_code: int

- Body form-data:
```
auth_token: {{token}}
curr_code: {{Cur_ID}}
```
- response:
```json
{
    "Cur_Abbreviation": "TZS",
    "Cur_ID": 106,
    "Cur_Name": "Tanzanian shillings",
    "Cur_OfficialRate": 71.62360366172011,
    "Cur_Scale": 36,
    "Date": "2023-05-28"
}
```

**1) Статус код 200**
```js
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```

**2) Проверка структуры json в ответе.**
```js
var schema = {
    "type": "object",
    "required": [
        "Cur_Abbreviation",
        "Cur_ID",
        "Cur_Name",
        "Cur_OfficialRate",
        "Cur_Scale",
        "Date",
    ],
    "properties": {
        "Cur_Abbreviation": {
            "type": "string",
        },
        "Cur_ID": {
            "type": "integer",
        },
        "Cur_Name": {
            "type": "string",
        },
        "Cur_OfficialRate": {
            "type": "number",
        },
        "Cur_Scale": {
            "type": "integer",
        },
        "Date": {
            "type": "string",
        },

    },
}

pm.test('Schema is valid', function() {
  pm.response.to.have.jsonSchema(schema);
});
```

************

## /currency ***
### 6) http://162.55.220.72:5005/currency
### Alt - http://54.157.99.22:80/currency

- POST
- auth_token

- Body form-data:
```
auth_token: {{token}}
```
- response:
```json
[
    {
        "Cur_Abbreviation": "USD",
        "Cur_ID": 0,
        "Cur_Name": "US dollars"
    },
    ...
    {
        "Cur_Abbreviation": "ZWL",
        "Cur_ID": 118,
        "Cur_Name": "Zimbabwean Dollar"
    }
]
```
```
***
1) получить список валют
2) итерировать список валют
3) в каждой итерации отправлять запрос на сервер для получения курса каждой валюты
4) если возвращается 500 код, переходим к следующей итреации
5) если получаем 200 код, проверяем response json на наличие поля "Cur_OfficialRate"
6) если поле есть, пишем в консоль инфу про фалюту в виде response
{
    "Cur_Abbreviation": str
    "Cur_ID": int,
    "Cur_Name": str,
    "Cur_OfficialRate": float,
    "Cur_Scale": int,
    "Date": str
}
7) переходим к следующей итерации

```
```js
//1) получить список валют
var currency = pm.response.json();

//2) итерировать список валют
currency.forEach((item) => {
    //3) в каждой итерации отправлять запрос на сервер для получения курса каждой валюты    
    pm.sendRequest({
        url: "http://54.157.99.22:80/curr_byn",
        method: 'POST',
        body: {
            mode: 'formdata',
            formdata: [
                {key: 'auth_token', value: pm.environment.get("auth_token")},
                {key: 'curr_code', value: String(item.Cur_ID)},
                ]
        }
        }, function (err, response) {
        //4) если возвращается 500 код, переходим к следующей итреации
        if (response.code === 500) {
            return;
        } else if (response.code === 200) {
            //5) если получаем 200 код, проверяем response json на наличие поля "Cur_OfficialRate"
            if ('Cur_OfficialRate' in response.json()) {
                //6) если поле есть, пишем в консоль инфу про валюту в виде response
                console.log(response.json())
            }
        }
        });
        //7) переходим к следующей итерации
});
```