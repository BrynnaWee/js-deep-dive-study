# 순수 함수

🧡 **I N D E X**
- [순수 함수의 두가지 특징](#순수-함수의-두가지-특징)
- [순수함수 예제](#순수함수-예제)
- 🤔[만약, 값이 변경되어야 하는 코드가 있는데, 그것을 반환하는 함수를 순수함수로 만들려면 어떻게 해야할까?](#-의문-1)
- 🤔[**`createRainbowColorGenerator`** 함수와 메서드까지 완벽한 순수함수로 만들어보자.](#-의문-2)
- 🤔[정말로 **`createRainbowColorGenerator`** 함수의 호출 구문을 새로운 변수에 할당할 때마다 다른 컨텍스트가 생성되는 것이 맞나 확인해보자.](#-의문-3)
- [순수함수를 써야하는 이유](#순수함수를-써야하는-이유)
---



### 순수 함수의 두가지 특징

- 순수 함수는 입력이 같으면 결과도 반드시 같다.
    - 동일한 입력에 대해 항상 동일한 출력을 반환함.
- 순수 함수에는 부수 효과(side effect)가 없다.
    - 예를 들어 함수를 실행할 때마다 **함수에서 선언되지 않은** 외부의 다른 데이터가 변경되면 사이드이펙트이다.
    - 함수의 실행 결과는 함수의 인자(입력)에만 의존하며, 함수 외부에서 관찰 가능한 상태를 변경하지 않는다.

---

### 순수함수 예제

- 예제 1)

```jsx
// 순수 함수
function add(a, b) {
  return a + b;
}

// 테스트
console.log(add(3, 5)); // 8
console.log(add(3, 5)); // 8 (동일한 입력에 대해 동일한 결과)
```

- 예제 2)

```jsx
// 순수 함수
function square(x) {
  return x * x;
}

// 테스트
console.log(square(4)); // 16
console.log(square(4)); // 16 (동일한 입력에 대해 동일한 결과)
```

- 예제 3)

```jsx
// 순수 함수
function sortArray(arr) {
  return [...arr].sort((a, b) => a - b);
}

// 테스트
const inputArray = [3, 1, 4, 2];
const sortedArray = sortArray(inputArray);
console.log(sortedArray); // [1, 2, 3, 4]
console.log(inputArray);  // [3, 1, 4, 2] (원본 배열 변경 없음)
```

- 예제4)

```jsx
// 순수 함수
function capitalizeFirstLetter(str) {
  return str.charAt(0).toUpperCase() + str.slice(1);
}

// 테스트
const inputString = 'hello';
const capitalizedString = capitalizeFirstLetter(inputString);
console.log(capitalizedString); // 'Hello'
console.log(inputString);       // 'hello' (원본 문자열 변경 없음)
```

- 예제 5)

```jsx
// 순수 함수
function calculateCircleArea(radius) {
  return Math.PI * radius * radius;
}

// 테스트
const circleRadius = 5;
const area = calculateCircleArea(circleRadius);
console.log(area); // 78.53981633974483
```

---
### 🤔 의문 1
### 만약, 값이 변경되어야 하는 코드가 있는데, 그것을 반환하는 함수를 순수함수로 만들려면 어떻게 해야할까?

- **대상 코드**

```jsx
//아래와 같이 무지개의 색상을 순서대로 나열하는 함수가 있다.
//이것을 순수함수로 만들어라.

const colors = ['red','orange','yellow','green','blue','indigo','purple'];

let colorIndex = -1;

function getNextRainbowColor(){
    if(++colorIndex >= colors.length) colorIndex = 0;

    return colors[colorIndex];
};
```

- 일단 반환 값이 매번 변경되므로 입력이 같으면 결과도 같아야 한다는 조건에 위배된다.
    - 매개변수가 존재하지 않으므로 항상 인수로 **`undefined`** 가 들어간다.(동일값 입력)
- 하지만, 객체를 반환한다면?
- 객체는 참조값이므로, 객체 내부의 프로퍼티가 변경될 뿐 객체 자체는 변경되지 않는다.
- 그러므로 첫번째 원칙에 위배되지 않는다.

- 두번째로, 순수함수에는 부수효과가 없어야한다.
    - 외부의 데이터 또는 외부에서 데이터를 변경해서는 안되므로, 사용할 데이터는 모두 함수의 스코프로 옮겨준다.
    - 또한, 외부의 다른 곳에서 함수를 호출하더라도 반환하는 객체를 변경할 수 없도록 해야한다.
    - 이것은 함수 내부의 값을 반환하는 함수를 객체의 메서드로 만들어버리면 된다.
    - 메서드는 호출하는 객체 컨텍스트 안에서만 동작하므로, 같은 함수를 외부에서 호출하더라도 독립적인 객체가 생성되므로 사이드이펙트를 발생시키지 않는다.

- **순수함수로 변경**
    
    ```jsx
    function createRainbowColorGenerator(){
    
        //사용할 변수들을 내부 스코프로 옮겨줌
        const colors = ['red','orange','yellow','green','blue','indigo','purple'];
        let colorIndex = -1;
        
        return { 
            //전체 리턴값을 객체로 만들어 값을 반환하는 함수를 메서드로 만들어줌
            getNextColor(){
                if(++colorIndex >= colors.length) colorIndex = 0;
    
    //❌ : 메서드가 실행될 때마다 반환하는 객체는 다른 객체이다.
                ~~~
                //최종적으로 반환하는 값도 객체로 만들어 항상 같은 값을 반환하도록 함.
                return {value:colors[colorIndex], done:false} //이터레이터
                ~~~
    
    //⭕ : colors객체는 항상 같은 객체이므로 아래와 같이 수정하는 것이 맞다.
    				return colors[colorIndex];
            }
        };  
    };
    ```
    
- **사용하는 쪽 코드 예시**
    
    ```jsx
    const rainbowIterator = createRainbowColorGenerator();
    
    setInterval(function(){
        document.querySelector('.rainbow')
            .style['background-color'] = rainbowIterator.getNextColor();
    },500);
    ```
    
    - setInterval로 0.5초 마다 다음 색상이 나오도록 하더라도
    - 항상 같은 객체(`colors`)를 반환받으므로 결과가 달라지지 않는다고 할 수 있다.(getNextColor메서드는 항상 colors객체를 리턴한다.)
    - 또한, createRainbowColorGenerator()를 실행해 객체를 리턴받았으므로, getNextColor()는 메서드가 되어 다른 콘텍스트와 간섭하지 않는다.
        
        ```jsx
        //rainbowIterator에 할당되는 객체.
        //getNextColor함수를 메서드로 가지고 있다.
        
        //외부에서 사용하는 것은 이 객체의 메서드일 뿐이다.
        return { 
                //colors 객체를 참조하여 반환하는 함수를 메서드로 만들어줌
                getNextColor(){
                    if(++colorIndex >= colors.length) colorIndex = 0;
        
        //최종적으로 반환하는 값은 colors객체이므로 값이 변하지 않는다.
                    return colors[colorIndex];
                }
            };
        ```
        

🐱‍💻**스터디 시간에 설명이 부족했던 부분 보충**

- 위 코드에서 **`createRainbowColorGenerator`** 함수는 새로운 컬러 제너레이터를 생성하고 반환한다.
- 이 컬러 생성기 객체에는 **`getNextColor`** 메서드가 있으며, 이 메서드를 호출할 때마다 다음 색상을 가져온다.
- **`colors`** 배열이 함수 내부에 캡슐화되어 있으며, 외부에서 **`colors`** 배열을 직접 변경할 수 없어 순수 함수의 조건을 유지할 수 있다.
- 위의 코드에서 **`createRainbowColorGenerator`** 함수는 매번 같은 **`colors`** 배열을 사용하며, **`getNextColor`** 메서드는 **`colorIndex`**를 변경하면서 **`colors`** 배열의 원소를 반환한다.
- 이 때 **`colorIndex`**의 변경은 함수 내부에서 일어나며, 외부 상태에는 영향을 주지 않는다. 또한 **`colors`** 배열의 내용은 변경되지 않는다.
- 따라서 **`getNextColor`** 메서드는 동일한 입력(**`colorIndex`**의 현재 값)에 대해서 항상 같은 출력(**`colors`** 배열의 현재 원소)을 반환하고, 외부 상태에 영향을 주지 않는다는 순수 함수의 조건을 만족한다. (ChatGPT의 의견이다.)

---

### 🤔 의문 2
### **`createRainbowColorGenerator`** 함수와 메서드까지 완벽한 순수함수로 만들어보자.

- 하지만 난 ChatGPT의 답변이 정말 맞는지 의심이 되어 아예 의심의 싹을 잘라버리기로 했다.
- getNextColor메서드가 항상 같은 오브젝트를 반환하게 하고, 내부 프로퍼티의 값만 변경하는 것이다. 아래와 같이 수정해보았다.
    
    ```jsx
    function createRainbowColorGenerator(){
    
        const colors = ['red','orange','yellow','green','blue','indigo','purple'];
        let colorIndex = -1;
    
        const resultObj = {value:undefined};
        
        return { 
            getNextColor(){
                if(++colorIndex >= colors.length) colorIndex = 0;
                resultObj.value = colors[colorIndex];
                return resultObj;
            }
        };  
    };
    
    const rainbowIteratorA = createRainbowColorGenerator();
    console.log('rainbowIteratorA-1',rainbowIteratorA.getNextColor().value);
    console.log('rainbowIteratorA-2',rainbowIteratorA.getNextColor().value);
    console.log('rainbowIteratorA-3',rainbowIteratorA.getNextColor().value);
    console.log('rainbowIteratorA-4',rainbowIteratorA.getNextColor().value);
    
    const rainbowIteratorB = createRainbowColorGenerator();
    console.log('rainbowIteratorB-2',rainbowIteratorB.getNextColor().value);
    console.log('rainbowIteratorB-3',rainbowIteratorB.getNextColor().value);
    console.log('rainbowIteratorB-1',rainbowIteratorB.getNextColor().value);
    console.log('rainbowIteratorB-4',rainbowIteratorB.getNextColor().value);
    ```
    
    - **`getNextColor`** 메서드는 항상 동일한 입력(**`colorIndex`** 값)에 대해 항상 동일한 출력(객체 **`resultObj`** 에 저장된 **`value`** )을 반환한다.
    - 또한 외부 상태에 영향을 주지 않으며, **`resultObj`** 에 저장된 값만 변경된다.
    - 비로소 의심의 여지가 없는 순수함수가 완성되었다.

- 객체의 **`getNextColor`** 메서드를 사용해 외부의 간섭을 피하는 이유
    - 이것은 동적 스코프와 비슷한 개념이다.
    - 자바스크립트는 정적 스코프이지만, 동적스코프 처럼 사용하는 방법이 바로 객체를 사용해 **새로운 컨텍스트를 형성**해주는 것이다.

---

### 🤔 의문 3
### 정말로 **`createRainbowColorGenerator`** 함수의 호출 구문을 새로운 변수에 할당할 때마다 다른 컨텍스트가 생성되는 것이 맞나 확인해보자.

- **코드**
    
    ```jsx
    function createRainbowColorGenerator(){
    
        const colors = ['red','orange','yellow','green','blue','indigo','purple'];
        let colorIndex = -1;
        
        return { 
            getNextColor(){
                if(++colorIndex >= colors.length) colorIndex = 0;
                return colors[colorIndex];
            }
        };  
    };
    
    const rainbowIteratorA = createRainbowColorGenerator();
    console.log('rainbowIteratorA-1',rainbowIteratorA.getNextColor());
    console.log('rainbowIteratorA-2',rainbowIteratorA.getNextColor());
    console.log('rainbowIteratorA-3',rainbowIteratorA.getNextColor());
    console.log('rainbowIteratorA-4',rainbowIteratorA.getNextColor());
    
    const rainbowIteratorB = createRainbowColorGenerator();
    console.log('rainbowIteratorB-1',rainbowIteratorB.getNextColor());
    console.log('rainbowIteratorB-2',rainbowIteratorB.getNextColor());
    console.log('rainbowIteratorB-3',rainbowIteratorB.getNextColor());
    console.log('rainbowIteratorB-4',rainbowIteratorB.getNextColor());
    ```
    
    - **실행결과**
    
    ```jsx
    rainbowIteratorA-1 red
    rainbowIteratorA-2 orange
    rainbowIteratorA-3 yellow
    rainbowIteratorA-4 green
    rainbowIteratorB-2 red
    rainbowIteratorB-3 orange
    rainbowIteratorB-1 yellow
    rainbowIteratorB-4 green
    ```
    

---

### 순수함수를 써야하는 이유

1. **예측 가능성**: 순수 함수는 입력 값에만 의존하므로 항상 동일한 결과를 반환한다. 이는 코드를 예측 가능하게 만들어 디버깅 및 테스트를 용이하게 한다.
2. **테스트 용이성**: 순수 함수는 입력과 출력 사이에 외부 의존성이 없기 때문에 단위 테스트를 쉽게 작성할 수 있다. 입력을 제공하고 결과를 검증하는 것 만으로도 함수의 동작을 확인할 수 있다.
3. **병렬성과 병행성**: 순수 함수는 외부 상태에 의존하지 않기 때문에 여러 개의 쓰레드나 프로세스에서 병렬로 실행되거나 순차적으로 실행될 수 있다.
4. **리팩토링 용이성**: 외부 상태에 의존하지 않는 순수 함수는 코드를 재구성하거나 리팩토링하는 데 용이하다. 다른 부분에 영향을 미치지 않고 해당 함수를 수정할 수 있다.
5. **캐싱 및 최적화**: 동일한 입력에 대한 결과가 항상 같기 때문에 순수 함수는 결과를 캐싱하여 성능을 최적화하는 데 활용할 수 있다.
6. **부작용 최소화**: 순수 함수는 외부 상태를 변경하지 않기 때문에 부작용(side effect)을 최소화할 수 있다. 이로 인해 코드의 예측성과 안정성이 증가한다.

- 함수가 상황에 따라 다른 값을 반환하거나 사이드이펙트가 있다면, 그 함수는 컨텍스트에 좌우되는 함수이다.
- 부수효과가 있는 함수는 컨텍스트가 달라지면 버그를 일으킬 가능성이 있다.
- 순수한 함수를 쓰면
    - 코드를 테스트 하기 쉽고,
    - 이해하기 쉽고,
    - 재사용하기 쉽다.
- 부수 효과는 스코프를 잘 사용하여 제어할 수 있다.