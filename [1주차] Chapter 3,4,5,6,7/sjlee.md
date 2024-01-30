# 3.1 타입스크립트만의 독자적 타입 시스템

## any 타입

### 특징

1. 자바스크립트에 존재하는 모든 값을 오류없이 받을 수 있다.

2. 타입을 명시하지 않은 것과 동일한 효과를 나타낸다.

3. 아래와 같이 any로 지정한 타입은 어떠한 값을 할당하더라도 오류가 발생하지 않는다.
   ```ts
   let state: any;
   // 객체 할당
   state = { value: 0 };
   // 숫자 할당
   state = 100;
   //문자열 할당
   state = 'hello world';
   // 중첩구조로 들어가 함수 할당
   state.inner = () => console.log('any type');
   ```

### 효용성 측면

- 타입스크립트를 사용한 정적 타이핑의 의미를 무색하게 만들 수 있음

- 따라서 any 타입을 변수에 할당하는 것은 지양해야하는 패턴

- 하지만 타입스크립트에서 any타이블 어쩔 수없이 사용해야하는 3가지의 대표적인 경우 존재

### any 타입을 사용하는 대표적인 경우

1. 개발단계에서 임시로 값을 지정해야 할 때

2. 어떤 값을 받아올지 또는 넘겨줄지 정할 수 없을 때

3. 값을 예측할 수 없을 때 암묵적으로 사용

## unknown 타입

  ```ts
  let anyData: any;

  anyData = 'string';
  anyData = () => {
    console.log('function');
  };
  anyData = 100;

  let anyData2: string = anyData; // 할당 가능
  ```

  ```ts
  let unknownData: unknown;

  unknownData = 100;
  unknownData = 'string';
  unknownData = () => {
    console.log('function');
  };

  let unknownData3: any = unknownData; // 할당 가능
  let unknownData2: string = unknownData; // "'unknown' 형식은 'string' 형식에 할당할 수 없습니다." 에러 발생
  ```

### unknown 타입이 추가된 이유

- unknown 타입은 어떤 타입이 할당되었는지 알 수 없음을 나타내기 때문에 unknown타입으로 선언된 변수는 값을 가져오거나 내부 속성에 접근할 수 없음

- unknown 타입으로 할당된 변수는 어떤 값이든 올 수 있음을 의미하는 동시에 개발자에게 엄격한 타입 검사를 강제

- any 타입과 유사하지만 타입 검사를 강제하고 타입이 식별된 후에 사용할 수 있기 때문에 any 타입보다 더 안전

- 데이터 구조를 파악하기 힘든 경우 any 타입보다 unknown 타입으로 대체해서 사용하는 방식을 권장

## void 타입


### 변수에서 void 사용

- void는 주로 함수의 반환 타입으로 사용하지만 함수에 국한된 타입은 아니다. 다만 함수가 아닌 값에 대해서는 대부분 무의미하다.

- void로 타입이 지정된 변수는 undefined 또는 null만 할당할 수 있다.
- 그런데 만약 tsconfig.json에서 strictNullchecks옵션이 설정 되어 있거나 해당 플래그 설정이 실행되면 null값을 할당할 수 없다.

## never 타입

- never 타입도 void와 마찬가지로 타입의 값을 반환할 수 없은 타입을 말한다. 보통은 에러를 던지는 경우와 무한히 함수가 실행되는 경우에 사용된다.

  ```ts
  // 에러를 던지는 경우
  function generateError(res: Response): never {
    throw new Error(res.getMessage());
  }

  // 무한히 함수 실행
  function checksStatus(): never {
    while (true) {
      //...
    }
  }
  ```

> void와 never의 차이를 말하자면 함수의 완료여부에 달려있다. 에러를 던지거나 무한히 함수가 실행되는 것은 함수 자체가 끝난 것이 아니다.

## Array 타입


### 정적타이핑

- 선언하는 방식으로는 크게 두가지가 있다.

  - 자료형 + [ ]
    ```ts
    const array: number[] = [1, 2, 3];
    ```
  - Array + 제네릭
    ```ts
    const array: Array<number> = [1, 2, 3];
    ```

- 만약 여러 타입을 모두 관리해야 하는 배열을 선언하고 싶은 경우에는 유니온 타입을 사용할 수 있다.

  ```ts
  const array1: Array<number | string> = [1, 'string'];
  const array2: number[] | string[] = [1, 'string'];
  const array3: (number | string)[] = [1, 'string'];
  ```

### 튜플

- 튜플은 배열 기능에 길이 제한까지 추가한 타입 시스템이다.

- 대괄호와 타입시스템을 사용하여 선언할 수 있으며 대괄호 안에 선언하는 타입의 개수가 튜플이 가질 수 있는 원소의 개수를 나타낸다.

  ```ts
  let tuple: [number] = [1]; // O, 가능

  tuple = [1, 2]; // X, 불가능
  tuple = [1, 'string']; // X, 불가능

  let tuple2: [number, string, boolean] = [1, 'hello world', true]; // O, 가능
  ```
> 이처럼 타입을 제한하는 것은 자바스크립트의 런타임 에러와 유지 보수의 어려움을 막기 위한 것이며 특히 튜플의 경우 컨벤션을 잘 지키고 각 배열 원소의 명확한 의미와 쓰임을 보장할 때 더욱 안전하게 사용할 수 있다.

## enum 타입

  ```ts
  enum ProgrammingLanguage {
    Typescript, // 0
    Javascript, // 1
    Java, // 2
    Python, // 3
    Rust, // 4
  }

  // ProgrammingLanguage의 멤버로 접근
  ProgrammingLanguage.Typescript; // 0
  ProgrammingLanguage.Javascript; // 1
  ProgrammingLanguage.Python; // 3
  ProgrammingLanguage['Rust']; // 4

  // 역방향 접근도 가능
  ProgrammingLanguage[2]; // Java
  ```

- 명시적인 값의 할당도 가능하며 일부 멤버에 값을 할당하지 않아도 누락된 멤버를 아래와 같은 방식으로 이전 멤버를 기준으로 1씩 늘려가며 자동으로 할당한다.

  ```ts
  enum ProgrammingLanguage {
    Typescript = 'TypeScript',
    Javascript = 'JavaScript',
    Java = 300,
    Python = 400,
    Rust, // 401
    Go, // 402
  }
  ```

- enum은 주로 문자열 상수를 생성하는데 사용되며 이를 통해 응집력 있는 집합 구조체를 만들어 사용자 입장에서 간편하게 활용할 수 있다.

- 또한 그 자체로 변수 타입으로 지정할 수 있으며 열거형을 타입으로 가지는 변수는 해당 열거형이 가지는 모든 멤버들을 값으로 받을 수 있다.

- 다음과 같이 itemStatus라는 인자가 ItemStatusType라는 열거형을 타입으로 가지면 문자열로 타입이 지정되었을때와 비교하여 다음과 같은 효과가 있다.

  ```ts
  enum ItemStatusType {
    DELIVERY_HOLD = 'DELIVERY_HOLD',
    DELIVERY_READY = 'DELIVERY_READY',
    DELIVERING = 'DELIVERING',
    DELIVERED = 'DELIVERED',
  }

  const checkItemAvailable = (itemStatus: ItemStatusType) => {
    switch (itemStatus) {
      case ItemStatusType.DELIVERY_HOLD:
      case ItemStatusType.DELIVERY_READY:
      case ItemStatusType.DELIVERING:
        return false;
      case ItemStatusType.DELIVERED:
      default:
        return true;
    }
  };
  ```

  1. **타입안전성**: ItemStatusType에 명시되지 않은 다른 문자열은 인자로 받을 수 없어 타입 안전성이 우수하다.

  2. **명확한 의미 전달과 높은 응집력**: ItemStatusType이 다루는 값이 무엇인지 명확하고 아이템 상태에 대한 값을 모아놓은 것으로 응집력이 우수하다.

  3. **가독성**: 응집도가 높기 때문에 말하고자 하는 바가 더욱 명확하다. 열거형 멤버를 통해 어떤 상태를 나타내는지 쉽게 알 수 있다.

### enum 사용시 주의사항

1. 의도하지 않은 값의 할당이나 접근

2. 불필요한 코드의 크기 증가

# 3.2 타입 조합

## 교차타입(Intersection)

  ```ts
  type ProductItem = {
    id: number;
    name: string;
    price: number;
  };

  type ProductItemWidtDiscount = ProductItem & { discountAmount: number };
  ```

## 유니온 타입(Union)

유니온 타입은 A 또는 B 중 하나가 될 수 있는 타입을 말하며 |을 사용하여 표기한다. 특정 변수가 가질 수 있는 타입을 전부 나열하는 용도로 주로 사용한다. 교차타입과 마찬가지로 두개 이상의 타입을 이을 수 있고 타입 별칭을 사용할 수 있다.

아래 `printPromotionItem()` 함수는 name에는 접근이 가능하지만 price는 컴파일 에러가 뜬다. 그 이유는 ProductItem에만 price가 존재하기 때문이다. 만약 공통 속성이 아닌 price나 type같은 속성에 접근하고 싶을경우에는 **타입가드**를 사용하여 타입을 검증 한 뒤 접근해야 한다.

```ts
type ProductItem = {
  id: number;
  name: string;
  price: number;
};

type CardItem = {
  id: number;
  name: string;
  type: string;
};

type PromotionEventItem = ProductItem | CardItem;

const printPromotionItem = () => {
  console.log(item.name); // O

  console.log(item.price); // X, 컴파일 에러 발생
};
```

## 인덱스 시그니처(Index Signature)

인덱스 시그니처는 특정 타입의 속성 이름은 알 수 없지만 속성값의 타입을 알고 싶을 때 사용한다. 인터페이스 내부에 `[key: K]: T`꼴로 타입을 명시해주면 된다. 이는 해당 타입의 속성 키는 모두 K타입이며 value는 T타입을 가져야 한다는 의미다.

```ts
// 기본 형태
interface IndexSignatureEx {
  [key: string]: number; // key의 이름은 문자열, value는 숫자형
}
```

아래 예시에서 name은 string을 가져오도록 되어있지만 인덱스 시그니처에서 key가 string일 경우에는 number | boolean이 오게끔 선언되어 있어 에러가 발생한다.

```ts
// 기본 형태
interface IndexSignatureEx2 {
  [key: string]: number | boolean; // key의 이름은 문자열, value는 숫자형
  length: number; // O
  isOpen: boolean; // O
  name: string; // X, 에러 발생
}
```

## 인덱스드 엑세스 타입(Indexed Access Types)

```ts
const PromotionList = [
  { type: 'product', name: 'laptop' },
  { type: 'product', name: 'keyboard' },
  { type: 'card', name: 'SH' },
];

type ElementOf<T> = (typeof T)[number];

type PromotionItemType = ElementOf<PromotionList>;
```

## 맵드 타입(Mapped Types)


```ts
type Example = {
  a: number;
  b: string;
  c: boolean;
};

type Subset<T> = {
  [K in keyof T]?: T[K];
};

const aExample: Subset<Example> = { a: 100 };
const bExample: Subset<Example> = { b: 'string' };
const cExample: Subset<Example> = { c: true };

const dExpample: Subset<Example> = { d: true }; // X, "개체 리터럴은 알려진 속성만 지정할 수 있으며 'Subset<Example>' 형식에 'd'이(가) 없습니다." 에러 발생
```

BottomSheet라는 컴포넌트에는 연락처와 장바구니가 있다고 하자. 여기에서 각각 resolver, isOpened 등의 상태를 관리하는 스토어가 필요한데 이 스토어의 타입을 선언해줘야한다.

이때 모든 키에 대해서 스토어를 만들어 줄 수 있지만 Mapped Types 문법을 사용하여 각 키에 해당하는 스토어를 선언할 수 있다.

```ts
const BottomSheetMap = {
  CONTACT: {},
  CART: {},
};

// BottomId는 "CONTACT" | "CART";' 유니온 타입
type BottomId = keyof typeof BottomSheetMap;

// Mapped Types를 통한 타입 선언
type BottomSheetStore = {
  [key in BottomId]: {
    resolver?: (payload: any) => void;
    args?: any;
    isOpened: boolean;
  };
};
```

또한 맵드 타입에서는 as 키워드를 사용하여 키를 재정의할 수 있다. 만약 위 코드에서 모든 키에 특정 문자열을 붙이는 식으로 공통된 처리를 하고싶으면 다음과 같이 사용하면 된다.

```ts
type BottomSheetStore = {
  [key in BottomId as `${key}_BOTTOM_SHEET`]: {
    resolver?: (payload: any) => void;
    args?: any;
    isOpened: boolean;
  };
};
```

## 템플릿 리터럴 타입(Template Literal Types)

```ts
type Status = 'before' | 'in progress' | 'after';

type TestStatus = `Test-${Status}`;
```

## 제네릭(Generic)

### 제네릭이란

- 제네릭은 일반화된 데이터 타입으로서 함수, 타입, 클래스 등에서 내부적으로 사용할 타입을 미리 **정해두지 않고** 타입 변수를 사용해서 해당 위치를 비워 둔 다음에 그 값을 사용할 때 **외부에서** 타입 변수자리에 **타입을 지정**하여 사용하는 방식으로 사용한다.

- 이럴경우 함수, 타입, 클래스 등 여러 타입에 대해 따로 정의하지 않아도 되기 때문에 재사용성이 크게 향상된다.

  ```ts
  type ExampleType<T> = T[];

  const foodArray: ExampleType<string> = ['떡볶이', '순대', '튀김', '어묵'];
  ```

### 제네릭과 타입추론

- 제네릭 함수를 호출할 때 꺾쇠괄호안에 타입을 명시하는것이 필수는 아니다. 타입을 명시하는 부분을 생략하면 컴파일러가 함수 호출시의 인수를 보고 타입을 추론해준다.

### 제네릭의 기본값

- 제네릭도 = 를 사용하여 기본값을 추가할 수 있다.

### 제네릭 제약

- 제네릭은 일반화된 데이터 타입이다. 따라서 특정 타입에서만 존재하는 멤버를 참조하려고 하면 에러가 발생한다.

- 만약 특정 속성을 제네릭에서 참조하고 싶을 경우에는 제약을 걸어줌으로써 해당 속성을 사용할 수 있게 만들 수 있다.

- 다음의 코드에서 exampleFunc 함수는 제네릭 타입 T를 가지며, 이 타입은 TypeWithLength 인터페이스를 확장한 타입이어야 한다. TypeWithLength 인터페이스는 length 속성을 가져야 하는 제약을 가지고 있는 것이다.

  ```ts
  interface TypeWithLength {
    length: number;
  }

  function exampleFucn<T extends TypeWithLength>(arg: T): number {
    return arg.length;
  }
  ```

- 이러한 제약을 통해 exampleFunc 함수는 T가 TypeWithLength 인터페이스를 만족하는 경우에만 arg.length와 같은 특정 속성을 안전하게 사용할 수 있게 된다.

# 3.3 제네릭 사용법

## 함수의 제네릭

함수의 매개변수나 반환 값에 다양한 타입을 넣고 싶을 때 제네릭을 사용할 수 있다.

다음과 같이 T자리에 넣는 타입에 따라 적절하게 사용될 수 있다.

```ts
function ReadOnlyRepository<T>(
  target: ObjectType<T> | EntitySchema<T> | string
): Repository<T> {
  return getConnection('ro').getRepository(target);
}
```

## 호출 시그니처의 제네릭

- 호출 시그니처: 타입스크립트의 함수 타입 문법, 함수의 매개변수와 반환 타입을 미리 선언하는 것
```ts
export type UserRequestHookType = <RequestData = void, ResponseData = void>(
  baseURL?: string | Headers,
  defaultHeader?: Headers
) => [RequestStatus, Requester<RequestData, ResponseData>];
```

```ts
const defaultHeader: Headers = { header: 'header' };

userRequestFunction<string, number>('baseURL', defaultHeader);
```

이런식으로 사용하면 RequestData에는 string 타입이 ResponseData에는 number 타입이 들어가게 된다.

## 제한된 제네릭

제한된 제네릭은 타입 매개변수에 대한 제약 조건을 설정하는 기능을 말한다.

만약 A라는 타입을 B라는 타입으로 제약을 하기 위해서 A 타입의 매개변수는 `extends`키워드를 사용하여 B 타입을 상속해야한다.

예시 코드는 다음과 같다.

```ts
type Student = {
  name: string;
  age: number;
};

function printStudent<T extends Student>(obj: T, key: keyof T): void {
  console.log(obj[key]);
}
```

printStudent의 타입 매개변수 T는 Student라는 타입으로 제약 조건이 설정되어 있다.
이처럼 타입 매개변수가 특정 타입에 묶여 있을 때 해당 키를 바운드 타입 매개변수라 부른다.

또한 상속된 Student는 T의 상한 한계라고 부른다.

> 추가로 타입스크립트는 **구조적 타이핑**의 특성을 지니고 있으므로 제한된 타입과 유사한 타입의 값을 넘겨받을 경우에는 에러가 발생하지 않는다.

> 하지만 유사하지 않은 타입을 값을 넘겨받을 경우 컴파일 에러가 발생한다.

```ts
// 제한된 제네릭으로 상속받은 Student 타입과 유사한 타입의 값을 넘겨받을 경우 컴파일 에러가 발생하지 않음
printStudent({ name: 'jay', age: 25 }, 'name'); // jay
printStudent({ name: 'mark', age: 20, class: 'A' }, 'name'); // mark
printStudent({ name: 'jhon', class: 'A', age: 27 }, 'name'); // jhon

// printStudent({name:"jay",class:"A"},"name") // 에러 발생
```

## 확장된 제네릭

제네릭 타입은 여러 타입을 상속받을 수 있으며 타입 매개변수를 여러개 둘 수 있다.

하지만 `<T extends string>` 이런식으로 타입을 제약하면 제네릭의 유연성을 잃을 수 있다.

제네릭의 유연성을 잃지 않으며서 타입을 제약해야 할 때는 타입 매개변수에 유니온 타입 `|`을 상속해서 `<T extends string | number>`와 같이 선언하면 된다.
```ts
// T는 string 또는 number 타입, U는 string 타입
function printType<T extends string | number, U extends string>(
  a: T,
  b: U
): void {
  // T가 string인 경우
  if (typeof a === 'string') {
    console.log(`Type of 'a': string, Value of 'a': ${a.toUpperCase()}`);
  }
  // T가 number인 경우
  else if (typeof a === 'number') {
    console.log(`Type of 'a': number, Value of 'a': ${a.toFixed(2)}`);
  }

  console.log(`Type of 'b': ${typeof b}`);
}
```

## 제네릭 예시

제네릭의 장점은 다양한 타입을 받게 함으로써 코드를 효율적으로 재사용할 수 있는 것이다.

그 중 API 응답 값의 타입을 지정할 때 현업에서 가장 많이 활용된다.

API 응답 값의 타입 지정시의 활용되는 예시는 다음과 같다.

```ts
export interface MobileApiResponse<Data> {
  data: Data;
  statusCode: string;
  statusMessage?: string;
}
```

MobileApiResponse의 `data`라는 속성은 API의 응답 값에 따라서 달라질 것이다. 그러므로 제네릭을 사용하여 타입매개변수인 `Data`로 지정했다.

이후 `interface MobileApiResponse` 를 활용하는 코드는 다음과 같다.

```ts
// 어딘가에 선언된 타입 Price를 넘겨줌
const fetchPriceInfo = (): Promise<MobileApiResponse<Price>> => {
  const priceUrl = 'price URL';
  return request({
    method: 'GET',
    url: priceUrl,
  });
};

// 어딘가에 선언된 타입 Order를 넘겨줌
const fetchOrderInfo = (): Promise<MobileApiResponse<Order>> => {
  const orderUrl = 'order URL';
  return request({
    method: 'GET',
    url: orderUrl,
  });
};
```

이처럼 다양한 API 응답 값의 타입으로 MobileApiResponse를 재사용할 수 있다.

이런식으로 제네릭을 적재적소에 활용하면 가독성을 높이고 효율적인 코드 작성이 가능하지만 굳이 필요하지 않은 곳에서 사용하면 오히려 코드를 복잡하게 만들 수 있다.

### 제네릭을 사용하지 않아도 되는 경우

1. 제네릭을 굳이 사용하지 않아도 되는 타입

   - 제네릭이 필요하지 않을 때 사용하면 코드 길이만 늘어나고 가독성을 해칠 수 있다.

     ```ts
     type Gtype<T> = T;
     type RequirementType = 'USE' | 'UN_USE' | 'NON_SELECT';
     interface Order {
       gerRequirement(): Gtype<RequirementType>;
     }
     ```

     `Gtype` 이라는 이름은 목적의 의미를 담지도 않고 굳이 제네릭을 사용하지 않고 타입 매개변수를 그대로 선언하는 것과 같은 기능을 하고 있다.

     따라서 위의 코드는 다음과 동일하다.

     ```ts
     type RequirementType = 'USE' | 'UN_USE' | 'NON_SELECT';
     interface Order {
       gerRequirement(): RequirementType;
     }
     ```

2. any 사용하기

   - any 타입은 모든 타입을 허용하기 때문에 사실상 자바스크립트와 동일한 방식으로 코드를 작성하는 것과 같다. 따라서 any를 사용하면 제네릭을 포함헤 타입을 지정하는 의미가 사라진다.
     ```ts
     type ReturnType<T = any> {
         //...
     }
     ```

3. 가독성을 고려하지 않은 사용

   - 과도한 제네릭은 가독성을 해쳐 코드의 해석을 어렵게 한다. 복잡한 제네릭은 의미 단위로 분할해서 사용하는 것이 좋다.

     ```ts
     // ReturnType<Record<OrderType,Partial<Record<CommonOrderStatus | CommonReturnStatus,Partial<Record<OrderRoleType,string[]>>>>>>

     type CommonStatus = CommonOrderStatus | CommonReturnStatus;

     type PartialOrderRole = Partial<Record<OrderRoleType, string[]>>;

     type RecordCommonOrder = Record<CommonStatus, PartialOrderRole>;

     type RecordOrder = Record<OrderType, Partial<RecordCommonOrder>>;

     ReturnType<RecordOrder>;
     ```
# 4.1 타입 확장하기

타입 확장은 기존 타입을 사용해서 새로운 타입을 정의하는 것을 말한다. 기본적으로 타입스크립트에서는 extends, 교차 타입, 유니온 타입을 사용하여 타입을 확장한다.

## 4.1.1 타입 확장의 장점

타입 확장의 가장 큰 장점은 코드 중복을 줄일 수 있다는 것이다. 타입스크립트 코드 작성시, 중복되는 타입 선언이 생길 수 있는데, 이 때 중복되는 타입을 반복적으로 선언하는 것보다 기존에 작성한 타입을 바탕으로 타입 확장을 함으로써 불필요한 코드 중복을 줄일 수 있다.

```tsx
/**
 * 메뉴 요소 타입
 * 메뉴 이름, 이미지, 할인율, 재고 정보
 * */
interface BaseMenuItem {
  itemName: string | null;
  itemImageUrl: string | null;
  itemDiscountAmount: number;
  stock: number | null;
}

/**
 * 장바구니 요소 타입
 * 메뉴 타입에 수량 정보 추가
 */
interface BaseCartItem extends BaseMenuItem {
  // BaseMenuItem의 모든 타입을 가지면서, quantity 타입을 추가함.
  quantity: number;
}
```
```tsx
/**
 * 수정할 수 있는 장바구니 요소 타입
 * 품절 여부, 수정할 수 있는 옵션 배열 정보가 추가
 */
interface EditableCartItem extends BaseCartItem {
  isSoldOut: boolean;
  optionGroups: SelectableOptionGroup[];
}

/**
 * 이벤트 장바구니 요소 타입
 * 주문 가능 여부에 대한 정보가 추가
 */
interface EventCartItem extends BaseCartItem {
  orderable: boolean;
}
```

기존 장바구니 요소에 대한 요구 사항이 변경되어도 BaseCartItem 타입만 수정하고 EditableCartItem이나 EventCartItem은 수정하지 않아도 되기 때문에 효율적이다.

## 4.1.2 유니온 타입

```tsx
type MyUnion = A | B;
```

A와 B의 유니온 타입인 MyUnion은 타입 A와 B의 합집합이다. 집합 A의 모든 원소는 집합 MyUnion의 원소이며, 집합 B의 모든 원소 역시 집합 MyUnion의 원소라는 뜻이다. 즉, A 타입과 B 타입의 모든 값이 MyUnion 타입의 값이 된다.

주의해야 할 점은, 유니온 타입으로 선언된 값은 **유니온 타입에 포함된 모든 타입이 공통으로 갖고 있는 속성에만 접근**할 수 있다.

```tsx
interface CookingStep {
  orderId: string;
  price: number;
}

interface DeliveryStep {
  orderId: string;
  time: number;
  distance: string;
}

function getDeliveryDistance(step: CookingStep | DeliveryStep) {
  return step.distance;
  // Property ‘distance’ does not exist on type ‘CookingStep | DeliveryStep’
  // Property ‘distance’ does not exist on type ‘CookingStep’
}
```

함수 본문에서 step.distance를 호출하고 있는데 distance는 DeliveryStep에만 존재하는 속성이다. step이라는 유니온 타입은 CookingStep 또는 DeliveryStep 타입에 해당할 뿐이지 CookingStep이면서 DeliveryStep인 것은 아니다.

> 💡 타입스크립트의 타입을 속성의 집합이 아니라 값의 집합이라고 생각해야 유니온 타입이 합집합이라는 개념을 이해할 수 있다.

## 4.1.3 교차 타입

교차 타입도 기존 타입을 합쳐 필요한 모든 기능을 가진 하나의 타입으로 만드는 것으로 이해할 수 있지만 유니온 타입과 다른 점이 있다.

```tsx
interface CookingStep {
  orderId: string;
  time: number;
  price: number;
}

interface DeliveryStep {
  orderId: string;
  time: number;
  distance: string;
}

type BaedalProgress = CookingStep & DeliveryStep;

function logBaedalInfo(progress: BaedalProgress) {
  console.log(`주문 금액: ${progress.price}`);
  console.log(`배달 거리: ${progress.distance}`);
}
```

```tsx
/* 배달 팁 */
interface DeliveryTip {
  tip: string;
  }
/* 별점 */
interface StarRating {
  rate: number;
}
/* 주문 필터 */
type Filter = DeliveryTip & StarRating;

const filter: Filter = {
  tip: “1000원 이하”,
  rate: 4,
};
```

교차 타입은 두 타입의 교집합을 의미한다고 했다. 그런데 DeliveryTip과 StarRating은 공통된 속성이 없는데도 Filter의 타입은 공집합(never 타입)이 아닌 DeliveryTip과 StarRating의 속성을 모두 포함한 타입이 된다. 왜냐하면 **타입이 속성이 아닌 값의 집합으로 해석되기 때문**이다. 즉, 교차 타입 Filter는 DeliveryTip의 tip 속성과 StarRating의 rate 속성을 모두 만족하는 값이 된다.

교차 타입을 사용할 때 타입이 서로 호환되지 않는 경우도 있다.

```tsx
type IdType = string | number;
type Numeric = number | boolean;

type Universal = IdType & Numeric;
```

Universal은 IdType과 Numeric의 교차 타입이므로 두 타입을 모두 만족하는 경우에만 유지된다. 따라서 “number이면서 number인 경우”만 유효하여 Universal의 타입은 number가 된다.

## 4.1.4 extends와 교차 타입

```tsx
interface BaseMenuItem {
  itemName: string | null;
  itemImageUrl: string | null;
  itemDiscountAmount: number;
  stock: number | null;
}

interface BaseCartItem extends BaseMenuItem {
  quantity: number;
}
```

BaseCartItem은 BaseMenuItem을 확장함으로써 BaseMenuItem의 속성을 모두 포함하고 있다. 즉, BaseCartItem은 BaseMenuItem의 속성을 모두 포함하는 상위 집합이 되고 BaseMenuItem은 BaseCartItem의 부분집합이 된다.

```tsx
type BaseMenuItem = {
  itemName: string | null;
  itemImageUrl: string | null;
  itemDiscountAmount: number;
  stock: number | null;
};

type BaseCartItem = {
  quantity: number;
} & BaseMenuItem;

const baseCartItem: BaseCartItem = {
  itemName: “지은이네 떡볶이”,
  itemImageUrl: “https://www.woowahan.com/images/jieun-tteokbokkio.png”,
  itemDiscountAmount: 2000,
  stock: 100,
  quantity: 2,
};
```

BaseCartItem은 quantity라는 새로운 속성과 BaseMenuItem의 모든 속성을 가진 단일 타입이다. 교차 타입을 사용한 코드에서는 BaseMenuItem과 BaseCartItem을 interface가 아닌 type으로 선언했다. 왜냐하면 **유니온 타입과 교차 타입을 사용한 새로운 타입은 오직 type 키워드로만 선언할 수 있기 때문**이다.

주의할 점은 extends 키워드를 사용한 타입이 교차 타입과 100% 상응하지 않는다는 것이다.

### extends 키워드를 사용한 예시

```tsx
interface DeliveryTip {
  tip: number;
}

interface Filter extends DeliveryTip {
  tip: string;
  // Interface ‘Filter’ incorrectly extends interface ‘DeliveryTip’
  // Types of property ‘tip’ are incompatible
  // Type ‘string’ is not assignable to type ‘number’
}
```

🚨 DeliveryTip을 extends로 확장한 Filter 타입에 string 타입의 속성 tip을 선언하면 tip의 타입이 호환되지 않는다는 에러가 발생한다.

### 교차 타입으로 작성한 예시

```tsx
type DeliveryTip = {
  tip: number;
};

type Filter = DeliveryTip & {
  tip: string;
};
```

extends를 &로 바꿨을 뿐인데 에러가 발생하지 않는다. 이때 tip 속성의 타입은 “never”이다.

type 키워드는 교차 타입으로 선언되었을 때 새롭게 추가되는 속성에 대해 미리 알 수 없기 때문에 선언 시 에러가 발생하지 않는다. **하지만 tip이라는 같은 속성에 대해 서로 호환되지 않는 타입이 선언되어 결국 never 타입이 된 것이다.**


### 4.2.1 타입 가드에 따라 분기 처리하기

타입스크립트에서의 분기 처리는 조건문과 타입 가드를 활용하여 변수나 표현식의 타입 범위를 좁혀 다양한 상황에 따라 다른 동작으로 수행하는 것을 말한다. (\*타입 가드 : 런타임에 조건문을 사용하여 타입을 검사하고 타입 범위를 좁혀주는 기능)

> 👶🏻 : 어떤 함수가 A | B 타입의 매개변수를 받을 때, 인자 타입이 A 또는 B일 때를 구분해서 로직 처리하고 싶을 땐 어떻게 해야 할까? <br/>
> 👨🏻‍🦳 : if문을 사용해서 처리하면 될 것 같은데! <br/>
> 👶🏻 : if문을 사용하면 컴파일 시 타입 정보는 모두 제거되어 런타임에 존재하지 않기 때문에 타입을 사용하여 조건을 만들 수는 없어. 컴파일해도 타입 정보가 사라지지 않는 방법을 사용해야 해.

타입에 따라 분기 처리를 하기 위해서는 특정 문맥 안에서 1) 타입스크립트가 해당 변수를 타입 A로 추론하도록 유도하면서 2) 런타임에서도 유효한 방법이 필요한데, 이때 **타입 가드**를 사용하면 된다. 타입 가드는 크게 **자바스크립트 연산자를 사용한 타입 가드**와 **사용자 정의 타입 카드**로 구분할 수 있다.

### 자바스크립트 연산자를 활용한 타입 가드

- typeof, instanceof, in과 같은 연산자를 사용해서 제어문으로 특정 타입 값을 가질 수밖에 없는 상황을 유도하여 자연스럽게 타입을 좁히는 방식
- 자바스크립트 연산자를 사용하는 이유 : 런타임에 유효한 타입 가드를 만들기 위해 (런타임에 유효하다 == TS뿐만 아니라 JS에서도 사용할 수 있는 문법이어야 한다.)
- 활용 예시 : 원시 타입을 추론할 때(typeof 연산자), 인스턴스화된 객체 타입을 판별할 때(instanceof 연산자), 객체의 속성이 있는지 없는지에 따른 구분(in 연산자)

### 사용자 정의 타입 가드

- 사용자가 직접 어떤 타입으로 값을 좁힐지를 직접 지정하는 방식
- 활용 예시 : 타입 명제인 함수를 정의하여 사용(is 연산자)

## 4.2.2 원시 타입을 추론할 때: typeof 연산자 활용

typeof A === B를 조건으로 분기 처리하면, 해당 분기 내에서는 A의 타입이 B로 추론된다. 다만 typeof는 자바스크립트 타입 시스템만 대응할 수 있다. 자바스크립트의 동작 방식으로 인해 null과 배열 타입 등이 object 타입으로 판별되는 등 복잡한 타입을 검증하기에는 한계가 있기 때문에 주로 원시 타입을 좁히는 용도로만 사용할 것을 권장한다.

## typeof 연산자를 사용하여 검사할 수 있는 타입 목록

- string
- number
- boolean
- undefined
- object
- function
- bigint
- symbol

```tsx
const replaceHyphen: (date: string | Date) => string | Date = (date) => {
  if (typeof date === “string”) {
  return date.replace(/-/g, “/”);
  }

  return date;
};
```

## 4.2.3 인스턴스화된 객체 타입을 판별할 때: instanceof 연산자 활용하기

instanceof 연산자는 인스턴스화된 객체 타입을 판별하는 타입 가드로 사용할 수 있다. A instanceof B 형태로 사용하며 A에는 타입을 검사할 대상 변수, B에는 특정 객체의 생성자가 들어간다. instanceof는 A의 프로토타입 체인에 생성자 B가 존재하는지를 검사해서 존재한다면 true, 그렇지 않다면 false를 반환한다. 이러한 동작 방식으로 인해 A의 프로토타입 속성 변화에 따라 instanceof 연산자의 결과가 달라질 수 있다는 점은 유의해야 한다.

```tsx
const onKeyDown = (event: React.KeyboardEvent) => {
  if (event.target instanceof HTMLInputElement && event.key === “Enter”) {
  // 이 분기에서는 event.target의 타입이 HTMLInputElement이며
  // event.key가 ‘Enter’이다
  event.target.blur();
  onCTAButtonClick(event);
  }
};
```

## 4.2.4 객체의 속성이 있는지 없는지에 따른 구분: in 연산자 활용하기

in 연산자는 객체에 속성이 있는지 확인한 다음에 true 또는 false를 반환한다. in 연산자를 사용하면 속성이 있는지 없는지에 따라 객체 타입을 구분할 수 있다. in 연산자는 A in B의 형태로 사용하는데 이름 그대로 A라는 속성이 B 객체에 존재하는지를 검사한다. 프로토타입 체인으로 접근할 수 있는 속성이면 전부 true를 반환한다. in 연산자는 B 객체 내부에 A 속성이 있는지 없는지를 검사하는 것이기 때문에 B 객체에 존재하는 A 속성에 undefined를 할당한다고 해서 false를 반환하는 것은 아니다. delete 연산자를 사용하여 객체 내부에서 해당 속성을 제거해야만 false를 반환한다.

<br/>

```tsx
interface BasicNoticeDialogProps {
  noticeTitle: string;
  noticeBody: string;
}

interface NoticeDialogWithCookieProps extends BasicNoticeDialogProps {
  cookieKey: string;
  noForADay?: boolean;
  neverAgain?: boolean;
}

export type NoticeDialogProps =
| BasicNoticeDialogProps
| NoticeDialogWithCookieProps;

const NoticeDialog: React.FC<NoticeDialogProps> = (props) => {
  if (“cookieKey” in props) return <NoticeDialogWithCookie {...props} />; // NoticeDialogWithCookieProps 타입
  return <NoticeDialogBase {...props} />; // BasicNoticeDialogProps 타입
};
```

NoticeDialogWithCookieProps는 BasicNoticeDialogProps를 상속받고 cookieKey 속성을 가진다. 따라서 두 객체 타입을 cookieKey 속성을 가졌는지 아닌지에 따라 in 연산자로 조건을 만들 수 있다.

## 4.2.5 is 연산자로 사용자 정의 타입 가드를 만들어 활용하기

사용자 정의 타입 가드는 반환 타입이 타입 명제(type predicates)인 함수를 정의하여 사용할 수 있다. 타입 명제는 A is B 형식으로 작성하면 되는데 여기서 A는 매개변수 이름이고 B는 타입이다. 참/거짓의 진릿값을 반환하면서 반환타입을 타입 명제로 지정하게 되면 반환 값이 참일 때 A 매개변수의 타입을 B 타입으로 취급하게 된다.

- 타입 명제(type predicates) : 함수의 반환 타입에 대한 타입 가드를 수행하기 위해 사용되는 특별한 형태의 함수이다.

```tsx
const isDestinationCode = (x: string): x is DestinationCode =>
  destinationCodeList.includes(x);
```

isDestinationCode는 string 타입의 매개변수가 destinationCodeList 배열의 원소 중 하나인지를 검사하여 boolean을 반환하는 함수이다. 함수의 반환값을 booelan이 아닌 x is DestinationCode로 타이핑하여 타입스크립트에게 이 함수가 사용되는 곳의 타입을 추론할 때 해당 조건을 타입 가드로 사용하도록 알려준다.

### isDestinationCode의 반환 값 타이핑을 x is DestinationCode가 아닌 boolean으로 했을 때

```tsx
const isDestinationCode = (x: string): boolean =>
  destinationCodeList.includes(x);
```

```tsx

const getAvailableDestinationNameList = async (): Promise<DestinationName[]> => {
  const data = await AxiosRequest<string[]>(“get”, “.../destinations”);
  const destinationNames: DestinationName[] = [];
  data?.forEach((str) => {
  if (isDestinationCode(str)) { // str이 destinationCodeList의 원소가 맞는지 체크.
    destinationNames.push(DestinationNameSet[str]);
    }
  });
  return destinationNames;
};
```

이 경우 타입스크립트는 isDestinationCode 함수 내부에 있는 includes 함수를 해석해 타입 추론을 할수 없다. 타입스크립트는 if문 스코프의 str 타입을 좁히지 못하고 string으로만 추론한다. destinationNames의 타입은 DestinationName[]이기 때문에 string 타입의 str을 push할 수 없다는 에러가 발생한다.

➡️ 이처럼 타입스크립트에게 반환 값에 대한 타입 정보를 알려주고 싶을 때 is를 사용할 수 있다. 반환 값의 타입을 x is DestinationCode로 알려줌으로써 타입스크립트는 if문 스코프의 str 타입을 DestinationCode로 추론할 수 있다.

## 4.3.1 에러 정의하기

배달의민족 선물하기 서비스에서는 유효성 에러가 발생하면 다양한 방식으로 에러를 보여준다. 이 에러를 크게 텍스트 에러, 토스트 에러, 얼럿 에러로 구분한다. 이들 모두 유효성 에러로 errorCode와 errorMessage를 가지고 있으며, 에러 노출 방식에 따라 추가로 필요한 정보가 있을 수 있다.

```tsx
type TextError = {
  errorCode: string;
  errorMessage: string;
};
type ToastError = {
  errorCode: string;
  errorMessage: string;
  toastShowDuration: number; // 토스트를 띄워줄 시간
};
type AlertError = {
  errorCode: string;
  errorMessage: string;
  onConfirm: () => void; // 얼럿 창의 확인 버튼을 누른 뒤 액션
};
```

각 에러 타입의 유니온 타입을 원소로 하는 배열을 정의해보면 다음과 같다.

```tsx
type ErrorFeedbackType = TextError | ToastError | AlertError;
const errorArr: ErrorFeedbackType[] = [
  { errorCode: “100”, errorMessage: “텍스트 에러” },
  { errorCode: “200”, errorMessage: “토스트 에러”, toastShowDuration: 3000 },
  { errorCode: “300”, errorMessage: “얼럿 에러”, onConfirm: () => {} },
];
```

ErrorFeedbackType의 원소를 갖는 배열 errorArr은 다양한 에러 객체를 관리한다. 다만, 여기서 ToastError의 특수 필드와 AlerError의 특수 필드를 모두 가지는 객체가 있다면, 타입 에러를 뱉어야할 것이다.

```tsx
const errorArr: ErrorFeedbackType[] = [
  // ...
  {
  errorCode: “999”,
  errorMessage: “잘못된 에러”,
  toastShowDuration: 3000,
  onConfirm: () => {},
  }, // expected error
];
```

하지만 자바스크립트는 “덕 타이핑 언어”이기 때문에 별도의 타입 에러를 뱉지 않는 것을 확인할 수 있다. 이런 상황에서 타입 에러가 발생하지 않는다면 앞으로의 개발에서 의미를 알 수 없는 에러 객체가 생겨날 위험성이 커진다.

## 4.3.2 식별할 수 있는 유니온

따라서 에러 타입을 구분할 방법이 필요하다. 각 타입이 비슷한 구조를 가지지만 서로 호환되지 않도록 만들어주기 위해서는 타입들이 서로 포함 관계를 가지지 않도록 정의해야 한다. 이때 바로 **식별할 수 있는 유니온**을 활용할 수 있다.

```tsx
type TextError = {
  errorType: “TEXT”;
  errorCode: string;
  errorMessage: string;
};
type ToastError = {
  errorType: “TOAST”;
  errorCode: string;
  errorMessage: string;
  toastShowDuration: number;
}
type AlertError = {
  errorType: “ALERT”;
  errorCode: string;
  errorMessage: string;
  onConfirm: () = > void;
};
```

각 에러 타입마다 이 필드에 대한 다른 값을 가지도록 하여 판별자를 달아주면 이들은 포함 관계를 벗어나게 된다.

```tsx
type ErrorFeedbackType = TextError | ToastError | AlertError;

const errorArr: ErrorFeedbackType[] = [
  { errorType: “TEXT”, errorCode: “100”, errorMessage: “텍스트 에러” },
  {
    errorType: “TOAST”,
    errorCode: “200”,
    errorMessage: “토스트 에러”,
    toastShowDuration: 3000,
  },
  {
    errorType: “ALERT”,
    errorCode: “300”,
    errorMessage: “얼럿 에러”,
    onConfirm: () => {},
  },
  {
    errorType: “TEXT”,
    errorCode: “999”,
    errorMessage: “잘못된 에러”,
    toastShowDuration: 3000, // Object literal may only specify known properties, and ‘toastShowDuration’ does not exist in type ‘TextError’
    onConfirm: () => {},
  },
  {
    errorType: “TOAST”,
    errorCode: “210”,
    errorMessage: “토스트 에러”,
    onConfirm: () => {}, // Object literal may only specify known properties, and ‘onConfirm’ does not exist in type ‘ToastError’
  },
  {
    errorType: “ALERT”,
    errorCode: “310”,
    errorMessage: “얼럿 에러”,
    toastShowDuration: 5000, // Object literal may only specify known properties, and ‘toastShowDuration’ does not exist in type ‘AlertError’
  },
];
```

우리가 처음 기대했던 대로 정확하지 않은 에러 객체에 대한 타입 에러가 발생하는 것을 확인할 수 있다.

## 4.2.3 식별할 수 있는 유니온의 판별자 선정

식별할 수 있는 유니온을 사용할 때 주의할 점이 있다. 식별할 수 있는 유니온의 판별자는 **유닛 타입**으로 선언되어야 정상적으로 동작한다. 유닛 타입은 다른 타입으로 쪼개지지 않고 오직 하나의 정확한 값을 가지는 타입을 말한다. null, undefined, 리터럴 타입을 비롯해 true, 1 등 정확한 값을 나타내는 타입이 유닛 타입에 해당한다. 반면 다양한 타입을 할당할 수 있는 void, string, number와 같은 타입은 유닛 타입으로 적용되지 않는다.

## 4.4 Exhaustiveness Checking으로 정확한 타입 분기 유지하기

Exhaustiveness Checking은 모든 케이스에 대해 철저하게 타입을 검사하는 것을 말하며 타입 좁히기에 사용되는 패러다임 중 하나이다. 모든 케이스에 대해 분기 처리해야만 유지보수 측면에서 안전하다고 생각되는 경우 Exhaustiveness Checking을 통해 모든 케이스에 대한 타입 검사를 강제할 수 있다.

## 4.4.1 상품권

선물하기 서비스에는 다양한 상품권이 있다. 상품권 가격에 따라 상품권 이름을 반환해주는 함수를 작성하면 다음과 같다.

```tsx
type ProductPrice = “10000” | “20000”;

const getProductName = (productPrice: ProductPrice): string => {
  if (productPrice === “10000”) return “배민상품권 1만 원”;
  if (productPrice === “20000”) return “배민상품권 2만 원”;
  else {
    return “배민상품권”;
  }
};
```

이때 새로운 상품권이 생겨 ProductPrice 타입이 업데이트되어야 한다고 해보자.

```tsx
type ProductPrice = “10000” | “20000” | “5000”;

const getProductName = (productPrice: ProductPrice): string => {
  if (productPrice === “10000”) return “배민상품권 1만 원”;
  if (productPrice === “20000”) return “배민상품권 2만 원”;
  if (productPrice === “5000”) return “배민상품권 5천 원”; // 조건 추가 필요
  else {
    return “배민상품권”;
  }
};
```

ProductPrice 타입이 업데이트되었을 때 getProductName 함수도 함께 업데이트 되었다. 그러나 getProductName 함수를 수정하지 않아도 별도 에러가 발생하는 것이 아니기 때문에 실수할 여지가 있다. 이처럼 모든 타입에 대한 타입 검사를 강제하고 시다면 다음과 같이 코드를 작성하면 된다.

```tsx
type ProductPrice = “10000” | “20000” | “5000”;

const getProductName = (productPrice: ProductPrice): string => {
  if (productPrice === “10000”) return “배민상품권 1만 원”;
  if (productPrice === “20000”) return “배민상품권 2만 원”;
  // if (productPrice === “5000”) return “배민상품권 5천 원”;
  else {
    exhaustiveCheck(productPrice); // Error: Argument of type ‘string’ is not assignable to parameter of type ‘never’
    return “배민상품권”;
  }
};

const exhaustiveCheck = (param: never) => {
  throw new Error(“type error!”);
};
```

앞에서 추가한 productPrice가 “5000”일 때의 분기 처리가 주석된 상태이다. exhaustiveCheck(productPrice);에서 에러를 뱉고 있는데 5000이라는 값에 대한 분기 처리를 하지 않아서 (철저하게 검사하지 않았기 때문에) 발생한 것이다. 이렇게 모든 케이스에 대한 타입 분기 처리를 해주지 않았을 때, 컴파일타임 에러가 발생하게 하는 것을 Exhaustiveness Checking이라고 한다.

exhaustiveCheck 함수를 자세히 보면, 이 함수는 매개변수를 never 타입으로 선언하고 있다. 즉, 매개변수로 그 어떤 값도 받을 수 없으며 만일 값이 들어온다면 에러를 내뱉는다. 이 함수를 타입 처리 조건문의 마지막 else 문에 사용하면 앞의 조건문에서 모든 타입에 대한 분기 처리를 강제할 수 있다.

이렇게 Exhaustiveness Checking을 활용하면 예상치 못한 런타임 에러를 방지하거나 요구사항이 변경되었을 때 생길 수 있는 위험성을 줄일 수 있다. 타입에 대한 철저한 분기 처리가 필요하다면 Exhaustiveness Checking 패턴을 활용해보길 바란다.
