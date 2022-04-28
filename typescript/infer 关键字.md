使用`infer`关键字在条件类型中进行推断。
```ts
type Flatten<Type> = Type extends Array<infer Item> ? Item : Type;
```
上面代码中，使用`infer`关键字声明了一个名为`Item`的新泛型类型变量。

---

```ts
type GetReturnType<Type> = Type extends (...args: never[]) => infer Return ? Return : never;

type Num = GetReturnType<() => number>; // type Num = number
type Str = GetReturnType<(x: string) => string>; // type Str = string
type Bools = GetReturnType<(a: boolean, b: boolean) => boolean[]>; // type Bools = boolean[]
type Var = GetReturnType<string>; // varReturnType 类型为 never
```

---

对于类似重载函数的类型，会从最后一个进行推断。

```ts
declare function stringOrNum(x: string): number;
declare function stringOrNum(x: number): string;
declare function stringOrNum(x: string | number): string | number;

type T1 = ReturnType<typeof stringOrNum>; // type T1 = string | number
```

---

用于解包

```ts
type Ids = number[];  
type Names = string[];  
type Unpacked<T> = T extends (infer R)[] ? R : T;  
type idType = Unpacked<Ids>; // idType 类型为 number
type nameType = Unpacked<Names>; // nameType 类型为string

type Response = Promise<number[]>;
type Unpacked<T> = T extends Promise<infer R> ? R : T;
type resType = Unpacked<Response>; // resType 类型为number[]
```

---

推断联合类型

```ts
type Foo<T> = T extends { a: infer U; b: infer U } ? U : never;
type T10 = Foo<{ a: string; b: string }>; // T10类型为 string
type T11 = Foo<{ a: string; b: number }>; // T11类型为 string | number

const arr = [1, true, 'str', () => {}];  
type TTuple = typeof arr;  
type ElementOf<T> = T extends (infer R)[] ? R : never;  
type Union = ElementOf<TTuple>; // Union 类型为 number | boolean | string | () => void
```