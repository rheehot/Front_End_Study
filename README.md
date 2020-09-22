# 🍓Front-End Study🍓

## DATE : 2020-09-10 기록



### Var, let의 차이

### ⭐var과 function의 hoisting → 어디에 선언 했는지 상관 없이 선언을 위로 끌어 올려주는 것을 말한다. (선언 전에 호출 되어도 hoisting 돼 코드가 문제 없이 실행된다.) 

### 그래서 let 을 사용해야 한다.

### Client ↔ Server

## <JSON>

1. Object to JSON
- 함수는 변환되지 않는다.

```jsx
//1. obeject to json

const rabbit ={

    name: 'tori',
    color: 'white',
    size : null,
    birth : new Date(),

};

json = JSON.stringify(rabbit, (key, value) =>
{
    console.log(`key : ${key}, value : ${value}`);
    return value;
});

console.log(json);
```

 2. JSON to Object

함수를 사용하면 세부적으로 조정 할 수 있다. 

```jsx
//2. json to object

console.clear();

json = JSON.stringify(rabbit);

const obj = JSON.parse(json, (key, value) =>{
    return key=== 'birth' ? new Date(value) : value;
});

console.log(obj.birth);
```

## <동기와 비동기>

1. Synchronous callback
- 순차적으로 콜백 해준다.

2. Asyhchronous callback

- 언제 실행될 지 예측할 수 없는 콜백.
- 순서대로 콜백이 되지 않는다.

🔥CallBack 지옥

```jsx
class UserStorage{

    loginUser(id, password, onSucess, onError){
        setTimeout(()=>{

            if((id==='holim0' && password==='jj1226') || (id==='holim4' && password==='1234')){
                onSucess(id);
            }else{
                onError(new Error('not found'));
            }

        }, 2000);

    }
    
    getRoles(user, onSucess, onError){
        if(user==='holim0'){
            onSucess({name: 'holim0', role: 'admin'});
        }else{
            onError(new Error('no access'));
        }
    }
}

const userStorage =new UserStorage();
const id=prompt('enter your id');
const password = prompt('enter your password');

userStorage.loginUser(
    id, 
    password, 
    user=>{
        userStorage.getRoles(
            user, 
            userWithRole=>{
                alert(`hello ${userWithRole.name}, you have ${userWithRole.role}`);
            }, 
            error=>{
                console.log(error);
            })
    }, 
    (error) => {console.log(error)}
    
);
```

→ 가독성이 떨어진다. 에러 발생 시 문제점을 찾기가 힘들다. 

## <Promise>

- JS 에 내장된 객체이고 비동기 수행을 위해 callback 대신 쓰인다.
- 시간이 오래걸리는 것을 처리하는데 유용하다 ex) network, readfiles
- 새로운 promise 가 생성될때는 executor 가 자동으로 실행된다. (유의해야한다) → why? 불필요한 작업을 수행 할 수 있기 때문임.
- 

1. state : pending ( 수행 중) - > fullfilled or rejected
2. Producer vs consumer

### 1. Producer

```jsx
//1. Producer

const promise = new Promise((resolve, reject)=>{
    //doing some heavy work (networ)

    console.log('dong');
    setTimeout(()=>{
        resolve('holim0');
    }, 2000);

});
```

### 2. Consumers : then, catch, finally

- then : resolve 일 때 실행
- catch : rejected 일 때 실행
- finally : resolve , rejected 상관없이 마지막에 실행

```jsx
//2. Consumer : then, catch, finally

promise
    .then((value)=>{
    console.log(value);
    })
    .catch(error=>{
        console.log(error);
    })
    .finally(()=>{
        console.log('finally');
    });
```

### 3. Promise chaining

```jsx
const fetchNumber = new Promise((resolve, reject)=>{
    setTimeout(()=> resolve(1), 1000);

});

fetchNumber
    .then(num=> num*2)
    .then(num => num*3)
    .then(num=>{
        return new Promise((resolve, reject)=>{
            setTimeout(()=> resolve(num-1), 1000);
        });
    })
    .then(num=> console.log(num));
```

## Callback to Promise

```jsx
class UserStorage{

    loginUser(id, password){

        return new Promise((resolve, reject)=>{
            setTimeout(()=>{

                if((id==='holim0' && password==='jj1226') || (id==='holim4' && password==='1234')){
                    resolve(id)
                }else{
                    reject(new Error('not found'));
                }
    
            }, 2000);
        });
    }
    
    getRoles(user){

        return new Promise((resolve, reject)=>{
            setTimeout(()=>{

                if(user==='holim0'){
                    resolve({name: 'holim0', role: 'admin'});
                }else{
                    reject(new Error('no access'));
                }
    
            }, 1000);
            
        });
        
    }
}

const userStorage =new UserStorage();
const id=prompt('enter your id');
const password = prompt('enter your password');

userStorage
    .loginUser(id, password)
    .then(userStorage.getRoles)
    .then(user => alert(`hello ${user.name},  you have a ${user.role}`))
    .catch(console.log);
```

<br/>



## DATE : 2020-09-14 기록

### <Promise 추가 공부>

- 프로미스는 자바스크립트 비동기 처리에 사용되는 객체
- 여기서 자바스크립트의 비동기 처리란 ‘특정 코드의 실행이 완료될 때까지 기다리지 않고 다음 코드를 먼저 수행하는 자바스크립트의 특성’을 의미

### Pending(대기)

먼저 아래와 같이 `new Promise()` 메서드를 호출하면 대기(Pending) 상태

```jsx
new Promise();
```

`new Promise()` 메서드를 호출할 때 콜백 함수를 선언할 수 있고, 콜백 함수의 인자는 `resolve`, `reject`.

```jsx
new Promise(function(resolve, reject) {
  // ...});

```

### Fulfilled(이행)

여기서 콜백 함수의 인자 `resolve`를 아래와 같이 실행하면 이행(Fulfilled) 상태가 됩니다.

```jsx
new Promise(function(resolve, reject) {
  resolve();
});

```

그리고 이행 상태가 되면 아래와 같이 `then()`을 이용하여 처리 결과 값을 받을 수 있습니다.

```jsx
function getData() {
  return new Promise(function(resolve, reject) {
    var data = 100;
    resolve(data);  // data의 값을 resolve로 전달 (콜백함수이다)
  });
}

// resolve()의 결과 값 data를 resolvedData로 받음  
getData()
    .then((resolvedData)=> {
          console.log(resolvedData); // 100
});

```

### Rejected(실패)

여기서 `reject`를 아래와 같이 호출하면 실패(Rejected) 상태가 된다.

```jsx
new Promise(function(resolve, reject) {
  reject();
});
```

그리고, 실패 상태가 되면 실패한 이유(실패 처리의 결과 값)를 `catch()`로 받을 수 있음.

```jsx
function getData() {
  return new Promise(function(resolve, reject) {
    reject(new Error("Request is failed"));  // 콜백 함수이다.
  });
}

// reject()의 결과 값 Error를 err에 받음
getData()
        .then()
        .catch((err) => {
  console.log(err);
{);

```


<img width="972" alt="스크린샷 2020-09-14 오전 12 58 13" src="https://user-images.githubusercontent.com/48006103/93022656-63fd7800-f625-11ea-9054-db9db9e3b705.png">





------

</br>

## [DATE: 2020/09/22 - JS 기초]

</br>

- **Objects and Properties → object can hold different types of data. → 코드 주석 참고**

```jsx
//obeject literal
let john = {
    firstName : 'John',  // key와 value
    lastName : 'Smith',
    brithYear : 1990, 
    job : 'teacher',
    isMarried : false

};

console.log(john.firstName);    // . 으로 value 에 접근할 수 있다. 

console.log(john['isMarried']) // 배열 속 key 값으로도 value 에 접근할 수 있다. 

let x = 'brithYear';

console.log(john[x]);

//mutate obeject

john.job = "programmer";

console.log(john.job);

//new Obeject syntax
let jane = new Object();  // 새로운 객체 형성. 

jane.name = 'jane';
```

</br>

- **Objects and methods → 코드 주석 참고**

```jsx
let john = {
    firstName : 'John',  // key와 value
    lastName : 'Smith',
    brithYear : 1990, 
    job : 'teacher',
    isMarried : false,
    calcAge : function(){    //method
        this.age = 2020-this.brithYear;  // this 는 현재 object 를 가리킨다. this.age property 추가 
    }
};

john.calcAge();     //propertiy 추가 
 
console.log(john);
```

</br>

- **Hoisting → 코드 주석 참고**

```jsx
calculateAge(1999);   // 함수 선언 전에 함수를 호출 -> hoisting 이 되어 잘 작동한다. 

function calculateAge(year){  // 함수 선언. 
    console.log(2020 - year);
}

//retirement(1990);   // 작동하지 않는다

var retirement = function(year) {     // 함수의 선언 방식이 아니기 떄문에 hoisting 되지 않는다. (only work on function declaration)
    console.log(65- (2020- year));
}

//variables

console.log(age); // 작동은 되지만 undefined로 뜬다. (hoisting) -> js 가 변수가 선언되었는지는 알고 있는 것이다. 

var age = 50;  //global 

console.log(age);

function foo(){
    var age = 65; //local
    console.log(age);   //local print
}

foo();  
console.log(age);  // global print
```

</br>

- **this → object 가 method 를 call 할 때만 할당이 된다.**

```jsx
calAge(1996);

function calAge(year){
    console.log(2020-year);
    console.log(this);  // this: global object -> window object 를 가리킴.
}

let john = {
    name: 'john',
    year: 1990,
    cal : function () {
        console.log(this);   // this 는 john object 를 가리킨다. 

        function inner() {
            console.log(this);      // window 를 가리킨다. default object = window. -> method가 아니기 때문이다. 
        }

        inner();
    }
};

john.cal();

let mike ={
    name: 'mike',
    year: '1996',
};

mike.cal = john.cal;  //method borrowing 

mike.cal();
```

</br>