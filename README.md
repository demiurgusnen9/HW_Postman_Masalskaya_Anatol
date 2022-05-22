This is an educational repository that contains homeworks, completed as part of Vadim Ksendzov's training course on software testing. Here I am learning how to work in Postman. Below is my homework and solution

# HW_Postman_Masalskaya_Anatol

## 1. http://162.55.220.72:5005/user_info_2
Необходимо провести тестирование API данного эндпоинта на валидацию входных параметров. Суть задания: проверить валидации каждого поля, 
подаваемого в эндпоинт на возможные значения. Будем УСЛОВНО считать, что негативная проверка должна возвращать какой угодно статус НО НЕ 200! 
Ваша задача написать тест кейсы в постмане таким образом, что одна негативная проверка - один запрос, позитивные проверки можно объединять в 1. 
Ваша задача протестировать исходя из требований на все возможные аспекты. В каждом запросе тест ТОЛЬКО НА СТАТУС КОД 
(200 - позитивное значение, не 200 - негативное). 
P.S. ЗАДАНИЕ НЕ ПОДРАЗУМЕВАЕТ, ЧТО ЭНДПОИНТ РАБОТАЕТ СОГЛАСНО НАПИСАННЫМ ТРЕБОВАНИЯМ. МЫ УЧИМСЯ ПИСАТЬ ТЕСТЫ НА API!
Требования:
1) Name: 3-40 символов включительно, запрещены префиксные и постфиксные пробелы. Поле обязательное
2) Age: только целые цифры в диапазоне 18-120 включительно. Поле обязательное
3) Salary: только целые цифры в диапазоне 1-1000000 включительно. Поле обязательное

## 1****. Преобразовать задание 1 таким образом, чтобы вы отправляли параметры через CSV файл. У вас будет ровно 1 запрос в коллекции, 
который будет повторяться столько раз, сколько строк в CSV файле. Также должна быть написана функция в тестах, которая проверяет валидность 
входящих данных, и в зависимости от этого проверяет на статус 200 или НЕ 200.
``` js 
let req = request.data
let name = req.name
let age = +req.age
let salary = +req.salary

function validName (name) {
   if ((name.length >= 3 && name.length <= 40) && (!((name.startsWith(" ") || (name.endsWith(" ")))))) {
        return true
    }        
}

function validAge (age) {
    if ((age >= 18 && age <= 120) && Number.isInteger(age)) {
        return true
    }
}

function validSalary (salary) {
    if ((salary >= 1 && salary <= 1000000) && Number.isInteger(salary)) {
        return true
    }
}

if (validName(name) && validAge(age) && validSalary(salary)) {
    pm.test(`expect to have status 200 with name = "${req.name}", age = "${req.age}", salary = "${req.salary}"`, function () {
        pm.response.to.have.status(200);
})} else {
    pm.test(`expect not to have status 200 with name = "${req.name}", age = "${req.age}", salary = "${req.salary}"`, function () {
    pm.response.to.not.have.status(200);
})}
```

---


## 2. http://162.55.220.72:5007/object_info_4
* Преобразовать пункты 12-13-14 (salary из реквеста и респонса равны) таким образом, чтобы проверка производилась циклом, 
в котором будет всего ОДИН тест. Имя теста должно меняться в зависимости от значения в Salary
``` js 
let response = pm.response.json();
let req = pm.request.url.query.toObject ();

for (let i = 0; i < resp.salary.length; i++) {
	pm.test(`Salary ${i+1} check`, function () {
        pm.expect(+(resp.salary[i])).to.eql(req.salary * (i+1));
});
}
```

---


## 3. http://162.55.220.72:5005/object_info_3
 Преобразовать задания 5-7 (сравнить идентичные поля в реквесте и респонсе) таким образом, чтобы это делалось ЗА ОДИН ТЕСТ 
 (сразу все 3 поля) БЕЗ ЦИКЛОВ! (глубокое сравнение объектов)
 ``` js
 let response = pm.response.json();
let req = pm.request.url.query.toObject ();
function response_filter(object, keys) {
    return Object.assign({}, ...keys.map(key => ({ [key]: String(object[key]) })));
}
let resp = response_filter(response, ['name', 'age', 'salary'])
pm.test("All check", function () {
        pm.expect(resp).to.deep.equal(req);
})
```

---


## 4. http://162.55.220.72:5005/user_info_2
### 1)  Преобразовать задания 8 - 13 (проверить что в json имеется нужный параметр) таким образом, чтобы все проверки делались в цикле
 (1 проверка в цикле, в которую попадают нужные параметры). Название теста должно видоизменяться исходя из подаваемых данных. 
 ( ${}  или другим способом)
 ``` js
 let resp = pm.response.json();
 let req = request.data
 let property = [
    'person',
    'qa_salary_after_1.5_year',
    'qa_salary_after_12_months',
    'qa_salary_after_3.5_years',
    'qa_salary_after_6_months',
    'start_qa_salary'
]
for (let i = 0; i < property.length; i++) {
	pm.test(`json response has ${property[i]} check`, function () {
        pm.expect(resp).to.have.property(property[i]);
});
}
```

### 2) ** Преобразовать задания 14 - 18 (проверить что параметр равен salary умножить на коэффициент) таким образом, чтобы все 
проверки делались в цикле (1 проверка в цикле, в которую попадают нужные параметры). Название теста должно видоизменяться 
исходя из подаваемых данных. ( ${}  или другим способом)
``` js
let coefficient = [
    3.3,
    2.7,
    3.8,
    2,
    1
]
for (let i = 0; i < coefficient.length; i++) {
 pm.test(`${property[(i+1)]} equals ${(+req.salary)}*${coefficient[i]}`, function () {
        pm.expect(Object.values(resp)[(i+1)]).to.eql((req.salary)*coefficient[i])
});
}
```

### 3) *** Преобразовать описанные выше задания 1 и 2 для данного эндпоинта в ОДИН ЦИКЛ, в котором будут проходить ОБА теста.
``` js
for (let i = 0; i < property.length; i++) {
    if( 
	pm.test(`json response has ${property[i]} check`, function () {
        pm.expect(resp).to.have.property(property[i]);
    }) && i < 5) {
        pm.test(`${property[(i+1)]} equals ${(+req.salary)}*${coefficient[i]}`, function () {
            pm.expect(Object.values(resp)[(i+1)]).to.eql((req.salary)*coefficient[i])
    });
    }
}
```
