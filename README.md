官网地址：https://cn.vitejs.dev/

# 搭建项目

```shell
$ yarn create @vitejs/app
```

快速创建项目指令：

```shell
$ yarn create @vitejs/app vue-ts-proj --template vue-ts
```

# Vite 配置项



#　移动端项目配置

## 1. 配置路由

安装依赖：

```shell
$ yarn add vue-router@4
```

在src下新建router目录，新建index.ts文件

```ts
import { createRouter, createWebHistory, RouteRecordRaw } from "vue-router";

const routes: Array<RouteRecordRaw> = [
  {
    path: "/",
    redirect: "/login",
  },
  {
    path: "/login",
    name: "login",
    component: () => import("pages/Login/Login.vue"),
    meta: {
      title: "登录",
    },
  },
  {
    path: "/index-page",
    name: "indexPage",
    component: () => import("pages/IndexPage/IndexPage.vue"),
    meta: {
      title: "首页",
    },
  },
];

const router = createRouter({
  history: createWebHistory(),
  routes,
});

// 导航守卫
router.beforeEach((to, from, next) => {
  if (to.path !== "/favicon.icon") {
    document.title = to.meta.title ? (to.meta.title as string) : "";
    next();
  }
});

export default router;
```

在main.ts挂载路由

```ts
import App from "./App.vue";
import router from "./router";

// App配置/挂载相关
// 1. 创建App
const app = createApp(App);
// 2. 注入
app.use(router);
// 3. 挂载
app.mount("#app");
```

## 2. 状态管理

安装依赖：

```shell
$ yarn add vuex@next
```

配置：在src下创建store目录，并在store下创建index.ts

```ts
import { createStore } from 'vuex'

export default createStore({
  state: {},
  mutations:{},
  actions: {},
  modules: {}
})
```

挂载：在main.ts挂载数据中心

```ts
import App from "./App.vue";
import router from "./router";
import store from './store';

// App配置/挂载相关
// 1. 创建App
const app = createApp(App);
// 2. 注入
app.use(router).use(store).use(vant);
// 3. 挂载
app.mount("#app");
```

## 3. Vant3

安装依赖：

```shell
$ yarn add vant@next
```

> 提示：vite版本不需要配置组件的按需加载，因为Vant 3.0 内部所有模块都是基于 ESM 编写的，天然具备按需引入的能力，但是样式必须全部引入137.2k

在main.ts全局引入样式：

```ts
import App from "./App.vue";
import router from "./router";
import store from './store';
import vant from 'vant';
import "vant/lib/index.css";   

// App配置/挂载相关
// 1. 创建App
const app = createApp(App);
// 2. 注入
app.use(router).use(store).use(vant);
// 3. 挂载
app.mount("#app");
```

## 4. 移动端适配

安装postcss-pxtorem：

```shell
$ yarn add postcss-pxtorem autoprefixer -D
```

在根目录下创建postcss.config.js

```js
module.exports = {
  plugins: {
    autoprefixer: {
      overrideBrowserslist: [
        "Android 4.1",
        "iOS 7.1",
        "Chrome > 31",
        "ff > 31",
        "ie >= 8",
      ],
    },
    "postcss-pxtorem": {
      rootValue: 37.5, // Vant 官方根字体大小是 37.5
      propList: ["*"],
      selectorBlackList: [".norem"], // 过滤掉.norem-开头的class，不进行rem转换
    },
  },
};
```

在根目录src中新建util目录下新建rem.ts等比适配文件

```ts
// rem等比适配配置文件
// 基准大小，注意此值要与 postcss.config.js 文件中的 rootValue保持一致
const baseSize = 37.5;
// 设置 rem 函数
function setRem() {
  // 当前页面宽度相对于375宽的缩放比例，可根据自己需要修改,一般设计稿都是宽750(图方便可以拿到设计图后改过来)。
  const scale = document.documentElement.clientWidth / 375; // 设置页面根节点字体大小（“Math.min(scale, 2)” 指最高放大比例为2，可根据实际业务需求调整）
  document.documentElement.style.fontSize = baseSize * Math.min(scale, 2) + "px";
}
// 初始化
setRem();
// 改变窗口大小时重新设置 rem
window.onresize = function () {
  console.log("我执行了");
  setRem();
};
```

在mian.ts引入

```ts
import "./utils/rem"
```

##　4. 环境变量

在根目录新建 ”.env.dev“、”.env.production“、”.env.test“文件

```ini
#　.env.dev
NODE_ENV=development
VITE_APP_ENV=development
VITE_APP_HOST=开发环境服务器地址
```

```ini
#　.env.production
NODE_ENV=production
VITE_APP_ENV=production
VITE_APP_HOST=生产环境服务器地址
```

```ini
#　.env.test
NODE_ENV=production
VITE_APP_ENV=production
VITE_APP_HOST=测试环境服务器地址
```

##　5. 全局ts类型声明

在根目录创建typings/index.d.ts文件

```ts
export {};
declare global {
  interface Window {
    _hmt: any;
    wx: any;
    AMap: any;
  }
  namespace LovePets {
    interface BaseResponse<T = any> {
      status: number;
      data: T;
      msg: string;
      page: {
        pageNo: number;
        pageSize: number;
        pages: number;
        total: number;
      };
      success: boolean;
    }
  }
}
```

## 6. axios

安装相关依赖：

```shell
$ yarn add axios lg-tools lg-cookie
```

在src创建utils文件夹,并在utils下创建request.ts

```ts
import axios, { AxiosRequestConfig, AxiosResponse } from "axios";
import Cookie from "lg-cookie";
import Tools from "lg-tools";
import { Toast } from "vant";

const service = axios.create({
  baseURL: import.meta.env.VITE_APP_HOST as string,
  timeout: 20000,
});

// 请求拦截
service.interceptors.request.use(
  (config: AxiosRequestConfig) => {
    // => 如果是GET请求追加时间戳
    if (config.method && /get/i.test(config.method)) {
      config.params = {
        ...config.params,
        timeState: Tools.randomCharacters(1) + Date.now(),
      };
    }
    // => 配置请求头
    const token = Cookie.get<string>("LOVEPETS_TOKEN");
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    config.headers = {
      "Content-Type": "application/json",
    };
    return config;
  },
  (error) => {
    return Promise.reject(error);
  }
);

// 响应拦截
service.interceptors.response.use(
  (response: AxiosResponse) => {
    // 清除toast
    const { status, msg } = response.data;
    switch (status) {
      case 200:
        return response.data;
      default:
        Toast.fail(msg);
        return response.data;
    }
  },
  (error: any) => {
    console.log(error);
    /timeout/.test(error.message) && Toast("请求超时，请检查网络");
    return Promise.reject(error);
  }
);

export default service;
```

