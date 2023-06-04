# Конспект Postman

************

## Парсинг

***Парсинг ответа (response) в json (form data):***
```js
var responseJson = JSON.parse(responseBody);
```

***Парсинг запроса (request) в json (form data):***
```js
var request_data = request.data;
```

***Парсинг запроса (request) в json (raw data):***
```js
request_data_raw = JSON.parse(request.data);
```

***Парсинг запроса (request) GET (url params) и отдельно параметры:***
```js
// Весь ответ
var request_data = pm.request.toJSON()
// Парсим только параметры
var request_params = {};
pm.request.url.query.all().forEach((param) => {request_params[param.key] = param.value});
```

************

## Проверки

***Проверка статус кода:***
```js
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```

***Проверка на наличие строки в ответе:***
```js
pm.test("Body is correct", function () {
    pm.response.to.have.body("Text");
});
```

***Проверка на равенство:***
```js
pm.test("Check name", function () {
    pm.expect(responseJson.name).to.eql("Oleksandr"); //Указано вручную
});

pm.test("Check name = request name", function () {
    pm.expect(responseJson.name).to.eql(request_data.name); //Взято из запроса 
});

pm.test("Check name = request name", function () {
    pm.expect(responseJson.name).to.eql(pm.request.url.query.get('name')); //Взято из параметра URL в запросе
});
```

***Проверка на наличие параметра:***
```js
pm.test("Dog has param name", function () {
    pm.expect(responseJson.family.pets.dog).to.have.property('name');
});
```

************

## Окружение (Environment)

***Создать в окружении переменную:***
```js
pm.environment.set("name");
```

***Передать в окружение переменную:***
```js
pm.environment.set("name", request_params.name);
```

***Получить значение переменной из окружения:***
```js
pm.environment.get("name");
```

************

## JavaScript

***Массивы, объекты***
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

***Циклы и Условные ветвления if***
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
