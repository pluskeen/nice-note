##### 描述基本对象

都能描述对象，但 type 还能描述其他任意类型，例如 string、number 等基本类型、联合或交叉等类型。

```ts
interface IPerson {
	name: string
	age: number
}

type TPerson {
	name: string
	age: number
}

// IPerson 和 TPerson 都可以用来描述 person 对象
const person: IPerson = {
	name: '9527',
	age: 18
}

// 期望描述性别，可能是数字或字符串
type TSex = string | number

const sex: TSex = 0
```


##### 类型拓展

都能进行类型拓展，interface 通过 extends 来实现，type 通过 & 交叉运算符形成交叉类型。

```ts
// 项目中已存在且已使用
interface IBar {
	x: number
	y: number
}

// 拓展后的类型
interface IBarExtend extends IBar {
	z: number
}

const bar: IBarExtend = {
	x: 3,
	y: 9,
	z: 4
}

// 项目中已存在且已使用
type TFoo = {
	x: number
	y: number
}

// 拓展后的类型
type TFooExtend = TFoo & { z: number }

const foo: TFooExtend = {
	x: 6,
	y: 7,
	z: 1
}
```


##### 名字相同时的表现

相同名字的 interface 会合同，相同名字的 type 会报错。

```ts
interface IPerson {
	name: string
	age: number
}

interface IPerson {
	sex: string
	// age: string 相同的属性会报错，提示重复
}

// IPerson 合并
const person: IPerson = {
	name: 'lilei',
	age: 16,
	sex: '男'
}

type TPerson = {
	name: string
	age: number
}

// 会报错，提示重复定义 TPerson
type TPerson = {
	sex: string
}
```

利用同名的 interface 会合并特性，可以自由拓展第三方库的类型，例如拓展 Vue Router 里路由元信息的类型。

```ts
const router = createRouter({
	history: createWebHistory(import.meta.env.BASE_URL),
	routes: [
		{
			path: '/room',
			component: () => import('@/views/home/index.vue'),
			meta: {
				title: '页面名称' // 元信息里添加自定义变量
			}
		}
	]
})

import 'vue-router';
declare module 'vue-router' {
	// 拓展 RouteMeta 里的类型
	interface RouteMeta {
		title?: string
	}
}
```




