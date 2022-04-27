# 타입스크립트 2

- 강제적으로 값이 있다고 명시하기 '!'

```js
const obj: {
  name: string;
  age?: number;
} = {
  name: 'mkp',
  age: 38,
};

if (obj.age! > 10) {
  console.log(1);
}
```

- 오브젝트에서 readonly 프로퍼티를 만들고 싶을 경우

```js
type Player = {
  readonly name: string;
  age?: number;
};

// interface 도 가능하다
interface Player {
  readonly name: string;
  age?: number;
}
```

- array 에도 적용가능하다

```js
const names: readonly number[] = [1, 2, 3, 4];
```

- 여러타입이 혼합된 array 사용 튜플이라 부름

```js
const person: readonly [string, number, boolean] = ['22', 99, true];
```

- 참고, array 의 본모습?

```js
{
  0: 'sun',
  1: 'mon',
  2: 'tue',
  3: 'wed',
  4: 'thu',
  5: 'fri',
  6: 'sat',
  readonly length: 7,
}
```

- void
- void는 값을 반환하지 않는 함수의 반환 값을 나타냅니다. 함수에 return 문이 없거나 해당 return 문에서 명시적 값을 반환하지 않을 때 항상 유추되는 타입입니다.

- unknown
- unknown타입은 모든 값을 나타냅니다. 이것은 any타입과 비슷하지만 any보다 unknown이 더 안전합니다. 이유는 unknown값으로 작업을 수행하는 것은 합법적이지 않기 때문입니다.

- never
- 일부 함수는 값을 반환하지 않습니다. 이는 함수가 예외를 throw하거나 프로그램 실행을 종료함을 의미합니다.

- 함수에 타입 정의

```js
type Add = (a: number, b: number) => number;

const add: Add = (a, b) => a + b;
```

- Overloads: 동일한 이름에 매개 변수와 매개 변수 타입 또는 리턴 타입이 다른 여러 버전의 함수

```js
type Add = {
  (a: number, b: number): number,
  (a: string, b: number): number,
};

const add: Add = (a, b) => {
  if (typeof a === 'string') {
    return b;
  }
  return a + b;
};
```

```js
type Add = {
  (a: number, b: number): number,
  (a: number, b: number, c: number): number,
};

// 갯수가 다를경우 옵셔널로 처리하고 다시한번 타입이 뭔지 알려준다.
const add: Add = (a, b, c?: number) => {
  if (c) {
    return a + b + c;
  }
  return a + b;
};
```

- 제네릭

```js
type Print = {
  <T>(arr: T[]): void,
};

const superPrint: Print = (arr) => {
  arr.forEach((element) => {
    console.log(element);
  });
};

superPrint([1, 2, 3, 4]);
superPrint(['1', '2', '3', '4']);
```

- 타입에서 제네릭 사용법

```js
type Player<T> = {
  name: string,
  info: T,
};

const mkp: Player<{
  age: number,
}> = {
  name: 'mkp',
  info: { age: 36 },
};
```

- public, protected, private 차이

  | 접근 가능성      | public | protected | private |
  | ---------------- | ------ | --------- | ------- |
  | 클래스 내부      | O      | O         | O       |
  | 자식 클래스 내부 | O      | O         | X       |
  | 클래스 인스턴스  | O      | X         | X       |

- interface 의 class 상속
- interface 는 private, protected 사용 할 수없다.

```js
interface User {
  firstName: string;
  lastName: string;
  sayHello(name: string): string;
  getFullName(): string;
}

interface Human {
  health: string;
}

class Church implements User, Human {
  constructor(
    public firstName: string,
    public lastName: string,
    public health: string
  ) {}
  sayHello(name: string): string {
    return `Welcome Home. ${name}`;
  }
  getFullName(): string {
    return this.firstName + ' ' + this.lastName;
  }
}
```

- typescript 다형성

```js
interface IStorage<T> {
  [key: string]: T;
}

class LocalStorage<T> {
  private storage: IStorage<T> = {};

  get(key: string): T {
    return this.storage[key];
  }
  set(key: string, value: T) {
    this.storage[key] = value;
  }
  remove(key: string) {
    delete this.storage[key];
  }
  clear() {
    this.storage = {};
  }
}

const ls_string = new LocalStorage<string>();
ls_string.set('k', 'k');
ls_string.set('d', 'd');
ls_string.clear();
console.log(ls_string);
```

- "strict": true 로 되어있을때, 자바스크립트 패키지를 사용할때는 사용할때 type이 정의된 파일이 필요하다
- {패키지}.d.ts 파일을 만든다.

```js
interface Config {}

declare module 'myPackage' {
  function init(config?: Config): boolean;
  function exit(code: number): number;
}
```

- 내가 만든 자바스크립트를 그대로 사용하고 타입스크립트의 도움을 받고싶을때, jsDoc을 사용한다
- 최상단에 '// @ts-check' 를 입력하고 규칙에 맞게 입력해준다

```js
// @ts-check

/**
 * @param {object} config
 * @param {boolean} config.debug
 * @param {string} config.url
 * @return {boolean}
 */
export const init = (config) => {
  return true;
};

/**
 * @param {number} code
 * @return {number}
 */
export const exit = (code) => {
  return code - 1;
};
```
