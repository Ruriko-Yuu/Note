# 全局 API

## 应用实例 API

### createApp()

创建一个应用实例

- 示例

```js
import { createApp } from "vue";
const app = ctrateApp({});
// or
import { crreateApp } from "vue";
import App from "./App.vue";
const app = createApp(App);
```

### createSSRApp()

以`SSR激活`模式创建一个实例应用，用法与 createApp()完全相同。

### app.mount()

将应用实例挂载在一个元素容器中

- 示例

```js
import {createApp} from 'vue'
const app = createApp(App)
app.mount('#app)
```

### app.unmount()

卸载一个已经挂载的应用实例。卸载一个应用会触发应用组件树内所有组件的卸载生命周期钩子

### app.component()

- 示例

```js
import {createApp} form 'vue'
const app = createApp({})
// 注册一个选项对象
app.component('my-component',{})
// 得到一个已经注册的组件
const Mycomponent = app.component('my-component')
```

### app.directive()

只传递一个名字和指令定义注册全局指令，如只传递一个名字则返回用该名字注册的指令

```js
import { createApp } from "vue";
const app = createApp(App);
// 注册（对象形式的指令）
app.directive("my-directive", {});
// 注册（函数形式的指令）
app.directive("my-directive", () => {});
// 得到一个已注册的指令
const myDirective = app.directive("my-directive");
```

### app.use()

安装一个插件 -示例

```js
import { createApp } from "vue";
import MyPlugin from "./plugins/MyPlugin";
const app = createApp(App);
app.use(MyPlugin);
```

### app.mixin()

逻辑复用，vue3 推荐组合式函数来替代

### app.provide()

提供一个值，可以在应用中的所有后代组件中注入使用

```js
import { inject } from "vue";
app.provide("message", "hello");
export default {
  setup() {
    console.log(inject("message")); // 'hello'
  },
};
```

### app.runWithContext()

`vue3.3+` 使用当前应用作为注入上下文执行的回调函数

- 示例

```js
import { inject } from "vue";
app.provide("id", 1);
const injected = app.runWithContext(() => {
  return inject("id");
});
```

### app.version

获取当前 vue 版本号。制作插件时使用

### app.config

包含对这个应用的配置设定

#### app.config.errorHandler

用于应用内抛出的未捕获错误指定的一个全局处理函数

#### app.config.warnHandler

vue 警告时指定一个自定义处理函数

#### app.config.compilerOptions

配置运行时编译器的选项

#### app.config.compilerOptions.isCustomElement

用于指定一个检查方法来识别原生自定义元素

#### app.config.compilerOptions.whitespace

用于调整模板中空格的处理行为

#### app.config.compilerOptions.delimiters

用于调整模板内文本差值的分隔符

#### app.config.compilerOptions.comments

用于调整是否移除模板中的 html 注释

#### app.config.globalProperties

一个用于注册能够被应用内所有组件实例访问到的全局属性对象

#### app.config,optionMergeStrategies

一个用于定义自定义组件选项的合并策略对象

## 常规

### version

```js
import { version } from "vue";
console.log(version);
```

### nextTick()

等待下一次 DOM 更新刷新工具方法(装填改变后直接刷新数据)

### defineComponent()

在定义 Vue 组件时提供类型推导的辅助函数

### defineAsyncComponent()

定义一个异步加载的组件（懒加载）

### defineCustomElement()

接受参数同 `defineComponent`,不同的是会返回一个原生自定义元素类构造器

# 组合式 API

## setup()

### 访问 Props

`setup`函数的第一个参数是组件的 props.和标准组件一致，一个 setup 函数的 props 是响应式的，会在传入新的 props 时同步更新
如如要解构 props 对象，或者需要将某个 prop 传到一个外部函数中保持响应性，可以使用 toRefs() 和 toRef 两个函数

### setup 上下文

传入`setup`函数的第二个参数是 Setup 上下文对象。上下文对象暴露了其他一些在 setup 可能会用到的值

```js
setup(props,context) {
  // context,attrs 透传Attributes（非响应式对象，等价于￥attrs）
  // context.slots 非响应式对象，等价于$slots
  // context.emit 触发事件（函数，等价于$emit）
}
```

### 暴露公共属性

`expose`函数用于显示的限制该组件暴露出的属性，当父组件通过模板引用访问该组件实例时，将仅能访问的 expose 函数暴露出的内容

```js
export default {
  setup(props, { expose }) {
    // 让组件实例处于关闭状态
    // 即不向父组件暴露任何东西
    expose();

    const publicCount = ref(0);
    const privateCount = ref(0);
    // 有选择的暴露局部状态
    expose({ count: publicCount });
  },
};
```

### 与渲染函数一起使用

`setup`也可以返回一个渲染函数，此时在渲染函数中可以直接使用在同一作用域下声明响应式状态

```js
import { h, ref } from "vue";
export default {
  setup() {
    const count = ref(0);
    return () => h("div", count.value);
  },
};
```

可以通过 expose()解决返回渲染函数时无法返回其他东西的问题

```js
import {h,ref} from 'vue'
export default {
  setuo(props, {expose}) {
    const count = ref(0)
    const increment = () => ++ count.value
  }
  expose({
    increment
  })
  return () => h('div', count.value)
}
```

## 响应式 核心

### ref()

接受一个内部值。返回一个响应式可更改的 ref 对象，此对象只有一个指向内部值的属性 value

- 示例

```js
const count = ref(0);
count.value++;
console.log(count.value); // 1
```

### computed()

接受一个 getter 函数，返回一个只读的响应式对象，该 ref 通过`.value`暴露 getter 函数的返回值，他也可以接受一个带有`get`和`set`函数对象来创建一个可写的 ref 对象

- 示例
  - 一个只读的计算属性 ref
  ```js
  const count = ref(1);
  const pluseOne = computed(() => count.value + 1);
  console.log(pluseOne.value); // 2
  pluseOne.value++; // 错误
  ```
  - 一个可写的计算属性 ref
  ```js
  const count = ref(1)
  const plusOne = computer({
    get: () => count.value + 1
    set: (val) => {
      const.value = val - 1
    }
  })
  plusOne.value = 1
  console.log(count.value) // 0
  ```

### reactive()

返回一个对象的响应式代理

```js
const obj = reactive({ count: 0 });
obj.count++;
```

## 工具函数

### isRefs()

检查某个值是否为 ref

### unref()

如果参数是 ref 则返回内部值，否则返回参数本身

### toRef()

将值、refs 或 getters 规范化为 refs

### toValue()

将值、refs 或 getters 规范化为值，同时也会规范化 getter。如果参数是 getter 他将被调用并返回他的返回值

### toRefs()

将一个响应式的对象转为普通对象，普通对象的每个属性都是指向源对象相应属性的 ref

- 从组合函数中返回响应式对象时，使用 toRefs 可以在消费者组件解构展开返回对象时不是去响应性

```js
function useFeatureX() {
  const state = reactive({
    foo: 1,
    bar: 2,
  });
  return toRefs(state);
}
const { foo, bar } = useFeatureX();
```

### isProxy()

检查一个对象是否由 reactive()、readonly()、shallowReactive() 或 shallowReadonly()创建的代理

### isReactive()

检查一个对象是否由 reactive()、shallowReactive()创建的代理

### isReadonly()

检查传入对象值是否为可读状态

- 通过 readonly() 和 shallowReadonly() 创建的代理都是只读的，因为他们是没有 set 函数的 computed()的 ref

### shallowRef()

- 与 ref() 不同，浅层的 ref 值将会远洋的保存和暴露，且不会深层递归转化为响应式
  示例

```js
const state = shallowRef({ count: 1 });
state.value.count = 2; // 不会触发更改
state.value = { count: 2 }; // 触发更改
```

### triggerRef()

强制触发依赖与浅层 ref 的副作用，这通常在对浅引用的内部值进行深度变更后使用(强制更新 shallowRef)

- 示例

```js
const shallow = shallowRef({
  greet: "Hello, world!",
});
watchEffect(() => {
  console.log(shallow.value.greet);
});
shallow.value.greet = "hello,universe";
// 打印 'hello,universe'
triggerRef(shallow);
```

### customRef()

创建一个自定义的 ref，显式声明对其依赖追踪和更新的触发方式

- 详细信息
  - `customRef()`预期接收一个工厂函数作为参数,这个工厂函数接受`track`和`trigger`两个函数作为参数，并返回一个带有`get`和`set`的方法
  - 一般来说，`track()`应该在`get()`方法中调用，而 trigger() 应该在 set()中调用.然而事实上，你对何时调用他们有完全的控制权
- 示例

```js
// 防抖ref
import {customRef} from 'vue'
export function useDebouncedRef(value, delay = 200) {
  let timeout
  return customRef((track, trigger) => {
    return {
      get() {
        track();
        return value
      }
      set(newValue) {
        clearTimeout(timeout)
        timeout = setTimeout(() => {
          value = newValue
          trigger()
        }, delay)
      }
    }
  })
}
```

### shallowReactive()

reactive() 的浅层作用形式

### shallowReadonly()

readonly 的浅层作用形式

### toRaw()

根据一个 Vue 创建的代理返回其原始对象（即获取由 reactive(), readonly(), shallowReadonly()或 shollowReadonly 创建的代理对应的原始对象）

### markRaw()

将一个对象标记为不可转为代理。返回该对象本身

### effectScope()

创建一个 effect 作用于，可以捕获其中所创建的响应式副作用（计算属性和侦听器），这样捕获到的副作用可以一起处理

```js
const scope = effectScope();
scope.run(() => {
  const doubled = computed(() => counter.value * 2);
  watch(doubled, () => console.log(doubled.value));
  watchEffect(() => console.log("Count:", doubled.value));
});
scope.stop(); // 处理掉当前作用域内所有的effect
```

### getCurrentScope()

如果有的话，返回当前活跃的 effect 作用域

### onScopedDispose()

- 在当前活跃的 effect 作用域上祖册一个处理回调的函数。相关的 effect 作用域停止时调用这个回调函数
- 可用作可复用组合式函数式中 onUnmounted 的替代品，不与组件耦合

## 组合式 API: 生命周期钩子

### onMounted()

注册一个回调函数，在组件挂载完成后执行

### onUpdated()

注册一个回调函数，在组件因为响应式状态变更而更新其 Dom 树结构后调用

### onUnmounted()

注册一个回调函数，在组件实例被卸载后调用

### onBeforeMount()

注册一个钩子，在组件被挂载之前被调用

### onBeforeUpdate()

注册一个钩子，在组件即将因为响应式状态变更时更新其 DOM 树前调用

### onBeforeUnmount()

注册一个钩子，在组件实例被卸载之前调用

### onErrorCaptured()

注册一个钩子，在捕获后代组件传递的错误时调用

### onRenderTracked() dev only

注册一个调试钩子，当组件渲染过程中追踪到响应式依赖时调用

### onRenderTriggered() dev only

注册一个调试钩子，当响应式依赖的变更触发组件渲染时调用

### onActivated()

注册一个回调函数，若组件实例是<KeepAlive>缓存树的一部分，当组件被插入到 dom 树时调用

### OnDeactivated()

注册一个回调函数，若组件实例是<KeepAlive>缓存树的一部分，当组件从 DOM 中移除时被调用

### onServerPrefetch() SSR only

注册一个异步函数，在组件实例在服务器上渲染之前调用

## 依赖注入

### provide()

提供一个值，可以被后代组件注入

### inject()

注入一个由先祖组件或整个应用（通过 app.provide()）提供的值

# 选项式 API

## 状态选项

### data

用于声明组件初始响应式状态的函数

```js
export default {
  data() {
    return { a: 1 };
  },
  created() {
    console.log(this.a); // 1
    console.log(this.$data); // {a:1}
  },
};
```

### props

用于声明一个组件的 props

- 详细信息
  在 vue 中，所有组件的 props 都需要被显示的声明。组件的 props 可以通过两种方式声明 -使用字符串数组的简易形式
  - 使用对象的完整形式。该对象的每个属性键是对应 prop 的名称，值则是该 prop 应具有的类型构造函数，或是更高级的选项

```js
export default {
  props: {
    height: Number, // 类型检查
    age: {
      // 类型检查 + 其他验证
      type: Number,
      default: 0,
      required: true,
      validator: (value) => {
        return value >= 0;
      },
    },
  },
};
```

### computed

用于声明要在组件实力上暴露的计算属性

- 示例

```js
export default {
  data() {
    return {a:1}
  },
  computed: {
    // 只读
    aDouble() {
      return this.a * 2
    },
    aPlus() {
      get() {
        return this.a + 1
      },
      set(v) {
        this.a = v - 1
      }
    }
  },
  created() {
    console.log(this.aDouble) // => 2
    console.log(this.aPlus) // => 2
    this.aPlus = 3
    console.log(this.a) // 2
    console.log(this.aDouble) // 4
  },
}
```

### methods

用于声明要混入到组件实例中的方法

```js
export default {
  data() {
    return { a: 1 };
  },
  method: {
    plus() {
      this.a++;
    },
  },
  created() {
    this.plus();
    console.log(this.a); // => 2
  },
};
```

### watch

用于声明在数据更改时调用的侦听回调

- 详细类型
  `watch`选项期望接受一个对象，其中键设计箭筒响应式组件的实例属性，例如`data`或`computed`声明的属性值是想对应的回调函数。该回调函数接受被侦听源的新值和旧值
  除了一个根级属性，键名也可是是一个简单的由点分隔的路径，如`a.b.c`,不支持复杂的表达式数据源，如果要监听复杂表达式数据源，可以使用命令式的`$watch`API
- 额外参数
  - immediate: 监听器创建时立即触发回调，第一次调用时，旧值将为`undefined`
  - deep: 如果源是对象或数组，则深度遍历源，以便在深度变更时触发回调
  - flush: 调整回调的刷新时机
  - onTrack/onTrigger: 调试侦听器的依赖关系

### emits

用于声明由组件触发的自定义事件

- 详细信息
  可以以两种形式声明触发的事件
  - 使用字符串数组的简易形式
  - 使用对象的完整形式。该对象的每个属性键是事件的名称，值是 null 或一个验证函数
- 示例
  - 数组语法
  ```js
  export default {
    emits: ["check"],
    created() {
      this.$emit("check");
    },
  };
  ```
  - 对象语法
  ```js
  export default {
    emits: {
      // 没有验证函数
      click: null,
      // 具有验证函数
      submit: (payload) => {
        if (payload.email && payload.password) {
          console.warn("Invalid submit event payload!");
          return false;
        }
      },
    },
  };
  ```

### expose

用于声明当组件实例被父组件通过模板引用访问时暴露的公共属性

- 详细信息
  默认情况下，通过$parent、$root 或模板引用访问时，组件实例向父组件暴露所有的实例属性。这可能不是我们希望看到的，因为组件可能拥有一些或保持私有内部状态和方法
- 示例

```js
export default {
  // 只有 `publicMethod`在公共实例上可用
  expose: ["publicMethod"],
  methods: {
    publicMethod() {},
    privateMethod() {},
  },
};
```

## 渲染选项

### template

用于声明组件的字符串模板

### render

用于编程式的创建组件虚拟 DOM 的函数
tips: 同时存在 template 和 render 时，render 有更高的优先级

### compilerOptions

用于配置组件模板的运行时编译器的选项

### slots (仅 Vue3.3+中支持)

一个在渲染函数中以编程方式使用插槽是辅助类型推断的选项

## 生命周期选项

### beforeCreate

在组件实例初始化完成之后立即调用

- 详细信息
  会在组件实例初始化完成、props 解析之后、data()和 computed 等选项处理之前立即调用<br>
  tips： 组合式 APi 中的`setup()`钩子会在所有选项式 API 钩子之前都用，`beforeCreate()`也不例外

### created

在组件实例处理完所有状态相关的选项后调用

- 详细信息
  当这个钩子被调用时，以下内容已经设置完成： 响应式数据、计算属性、方法和侦听器。然而，此时挂载阶段还未开始，因此$el 属性人不可使用

### beforeMount

在组件被挂载之前调用

- 详细信息
  当这个钩子被调用时，组件已经完成其响应状态的设置，但还没有创建 DOM 节点。它即将首次执行 DOM 的渲染过程<br>tips: 这个钩子在服务端渲染时不会被调用

### mounted

在组件被挂载之后调用

- 详细信息
  组件在以下情况被视为已挂载:
  - 所有同步子组件都已被挂载。（不包含 Suspense）树内组件
  - 其自身的 DOM 树已经创建完成并插入了父容器中。 注意仅当根容器在文档中时。才可哟保证组件 DOM 树也在文档中<br>tips: 这个钩子在服务器端不会被调用

### beforeupdate

在组件即将引文一个响应式状态变更而更新 DOM 树之前调用<br/>tips: 这个钩子在服务器端渲染时不会被调用

### updated

组件因为响应式状态变更而更新其 DOM 树后调用<br/>tips: 这个钩子在服务器端渲染时不会被调用

### beforeUnmount

在一个组件实例被卸载之前调用<br>tips: 这个钩子在服务器端渲染不会被调用

### unmounted

在一个组件实例被卸载之后调用<br>tips: 这个钩子在服务器端渲染不会被调用

- 详细信息<br>
  一个组件在以下情况被视为已卸载：

  - 其所有子组件都已被卸载
  - 所有相关响应式作用（渲染作用以及`setup()`时创建的计算属性和侦听器）都已停止<br>

  可以在这个钩子手动清理一些副作用。例如计时器、DOM 事件监听器或者与服务器的连接。<br>这个钩子在服务端渲染不会被调用

### errorCaptured

在捕获后代组件传递的错误时调用

- 详细信息<br>
  错误来源可以从以下几个来源中捕获

  - 组件渲染
  - 事件处理器
  - 生命周期钩子
  - `setup()`函数
  - 侦听器
  - 自定义指令钩子
  - 过渡钩子

  错误传递规则

  - 默认情况下，所有错误都被发送到应用级的`app.config.errorHandler`(前提是这个函数已经被定义)，这些错误都能在一个统一的地方报告给分析服务。
  - 如果组件继承链上或组件链上存在多个 errorCaptured 钩子，对于同一个错误，这些钩子会被按从底至上的顺序被一一调用，类似与 DOM 事件的冒泡机制
  - 如果`errorCaptured`钩子本身抛出了一个错误，那么这个错误和原来捕获到的错误都会发送到`app.config.errorHandler`
  - `errorCaptured`钩子可以通过返回`false`来阻止错误继续向上传递，即表示这个错误已经被处理，应当忽略，它将阻止其他的`errorCaptured`钩子或`app.config.errorHandler`因为这个错误而被调用

### renderTracked `Dev only`

在一个响应式依赖被组件渲染作用追踪后调用<br>
tips: 这个组件仅在开发模式下可用，且在服务器端渲染期间不会被调用

### renderTriggered `Dev only`

在一个响应式依赖被组件触发了重新渲染之后调用

### activated

若组件实例是`<KeepAlive>`缓存树的一部分，当组件被插入到 DOM 中时调用<br>
tips:这个钩子在服务度阿爸渲染时不会被调用

### deactivated

若组件示例是`<KeepAlive>`缓存树的一部分，当组件被从 DOM 中移除时被调用
tips:这个钩子在服务度阿爸渲染时不会被调用

### serverPrefetch `SSR only`

当组件实例在服务器上被如安然之前要完成的一部函数

## 组合选项

### provide

用于提供可以被后代组件注入的值

- 详细信息
  <br>`provide` 和`inject`通常成对一起使用，使一个祖先组件作为其后代组件依赖注入放，无论这个组件层级有多深都可以成功注入，只要他们处于同一条组件链上

  这个`provide` 选项应当是一个对象或是返回一个对象的函数。这个对象包含了可注入其后代组件的属性。你可以在这个对象中使用 Symbol 类型的值作为 key

- 示例

基本使用方式

```js
const s = Symbol();
export default {
  provide: {
    foo: "foo",
    [s]: "bar",
  },
};
```

使用函数可以提供其组件中的状态

```js
export default {
  data() {
    return {msg: 'foo'}
  }
  provide() {
    return {msg: this.msg}
  }
  // 针对上面的例子，所共给的`msg`将不会是响应式的。
}
```

### inject

用于声明要通过上层提供方匹配注入进当前组件的属性

- 详细信息

  该`inject`选项应该是一下两种之一：

  - 一个字符串数组
  - 一个对象，其 key 名就是在当前组件中的本地绑定名称，而他的值应该是一下几种之一：
    - 匹配可用注入的 key（string 或者 Symbol）
    - 一个对象
      - 它的`from`属性是一个 key(string 或者 Symbol)，用于匹配可用的注入
      - 它的`default`属性用作值候补。和 props 的默认值类似，如果他是一个对象。那么应该使用一个工厂函数来创建，以避免多个组件共享一个对象

  如果没有供给相匹配的属性、也没有提供默认值，那么注入的属性将为`undefined`

- 示例

基本使用方式

```JS
  export default {
    inject: ['foo'],
    created() {
      console.log(this.foo)
    }
  }
```

使用注入的值作为 props 的默认值

```js
const Child = {
  inject: ["foo"],
  props: {
    bar: {
      default() {
        return this.foo;
      },
    },
  },
};
```

使用注入的值作为 data

```js
const Child = {
  inject: ["foo"],
  data() {
    return {
      bar: this.foo,
    };
  },
};
```

注入项可以选择是否带有默认值

```js
const Child = {
  inject: {
    foo: {
      default: 'foo'
      from 'bar' // 如从不同的名字属性中注入，使用from指明源属性
    }
  }
}
```

和 props 默认值类似，对于非原始数据类型的值，你需要使用工厂函数

```JS
const Child ={
  inject: {
    foo: {
      from: 'bar',
      default: () => [1,2,3]
    }
  }
}
```

### mixins

一个包含祖先选项的数组，这些选项将被混入到当前组件的实例中<br>
tips: vue3 中不再推荐 mixins，推荐使用组合式 API 的组合式函数
示例

```js
const mixin = {
  created() {
    console.log(1);
  },
};
createApp({
  created() {
    console.log(2);
  },
  mixins: [mixin],
});
// => 1
// => 2
```

### extends

要继承的基类组件

- 详细信息

  使一个组件可以继承另一个组件的选项

- 示例

```js
const CompA = {...};
const CompB = {
  extends: ComA,
  ...
};
```

tips: 不建议用于组合式 api

```js
// 如仍想通过组合Api来继承组件，可以在继承组件的setup()中调用基类组件的setup()
import Base from "./Base.js";
export default {
  extends: Base,
  setup(props, ctx) {
    return {
      ...Base.setup(props, ctx),
    };
  },
};
```

## 其他杂项选项

### name

用于显示声明组件展示时的名称

### inheritAttrs

用于控制是否启用默认组件 attribute 透传行为

### components

一个对象,用于注册对当前组件实例可用的组件

- 示例

```js
import Foo from "./Foo.vue";
import Bar from "./Bar.vue";
export default {
  components: {
    // 简写
    Foo,
    // 注册为一个不同的名称
    RenamedBar: Bar,
  },
};
```

### directives

一个对象,用于注册对当前组件实例，可用的指令

- 示例

```js
export default {
  directives: {
    // 在模板中启用v-focus
    focus: {
      mounted(el) {
        el.focus()
      }
    }
  }
}
<input v-focus>
```

## 组件实例

### $data

从 data 选项函数中返回的对象，会被组件赋为响应式，组件实例将会代理对齐数据对象属性的访问

### props

表示组将当前已解析的 props 对象

- 详细信息

这里只包含通过`props`选项声明的 props。组件实例将会代理其对 props 对象上访问属性的访问

### $el

该组件实例管理 DOM 的根节点

- 详细信息
  `$el` 直到组件挂载完成`mounted`之前都是 underfined
  - 对于单一根元素的组件，$el 将会指向根元素
  - 对于以文本节点为根的组件，$el 将会指向该文本节点
  - 对于以多个元素为根的组件， $el 将是一个仅做占位符的 DOM 节点，vue 使用它来跟踪组件在 Dom 中的位置

### $options

已解析的用于示例化当前组件的组件选项

- 详细类型

  这个`options`对象暴露了当前组件已解析的选项，并且会是一下几种可能来源的合并结果

  - 全局 mixin
  - 组件`extends`的基组件
  - 组件级 mixin

  它通常用于支持自定义组件选项

  ```js
  const app = createApp({
    customOption: "foo",
    created() {
      console.log(this.$options.customOption); // => 'foo'
    },
  });
  ```

### $parent

当前组件可能存在的父组件实例，如果当前组件为顶层组件则为 null

### $root

当前组件树的根组件实例

### $slots

一个表示父组件所传入插槽的对象

### $refs

一个包含 DOM 元素和组件实例的对象，通过模板引用注册

### $attrs

一个包含了组件所有透传 attributes 的对象

### $watch()

用于命令式创建侦听器的 API

- 详细信息

第一个参数是侦听来源
第二个参数是回调函数

- immediate： 指定在侦听器创建时是否立即触发回调，第一次旧值为 undefined
- deep: 是否深度遍历
- flush: 指定回调函数的刷新时机
- onTrack/ onTrigger: 调用侦听器的依赖
- 示例

```js
// 侦听一个属性名
this.$watch("a", (nVal, oVal) => {});
// 侦听一个由`.`分隔的路径
this.$watch("a.b", (nVal, oVal) => {});
// 对更复杂的表达式使用getter函数
this.$watch(() => {
  this.a + this.b, (newVal, oldVal) => {};
});
// 停止该侦听器
const unWatch = this.$watch("a", cb);
// 之后...
unWatch();
```

### $emit()

在当前组件触发一个自定义事件。任何额外的参数都会传递给事件监听器的回调函数

- 示例

```js
export default {
  created() {
    // 仅触发事件
    this.$emit("foo");
    // 带有额外参数
    this.$emit("bar", 1, 2, 3);
  },
};
```

### $forceUpdate()

强制该组件重新渲染

- 详细信息

鉴于 Vue 全自动响应性系统，这个功能很少被用到，唯一使用的情况时使用高阶响应式 api 显式创建了一个响应式的组件状态

### $nextTick()

绑定在实例上的`nextTick()`函数

## 内置指令

### v-text

### v-html

### v-show

### v-if

### v-else

### v-else-if

### v-for

### v-on

给元素绑定事件监听器

- 缩写 `@`
- 期望绑定的值类型：`Function | Inline Statement | Object(不带参数)
- 参数： `event`(使用对象语法则为可选项)
- 修饰符
  - `.stop` - 调用 event.stopPropagetion()
  - `.prevent` - 调用 event.preventDefault()
  - `.capture` - 在捕获模式添加事件监听器
  - `.self` - 只有事件从元素本身触发才处理的函数
  - `.{keyAlias}` - 只有在按下某些键才出发处理函数
  - `.once` - 最多触发一次的函数
  - `.left` - 只有在鼠标左键数据触发处理函数
  - `.middle` - 只有在鼠标中键数据触发处理函数
  - `.right` - 只有在鼠标右键数据触发处理函数
  - `.passive` - 通过`{passive: true}` 附加一个 DON 事件

```template
<!-- 方法处理函数 -->
<button v-on:click="doSome"/>
<!-- 动态事件 -->
<button v-on:[event]="doSome"/>
<!-- 内联声明 -->
<button v-on:click="doThat('hello', $event)"/>
<!-- 缩写 -->
<button @click="doSome"/>
<!-- 缩写动态事件 -->
<button @[event]="doSome"/>
<!-- 停止传播 -->
<button @click.stop="doSome"/>
<!-- 阻止默认事件 -->
<button @click.prevent="doSome"/>
<!-- 不带表达式阻止默认事件 -->
<button @submit.prevent="doSome"/>
<!-- 链式调用修饰符 -->
<button @click.stop.prevent="doSome"/>
<!-- 点击事件最多触发一次 -->
<button @click.once="doSome"/>
<!-- 链式调用修饰符 -->
<button v-on={mousedown: doThis, mouseup: doSome}/>
<!-- 监听子组件的my-event 事件 -->
<MyComonent @my-event='handleThis(123, $event)'/>
```
## 内置组件
### `<Transition>`
为单个元素或组件提供动画过渡效果
### `<TransitionGroup>`
为列表中的多个元素或组件提供过渡效果
### `<keepAlive>`
缓存包裹在其中动态切换的组件
### `<Teleport>`
将其插槽内容渲染到Dom中的另一个位置
- 示例
```js
// 指定目标容器
<Teleport to="#some-id">
<Teleport to=".some-class">
<Teleport to="[data-teleport]">
// 有条件的禁用
<Teleport to="#popup" :disabled="displayVideoInline">
  <video src="./my-movie.mp4">
</Teleport>
```
### `<Suspense>`
- 事件
  - `@resolve`
  - `@pending`
  - `@fallback`
- 详细信息
  `<suspense>`接受两个插槽： `#default`和`#fallback`。它将在内存渲染默认插槽的同时展示后备插槽的内容。

  如在渲染时遇到异步依赖项（异步组件和具有`async setup()` 的组件，它将等到所有的依赖想解析完成时再显示默认插槽。
## 特殊元素
### `<component>`
一个用于渲染动态组件或元素的元组件
### `<slot>`
模板中插槽内容出口
### `<template>`
想要使用内置指令而不在dom中渲染元素时可用作占位符
## 特殊Attributes

### key
比较新旧节点列表时识别vnode
### ref
用于注册模板引用
### is
用于绑定动态组件