# Postman HOMEWORK 2

## /first
### http://162.55.220.72:5005/first

**1. Отправить запрос.**

- GET
- response:
```
This is the first responce from server!ss
```

**2. Статус код 200**

```js
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```

**3. Проверить, что в body приходит правильный string.**

```js
// проверяем значение типа данных string"
pm.test("Body is correct", function () {
    pm.response.to.have.body("This is the first responce from server!ss");
});

//проверяем тип данных string
pm.test("Body matches string", function () {
    pm.expect(pm.response.text()).to.be.a("string")
});
```

************

## /user_info_3
### http://162.55.220.72:5005/user_info_3

**1. Отправить запрос.**

- POST
```
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

**2. Статус код 200**

```js
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```

**3. Спарсить response body в json.**

```js
var responseJson = JSON.parse(responseBody);
```

**4. Проверить, что name в ответе равно name s request (name вбить руками.)**

```js
pm.test("Check name", function () {
    pm.expect(responseJson.name).to.eql("Oleksandr");
});
```

**5. Проверить, что age в ответе равно age s request (age вбить руками.)**

```js
pm.test("Check age", function () {
    pm.expect(+responseJson.age).to.eql(42); //Несовпадение типов данных - переводим респонс age в числовой
});
```

**6. Проверить, что salary в ответе равно salary s request (salary вбить руками.)**

```js
pm.test("Check salary", function () {
    pm.expect(responseJson.salary).to.eql(500);
});
```

**7. Спарсить request.**

```js
var request_data = request.data;
```

**8. Проверить, что name в ответе равно name s request (name забрать из request.)**

```js
pm.test("Check name = request name", function () {
    pm.expect(responseJson.name).to.eql(request_data.name);
});
```

**9. Проверить, что age в ответе равно age s request (age забрать из request.)**

```js
pm.test("Check age = request age", function () {
    pm.expect(+responseJson.age).to.eql(+request_data.age); // Переведем оба типа данных в числовой (можно сравнить и строки - будут одинаковы)
});
```

**10. Проверить, что salary в ответе равно salary s request (salary забрать из request.)**

```js
pm.test("Check salary = request salary", function () {
    pm.expect(responseJson.salary).to.eql(+request_data.salary); //В запросе данные в текстовом формате - переводим в числовой
});
```

**11. Вывести в консоль параметр family из response.**

```js
console.log(responseJson.family);
```

**12. Проверить что u_salary_1_5_year в ответе равно salary x4 (salary забрать из request)**

```js
pm.test("Check user_salary_1_5_year = request * 4", function () {
    pm.expect(responseJson.family.u_salary_1_5_year).to.eql(request_data.salary*4);
});
```

************

## /object_info_3
### http://162.55.220.72:5005/object_info_3

**1. Отправить запрос.**

- GET
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

**2. Статус код 200**

```js
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```

**3. Спарсить response body в json.**

```js
var responseJson = JSON.parse(responseBody);
```

**4. Спарсить request.**

```js
// Весь ответ
var request_data = pm.request.toJSON()
// Парсим только параметры
var request_params = {};
pm.request.url.query.all().forEach((param) => {request_params[param.key] = param.value});
```

**5. Проверить, что name в ответе равно name s request (name забрать из request.)**

```js
pm.test("Check name = request name", function () {
    pm.expect(responseJson.name).to.eql(pm.request.url.query.get('name'));
});
```

**6. Проверить, что age в ответе равно age s request (age забрать из request.)**

```js
pm.test("Check age = request age", function () {
    pm.expect(+responseJson.age).to.eql(+pm.request.url.query.get('age'));
});
```

**7. Проверить, что salary в ответе равно salary s request (salary забрать из request.)**

```js
pm.test("Check salary = request salary", function () {
    pm.expect(+responseJson.salary).to.eql(+pm.request.url.query.get('salary'));
});
```

**8. Вывести в консоль параметр family из response.**

```js
console.log(responseJson.family);
```

**9. Проверить, что у параметра dog есть параметры name.**

```js
pm.test("Dog has param name", function () {
    pm.expect(responseJson.family.pets.dog).to.have.property('name');
});
```

**10. Проверить, что у параметра dog есть параметры age.**

```js
pm.test("Dog has param age", function () {
    pm.expect(responseJson.family.pets.dog).to.have.property('age');
});
```

**11. Проверить, что параметр name имеет значение Luky.**

```js
pm.test("Dog name = Luky", function () {
    pm.expect(responseJson.family.pets.dog.name).to.equal('Luky');
});
```

**12. Проверить, что параметр age имеет значение 4.**

```js
pm.test("Dog age = 4", function () {
    pm.expect(responseJson.family.pets.dog.age).to.equal(4);
});
```

************

## /object_info_4
### http://162.55.220.72:5005/object_info_4

**1. Отправить запрос.**

- GET
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

**2. Статус код 200**

```js
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```

**3. Спарсить response body в json.**

```js
var responseJson = JSON.parse(responseBody);
```

**4. Спарсить request.**

```js
var request_data = pm.request.toJSON() // Весь ответ
var request_params = {};
pm.request.url.query.all().forEach((param) => {request_params[param.key] = param.value}); // Парсим только параметры
```

**5. Проверить, что name в ответе равно name s request (name забрать из request.)**

```js
pm.test("Check name = request name", function () {
    pm.expect(responseJson.name).to.eql(request_params.name);
});
```

**6. Проверить, что age в ответе равно age из request (age забрать из request.)**

```js
pm.test("Check age = request age", function () {
    pm.expect(+responseJson.age).to.eql(+request_params.age);
});
```

**7. Вывести в консоль параметр salary из request.**

```js
console.log(request_params.salary);
```

**8. Вывести в консоль параметр salary из response.**

```js
console.log(responseJson.salary);
```

**9. Вывести в консоль 0-й элемент параметра salary из response.**

```js
console.log(responseJson.salary[0]);
```

**10. Вывести в консоль 1-й элемент параметра salary параметр salary из response.**

```js
console.log(responseJson.salary[1]);
```

**11. Вывести в консоль 2-й элемент параметра salary параметр salary из response.**

```js
console.log(responseJson.salary[2]);
```

**12. Проверить, что 0-й элемент параметра salary равен salary из request (salary забрать из request.)**

```js
pm.test("Check user_salary [0] = request salary", function () {
    pm.expect(responseJson.salary[0]).to.eql(+request_params.salary);
});
```

**13. Проверить, что 1-й элемент параметра salary равен salary x2 из request (salary забрать из request.)**

```js
pm.test("Check user_salary [1] = request salary * 2", function () {
    pm.expect(+responseJson.salary[1]).to.eql(+request_params.salary*2);
});
```

**14. Проверить, что 2-й элемент параметра salary равен salary x3 из request (salary забрать из request.)**

```js
pm.test("Check user_salary [2] = request salary * 3", function () {
    pm.expect(+responseJson.salary[2]).to.eql(+request_params.salary*3);
});
```

**15. Создать в окружении переменную name**

```js
pm.environment.set("name");
```

**16. Создать в окружении переменную age**

```js
pm.environment.set("age");
```

**17. Создать в окружении переменную salary**

```js
pm.environment.set("salary");
```

**18. Передать в окружение переменную name**

```js
pm.environment.set("name", request_params.name);
```

**19. Передать в окружение переменную age**

```js
pm.environment.set("age", request_params.age);
```

**20. Передать в окружение переменную salary**

```js
pm.environment.set("salary", request_params.salary);
```

**21. Написать цикл который выведет в консоль по порядку элементы списка из параметра salary.**

```js
for (i in responseJson.salary) {
	console.log(responseJson.salary[i]);
}
```

************

## /user_info_2
### http://162.55.220.72:5005/user_info_2

**1. Вставить параметр salary из окружения в request**
<br>**2. Вставить параметр age из окружения в age**
<br>**3. Вставить параметр name из окружения в name**
<br>**4. Отправить запрос.**

- POST
```
Body form-data:
name: {{name}}
age: {{age}}
salary: {{salary}}
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

**5. Статус код 200**

```js
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```

**6. Спарсить response body в json.**

```js
var responseJson = JSON.parse(responseBody);
```

**7. Спарсить request.**

```js
var request_data = request.data
```

**8. Проверить, что json response имеет параметр start_qa_salary**

```js
pm.test("Response has param start_qa_salary", function () {
    pm.expect(responseJson).to.have.property('start_qa_salary');
});
```

**9. Проверить, что json response имеет параметр qa_salary_after_6_months**

```js
pm.test("Response has param qa_salary_after_6_months", function () {
    pm.expect(responseJson).to.have.property('qa_salary_after_6_months');
});
```

**10. Проверить, что json response имеет параметр qa_salary_after_12_months**

```js
pm.test("Response has param qa_salary_after_12_months", function () {
    pm.expect(responseJson).to.have.property('qa_salary_after_12_months');
});
```

**11. Проверить, что json response имеет параметр qa_salary_after_1.5_year**

```js
pm.test("Response has param qa_salary_after_1.5_year", function () {
    pm.expect(responseJson).to.have.property('qa_salary_after_1.5_year');
});
```

**12. Проверить, что json response имеет параметр qa_salary_after_3.5_years**

```js
pm.test("Response has param qa_salary_after_3.5_years", function () {
    pm.expect(responseJson).to.have.property('qa_salary_after_3.5_years');
});
```

**13. Проверить, что json response имеет параметр person**

```js
pm.test("Response has param person", function () {
    pm.expect(responseJson).to.have.property('person');
});
```

**14. Проверить, что параметр start_qa_salary равен salary из request (salary забрать из request.)**

```js
pm.test("Check start_qa_salary = request salary", function () {
    pm.expect(responseJson.start_qa_salary).to.eql(+request_data.salary);
});
```

**15. Проверить, что параметр qa_salary_after_6_months равен salary x2 из request (salary забрать из request.)**

```js
pm.test("Check qa_salary_after_6_months = request salary * 2", function () {
    pm.expect(responseJson.qa_salary_after_6_months).to.eql(+request_data.salary * 2);
});
```

**16. Проверить, что параметр qa_salary_after_12_months равен salary x2.7 из request (salary забрать из request.)**

```js
pm.test("Check qa_salary_after_12_months = request salary * 2.7", function () {
    pm.expect(responseJson.qa_salary_after_12_months).to.eql(+request_data.salary * 2.7);
});
```

**17. Проверить, что параметр qa_salary_after_1.5_year равен salary x3.3 из request (salary забрать из request.)**

```js
pm.test("Check qa_salary_after_1.5_year = request salary * 3.3", function () {
    pm.expect(responseJson["qa_salary_after_1.5_year"]).to.eql(+request_data.salary * 3.3);
});
```

**18. Проверить, что параметр qa_salary_after_3.5_years равен salary x3.8 из request (salary забрать из request.)**

```js
pm.test("Check qa_salary_after_3.5_years = request salary * 3.8", function () {
    pm.expect(responseJson["qa_salary_after_3.5_years"]).to.eql(+request_data.salary * 3.8);
});

```

**19. Проверить, что в параметре person, 1-й элемент из u_name равен salary из request (salary забрать из request.)**

```js
pm.test("Check u_name [1] = request salary", function () {
    pm.expect(responseJson.person.u_name[1]).to.eql(+request_data.salary);
});
```

**20. Проверить, что что параметр u_age равен age из request (age забрать из request.)**

```js
pm.test("Check u_age = request age", function () {
    pm.expect(responseJson.person.u_age).to.eql(+request_data.age);
});
```

**21. Проверить, что параметр u_salary_5_years равен salary x4.2 из request (salary забрать из request.)**

```js
pm.test("Check u_salary_5_years = request salary * 4.2", function () {
    pm.expect(responseJson.person.u_salary_5_years).to.eql(+request_data.salary * 4.2);
});
```

**22. ***Написать цикл который выведет в консоль по порядку элементы списка из параметра person.**

```js
for (i in responseJson.person) {
    if (Array.isArray(responseJson.person[i])) {
        for (j in responseJson.person[i]) {
            console.log(i, responseJson.person[i][j]);
        }
    }
    else {
        console.log(i, responseJson.person[i]);
    } 
}
```