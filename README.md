# Домашние работы:


- [Postman HOMEWORK 1](https://github.com/zenDozer/Postman/blob/main/HW%231/HW%20%231.md)
- [Postman HOMEWORK 2](https://github.com/zenDozer/Postman/blob/main/HW%232/HW%20%232.md)
- [Postman HOMEWORK 3](https://github.com/zenDozer/Postman/blob/main/HW%233/HW%20%233.md)

************

# :large_blue_diamond: Конспект Postman :large_blue_diamond:

************

## :one: Парсинг

:small_orange_diamond: ***Парсинг ответа (response) в json (form data):***
```js
var responseJson = pm.response.json();
```

:small_orange_diamond: ***Для разбора XML используйте следующее:***
```js
var responseJson = xml2Json(pm.response.text());
```

:small_orange_diamond:  ***Парсинг запроса (request) в json (form data):***
```js
var request_data = request.data;
```

:small_orange_diamond: ***Парсинг запроса (request) в json (raw data):***
```js
request_data_raw = JSON.parse(request.data);
```

:small_orange_diamond: ***Парсинг запроса (request) GET (url params) и отдельно параметры:***
```js
var request_data = pm.request.url.query.toObject();
```

************

## :two: Тесты

:small_orange_diamond: ***Проверка статус кода:***
```js
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

//Проверка нескольких кодов
pm.test("Successful POST request", () => {
  pm.expect(pm.response.code).to.be.oneOf([201,202]);
});
```

:small_orange_diamond: ***Тестирование заголовков***
```js
//Вы можете проверить наличие заголовка ответа:
pm.test("Content-Type header is present", () => {
  pm.response.to.have.header("Content-Type");
});

//Вы также можете проверить заголовок ответа, имеющий определенное значение:
pm.test("Content-Type header is application/json", () => {
  pm.expect(pm.response.headers.get('Content-Type')).to.eql('application/json');
});
```

:small_orange_diamond: ***Тестирование файлов cookie***
```js
//Вы можете проверить, присутствует ли файл cookie в ответе:
pm.test("Cookie JSESSIONID is present", () => {
  pm.expect(pm.cookies.has('JSESSIONID')).to.be.true;
});

//Вы также можете проверить наличие определенного значения файла cookie:
pm.test("Cookie isLoggedIn has value 1", () => {
  pm.expect(pm.cookies.get('isLoggedIn')).to.eql('1');
});
``` 

:small_orange_diamond: ***Тестирование времени отклика***
```js
//Вы можете проверить, чтобы время отклика находилось в заданном диапазоне:
pm.test("Response time is less than 200ms", () => {
  pm.expect(pm.response.responseTime).to.be.below(200);
});
```

:small_orange_diamond: ***Проверка на наличие строки в ответе:***
```js
pm.test("Body is correct", function () {
    pm.response.to.have.body("Text");
});

pm.test("Body contains string",() => {
  pm.expect(pm.response.text()).to.include("customer_id");
});
```

:small_orange_diamond: ***Проверка на равенство:***
```js
pm.test("Check name", function () {
    pm.expect(responseJson.name).to.eql("name"); //Указано вручную
});

pm.test("Check name = request name", function () {
    pm.expect(responseJson.name).to.eql(request_data.name); //Взято из запроса 
});

pm.test("Check name = request name", function () {
    pm.expect(responseJson.name).to.eql(pm.request.url.query.get('name')); //Взято из параметра URL в запросе
});
```

:small_orange_diamond: ***Проверка на больше или меньше:***
```js
pm.test("Check salary[2] > salary[1] and salary[0]", function () {
    pm.expect(+responseJson.salary[2]).to.greaterThan(+responseJson.salary[1]);
});

pm.test("Billing amount is less than 2500", function(){
    pm.expect(jsonData.data.bill<2500)
});

pm.test("Billing amount is less than 2500", function(){
    pm.expect(jsonData.data.bill).to.be.below(2500)
});
```

:small_orange_diamond: ***Использование нескольких утверждений:***
```js
pm.test("The response has all properties", () => {
    // разобрать json ответ и проверить три свойства
    const responseJson = pm.response.json();
    pm.expect(responseJson.type).to.eql('vip');
    pm.expect(responseJson.name).to.be.a('string');
    pm.expect(responseJson.id).to.have.lengthOf(1);
});
```

:small_orange_diamond: ***Проверка на наличие параметра:***
```js
pm.test("Dog has param name", function () {
    pm.expect(responseJson.family.pets.dog).to.have.property('name');
});
```

:small_orange_diamond: ***Проверка типа значения***
```js
/* ответ имеет такую структуру:
{
  "name": "Jane",
  "age": 29,
  "hobbies": [
    "skating",
    "painting"
  ],
  "email": null
}
*/
const jsonData = pm.response.json();
pm.test("Test data type of the response", () => {
  pm.expect(jsonData).to.be.an("object");
  pm.expect(jsonData.name).to.be.a("string");
  pm.expect(jsonData.age).to.be.a("number");
  pm.expect(jsonData.hobbies).to.be.an("array");
  pm.expect(jsonData.website).to.be.undefined;
  pm.expect(jsonData.email).to.be.null;
});
```

:small_orange_diamond: ***Проверка свойств массива***
```js
/*
ответ имеет такую структуру:
{
  "errors": [],
  "areas": ["goods", "services"],
  "settings": [
    {
      "type": "notification",
      "detail": ["email", "sms"]
    },
    {
      "type": "visual",
      "detail": ["light", "large"]
    }
  ]
}
*/

const jsonData = pm.response.json();
pm.test("Test array properties", () => {
    //массив ошибок пуст
  pm.expect(jsonData.errors).to.be.empty;
    //массив включает в себя "товары"
  pm.expect(jsonData.areas).to.include("goods");
    
  //получить объект настроек уведомлений
  const notificationSettings = jsonData.settings.find
      (m => m.type === "notification");
  pm.expect(notificationSettings).to.be.an("object", "Could not find the setting");
    //массив "detail" должен включать "sms"
  pm.expect(notificationSettings.detail).to.include("sms");
    //массив "detail" должен включать все перечисленные элементы
  pm.expect(notificationSettings.detail).to.have.members(["email", "sms"]);
});
```

:small_orange_diamond: ***Проверка свойств объекта***
```js
//Вы можете проверить, что объект содержит ключи или свойства.
pm.expect({a: 1, b: 2}).to.have.all.keys('a', 'b');
pm.expect({a: 1, b: 2}).to.have.any.keys('a', 'b');
pm.expect({a: 1, b: 2}).to.not.have.any.keys('c', 'd');
pm.expect({a: 1}).to.have.property('a');
pm.expect({a: 1, b: 2}).to.be.an('object').that.has.all.keys('a', 'b');
```

:small_orange_diamond: ***Проверка, что значение находится в наборе***
```js
//Вы можете проверить значение ответа по списку допустимых параметров.
pm.test("Value is in valid list", () => {
  pm.expect(pm.response.json().type).to.be.oneOf(["Subscriber", "Customer", "User"]);
});
```

:small_orange_diamond: ***Проверка структуры json в ответе (https://www.jsonschema.net):***
```js
var schema = {
    "type": "object",
    "required": [
        "Param1",
        "Param2",
        "Param3",
    ],
    "properties": {
        "Param1": {
            "type": "object",
            "required": [
                "Param1_1",
                "Param1_2",
                "Param1_3"
            ],
            "properties": {
                "Param1_1": {
                    "type": "integer",
                },
                "Param1_2": {
                    "type": "array",
                    "minItems": 3,
                },
                "Param1_3": {
                    "type": "integer",
                }
            },
        },
        "Param2": {
            "type": "number",
        },
        "Param3": {
            "type": "integer",
        },
    },
}

pm.test('Schema is valid', function() {
  pm.response.to.have.jsonSchema(schema);
});
```

************

## :three: Окружение (Environment)

:small_orange_diamond: ***Проверка текущего окружения***
```js
//Вы можете проверить активную (выбранную в данный момент) среду в Postman.
pm.test("Check the active environment", () => {
  pm.expect(pm.environment.name).to.eql("Production");
});
```

:small_orange_diamond: ***Создать в окружении переменную:***
```js
pm.environment.set("name");
```

:small_orange_diamond: ***Передать в окружение переменную:***
```js
pm.environment.set("name", request_params.name);
```

:small_orange_diamond: ***Получить значение переменной из окружения:***
```js
pm.environment.get("name");
```

************
## :four: Консоль

:small_orange_diamond: 
```js
//Вы можете записать значение переменной или свойства ответа:
console.log(pm.collectionVariables.get("name"));
console.log(pm.response.json().name);

//Вы можете записать тип переменной или свойства ответа:
console.log(typeof pm.response.json().id);
```

************

## :five: Запросы

:small_orange_diamond: 
```js
pm.sendRequest({
    url: "http://54.157.99.22:80/curr_byn",
    method: 'POST',
    body: {
        mode: 'formdata',
        formdata: [
            {key: 'auth_token', value: pm.environment.get("auth_token")},
            {key: 'curr_code', value: pm.environment.get("Cur_ID")},
            ]
    }
    }, function (err, response) {
    console.log(response.json())
});
```

************

## :six: JavaScript

:small_orange_diamond: ***Массивы, объекты***
```js
let r2d2 = [
   [11,22,33],
   ['qq','ww','ee'],
   [['q1','w2'],['a1','s2'],[['dd1','ff2'],['tt3', 'tt4']]]
]
console.log(r2d2[2][2][0][1])
```
```js
let j_son1 = {'name': 'Vadim',
              'age':33,
              'salary': 1000,
              'computers':{'comp1': 'Lenovo Legion',
                           'comp2': 'Lenovo ThinkPad',
                           'comp3': "MacBook 15' 2017" 
                        },
              'family': {'wife':{'name':'Nataliia',
                                 'age': 30,
                                 'Children':{'ch1':{'name':'Milana','age':5}}
                                }
            
                        }
}

console.log(j_son1.age)
console.log(j_son1.computers.comp2)
console.log(j_son1.family.wife.Children.ch1.age)
```

:small_orange_diamond: ***Циклы и Условные ветвления if***
```js
let year = 2015;

if (year < 2015) {
  console.log('Это слишком рано...');
} else if (year > 2015) {
  console.log('Это поздновато');
} else {
  console.log('Верно!');
}
```
```js
for(let i=0; i <= 10; i++){
	console.log(i);
}
```
```js
count = 0
while (count <= 10){
    count += 1
    console.log('Hello!!!', count)
}
```
```js
for (i in responseJson.salary) {
	console.log(responseJson.salary[i]);
}
```
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
