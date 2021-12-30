# 契子

本文主要简单介绍一下 `Vite` 相关的知识点及使用，本文会不定期更新优化收集在使用 Vite 过程中遇到的一些问题和相关配置。

# 一、前言

[Vite 下一代前端开发与构建工具 >>](https://cn.vitejs.dev/)

Vite  是一种新型前端构建工具，能够显著提升前端开发体验。它主要由两部分组成：

- 一个开发服务器，基于原生 `ES模块` 提供了丰富的内建功能，如模块热更新（`HMR`）；
- 一套构建指令，使用 `Rollup` 打包你的代码，并且它是预配置的，可以输出用于生产环境的优化过的静态资源。

**其特点就是：**

- 极速的服务启动：使用原生 `ESM` 文件，无需打包!
- 轻量快速的热重载：无论应用程序大小如何，都始终极快的模块热重载（`HMR`）
- 丰富的功能：对 `TypeScript`、`JSX`、`CSS` 等支持开箱即用。
- 优化的构建：可选 “多页应用” 或 “库” 模式的预配置 `Rollup` 构建
- 通用的插件：在开发和构建之间共享 `Rollup-superset` 插件接口。
- 完全类型化的API：灵活的 API 和完整 TypeScript 类型。

**浏览器支持：**

1. 开发环境中：Vite 需要在支持 [原生 ES 模块动态导入](https://caniuse.com/es6-module-dynamic-import) 的浏览器中使用。
2. 生产环境中：默认支持的浏览器需要支持 [通过脚本标签来引入原生 ES 模块](https://caniuse.com/es6-module) 。可以通过官方插件 [@vitejs/plugin-legacy](https://github.com/vitejs/vite/tree/main/packages/plugin-legacy) 支持旧浏览器。

# 二、搭建项目

`node`：v16.13.1

`npm`：8.1.2

[参考这里 >>](https://cn.vitejs.dev/guide/#scaffolding-your-first-vite-project)

```shell
$ npm init vite@latest
```

快速创建项目指令：

```shell
$ npm init vite@latest <PROJECT-Name> -- --template vue-ts
```

# 三、Vite 配置项

配置文件：`vite.config.js`

## 1. 取别名

```js
import { defineConfig } from "vite";
import { resolve } from "path";

export default defineConfig({
   resolve: {
    alias: {
      "@": resolve(__dirname, "src"),
    },
  }
});
```

> 提示：如果你使用 ts，那么你还需要在 `ts.config.json` 文件中添加如下配置：

```json
{
  "compilerOptions": {
   "baseUrl": ".",
   "paths": {
     "@/*": ["src/*"]
   }
 },
```

## 2. 配置代理

```js
// 配置代理
server: {
  proxy: {
    "/api": {
      target: "http://jsonplaceholder.typicode.com",
      changeOrigin: true,
      rewrite: (path) => path.replace(/^\/api/, ""),
    },
  },
},
```

## 3. 环境变量

**①. 在根目录新建  `.env.dev`、`.env.production`、`.env.test`**

```ini
# 开发环境：.env.dev
NODE_ENV=development
VITE_APP_ENV=development

# 服务器地址
VITE_APP_HOST=开发环境服务器地址

# 基础路径
VITE_APP_BASE=""

# appID相关
VITE_APP_APPID_WEIXIN=微信appID
VITE_APP_APPID_ALIPAY=支付宝appID
```

```ini
# 生产环境：.env.production 
NODE_ENV=production
VITE_APP_ENV=production

# 服务器地址
VITE_APP_HOST=生产环境服务器地址

# 基础路径
VITE_APP_BASE=""

# appID相关
VITE_APP_APPID_WEIXIN=微信appID
VITE_APP_APPID_ALIPAY=支付宝appID
```

```ini
#　测试环境：.env.test
NODE_ENV=production
VITE_APP_ENV=production

# 服务器地址
VITE_APP_HOST=测试环境服务器地址

# 基础路径
VITE_APP_BASE=""

# appID相关
VITE_APP_APPID_WEIXIN=微信appID
VITE_APP_APPID_ALIPAY=支付宝appID
```

> Tips：
>
> 1）环境变量名必须以 `VITE_APP` 开头；
>
> 2）配置文件的内容请根据项目需要设置；

**②. 修改 `package.json` 指令**

> **@Vue **

```json
"dev": "vite --mode dev --host 0.0.0.0",
"test": "vue-tsc --noEmit && vite build --mode test --base=/二级目录/",
"build": "vue-tsc --noEmit && vite build --mode production",
```

> **@React**

```
"dev": "vite --mode=dev --host=0.0.0.0",
"test": "tsc && vite build --mode=test",
"build": "tsc && vite build --mode=production",
```

**③. 配置环境变量智能提示**

> **@Vue**

根目录新建 `env.d.ts` 文件，键入以下内容：

```tsx
interface ImportMetaEnv {
  VITE_APP_BASE: string;
  VITE_APP_HOST: string;
  VITE_APP_APPID_WEIXIN: string;
  VITE_APP_APPID_ALIPAY: string;
}
```

> **@React**

在 `vite-env.d.ts` 文件中，键入以下内容：

```tsx
/// <reference types="vite/client" />

interface ImportMetaEnv {
  VITE_APP_BASE: string;
  VITE_APP_HOST: string;
  VITE_APP_APPID_WEIXIN: string;
  VITE_APP_APPID_ALIPAY: string;
}
```

**④. 使用环境变量**

```tsx
import.meta.env.VITE_APP_HOST
```

# 四、项目配置

## 1. 预处理样式

直接安装 `less/sass` 即可使用，无需任何配置。

```shell
$ yarn add less -D
$ yarn add sass -D
```

## 2. 移动端适配方案

安装依赖：

```shell
$ yarn add postcss-pxtorem autoprefixer -D
```

在根目录下创建 `postcss.config.js`  文件：

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

新建 `src/utils/rem.ts` 文件：

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
  setRem();
};
```

在 `mian.ts(x)`  引入

```ts
import "./utils/rem"
```

> 提示：如果提示 “无法在 "`--isolatedModules`" 下编译“`rem.ts`”，因为它被视为全局脚本文件。请添加导入、导出或空的 "`export {}`" 语句来使它成为模块 ”此时你应该在 `tsconfig.json` 文件中将 `isolatedModules` 字段设置为 `false`。
>

## 3. 全局TS类型声明

在根目录创建 `typings/index.d.ts` 文件

```ts
export {};

// => 全局类型声明
declare global {
  interface Window {
    _hmt: any;
    wx: any;
    AlipayJSBridge: any;
  }
  namespace GD {
    interface BaseResponse<T = any> {
      code: number;
      data: T;
      msg: string;
      page: {
        pageNo: number;
        pageSize: number;
        pages: number;
        total: number;
      };
    }
  }
}
```

> 提示：你需要在 <u>**tsconfig.json**</u> 文件里面的 **include** 字段中加入 `[**/*.d.ts]`。

然后就可以在全局访问了，如：

```typescript
const response = GD.BaseResponse<{name: string}>
```

## 4. mock.js

**①. 安装依赖：**

```shell
$ yarn add mockjs -S
$ yarn add vite-plugin-mock -D
```

**②. 引入插件 <u>vite.config.js</u>：**

```js
import { viteMockServe } from 'vite-plugin-mock';
export default defineConfig({
  plugins: [
    viteMockServe({})
  ],
});
```

**③. 新建 <u>mock/test.ts</u> 文件**

```js
export default [
  {
    url: "/api-dev/users",
    method: "get",
    response: (req) => {
      return {
        code: 0,
        data: {
          name: "Li-HONGYAO",
          phone: "173 **** 8669",
          address: "成都市高新区雅和南四路216号",
        },
        msg: "success",
      };
    },
  },
];
```

**④. 模拟请求**

```
fetch("/api-dev/users")
  .then((res) => res.json())
  .then((users) => {
    console.log(users);
  });
```

## 5. axios

**①. 安装相关依赖：**

```shell
$ yarn add axios lg-tools lg-cookie
```

> [lg-cookie >>](https://www.npmjs.com/package/lg-cookie) ：我封装的一个关于操作Cookie的库。
>
> [lg-tools >>](https://www.npmjs.com/package/lg-tools)：我封装的一个关于工具函数的库。

**②.  创建 <u>/src/utils/request.ts</u> 文件**

```ts
import axios, { AxiosRequestConfig, AxiosResponse } from "axios";
import Cookie from "lg-cookie";
import Tools from "lg-tools";

const service = axios.create({
  baseURL: import.meta.env.VITE_APP_HOST,
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
    const token = Cookie.get<string>("AUTHORIZATION_TOKEN");
    config.headers = {
      "Content-Type": "application/json",
      Authorization: Cookie.get<string>("AUTHORIZATION_TOKEN"),
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
    const { code, msg } = response.data;
    switch (code) {
      case 0:
        return response.data;
      default:
        console.log(msg);
        return response.data;
    }
  },
  (error: any) => {
    console.log(error);
    /timeout/.test(error.message) && console.log("请求超时，请检查网络");
    return Promise.reject(error);
  }
);

export default service;
```

**③. 创建 <u>/src/api/index.ts</u> && <u>/src/api/test.ts</u> 文件**

```typescript
// 1. src/api/test.ts
import request from "../utils/request";

/**
 * 获取用户信息
 * @returns
 */
export function users<T>() {
  return request.get<T>("/api-dev/users");
}
```

```typescript
// 2. src/api/index.ts
import * as test from "./test";

export default {
  test,
};
```

**④. 在全局TS类型声明文件中添加 axios 相关类型配置**

```tsx
// => axios 模块定义
import { AxiosRequestConfig } from "axios";

declare module 'axios' {
  export interface AxiosInstance {
    <T = any>(config: AxiosRequestConfig): Promise<T>;
    request<T = any> (config: AxiosRequestConfig): Promise<T>;
    get<T = any>(url: string, config?: AxiosRequestConfig): Promise<T>;
    delete<T = any>(url: string, config?: AxiosRequestConfig): Promise<T>;
    head<T = any>(url: string, config?: AxiosRequestConfig): Promise<T>;
    post<T = any>(url: string, data?: any, config?: AxiosRequestConfig): Promise<T>;
    put<T = any>(url: string, data?: any, config?: AxiosRequestConfig): Promise<T>;
    patch<T = any>(url: string, data?: any, config?: AxiosRequestConfig): Promise<T>;
  }
}
```

**⑤. 调用接口请求数据**

```tsx
// => 引入api
import api from "./api";

// => 响应数据类型声明
interface UserProps {
  name: string;
  phone: string;
  address: string;
}

// => 接口调用
api.test.users<GD.BaseResponse<UserProps>>().then(res => {
  if(res && res.code === 0) {
    console.log(res.data.name);
  }
})
```

## 6. 动态加载静态资源

在使用 webpack 动态引入静态资源可以使用 `require` 形式，但是在vite中不可取，会抛出 <u>*require is not defined*</u> 的错误。

不过我们可以通过 [import.meta.url](https://cn.vitejs.dev/guide/assets.html#new-url-url-import-meta-url) 的形式引入，它是一个 ESM 的原生功能，会暴露当前模块的 URL。将它与原生的 [URL 构造器](https://developer.mozilla.org/en-US/docs/Web/API/URL) 组合使用，在一个 JavaScript 模块中，通过相对路径我们就能得到一个被完整解析的静态资源 URL，其语法形式如下：

```js
new URL(url, import.meta.url)
```

使用示例：

```js
const imgs = [
  { imgUrl: new URL('./assets/logo_1.png', import.meta.url), text: "标题1" },
  { imgUrl: new URL('./assets/logo_2.png', import.meta.url), text: "标题2" },
];
```

```react
// => react
{imgs.map((item) => (
  <img src={item.imgUrl.toString()} key={item.title} />
))}
```

值得注意的是，在生产环境中会抛出 “*URL is not defined xxx*”  的错误，这个时我们需要使用一个插件：[rollup-plugin-import-meta-url-to-module](https://www.npmjs.com/package/rollup-plugin-import-meta-url-to-module)。

使用方式比较简单，首先安装依赖：

```shell
$ yarn add rollup-plugin-import-meta-url-to-module
```

然后再 “*vit.config.js*” 中配置plugins：

```js
import urlToModule from 'rollup-plugin-import-meta-url-to-module';

export default {
  plugins: [
    urlToModule()
  ]
};
```

#　五、差异化配置

## 1. vue

### - 路由配置

安装依赖：

```shell
$ yarn add vue-router@next
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

### - 状态管理

安装依赖：

```shell
$ yarn add vuex@next
```

配置：在src下创建store目录，并在store下创建index.ts

```ts
import { InjectionKey } from "vue";
import { createStore, ActionTree, GetterTree, MutationTree, Store } from "vuex";

// 1. 声明Store类型
declare interface StoreProps {
  authUrlForIos?: string;
}
// 2. 定义注入类型
export const globalStoreKey: InjectionKey<Store<StoreProps>> = Symbol();

// 3. ---- state
const state: StoreProps = {
  authUrlForIos: "",
};
// 3. ---- getters
const getters: GetterTree<StoreProps, any> = {
  authUrlForIos: (state) => state.authUrlForIos,
};
// 3. ---- mutations
const mutations: MutationTree<StoreProps> = {
  updateAuthUrlForIos(state, payload) {
    state.authUrlForIos = payload;
  },
};
// 3. ---- actions
const actions: ActionTree<StoreProps, any> = {
  updateAuthUrlForIos({ commit }, payload) {
    commit("updateAuthUrlForIos", payload);
  },
};

// 4. 创建导出
export default createStore<StoreProps>({
  state,
  getters,
  mutations,
  actions,
});

```

挂载：在main.ts挂载数据中心

```ts
import App from "./App.vue";
import router from "./router";
import store, { globalStoreKey } from "./store";

// App配置/挂载相关
// 1. 创建App
const app = createApp(App);
// 2. 注入
app.use(router).use(store, globalStoreKey).use(vant);
// 3. 挂载
app.mount("#app");
```

### - Vant3

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

### - 二级目录部署

**方案1：**

打包时设置 `-base` 选项：

```js
"build": "vue-tsc --noEmit && vite build --mode production --base=/二级目录名/",
```

然后再 `router/index.ts` 中配置如下：

```js
const router = createRouter({
  // 部署二级目录：createWebHistory(base?: string)
  history: createWebHistory("/二级目录名/"),
  routes,
});
```

**方案2：**

在 `vite.config.js` 配置项中添加 `base` 配置，如：

```js
base:"/YOUR_PROJECT_NAME/"
```

然后再 `router/index.ts` 中配置如下：

```js
const router = createRouter({
  // 部署二级目录：createWebHistory(base?: string)
  history: createWebHistory("/二级目录名/"),
  routes,
});
```

## 2. React

### - 路由配置

**①. 目录结构**

```ini
.
├── components
│   ├── Fallback # 懒加载loading提示 
│   ├── NotEnv   # 非微信/支付宝环境提示
│	  └── TabBar   # 标签栏
│   │   ├── index.tsx
│   │   └── index.less
├── layouts
│	  └── index.tsx
├── pages
│   ├── Auth
│   ├── IndexPage
│   ├── Integral
│   ├── Mine
│   ├── NotFound
│	  └── PrivilegeBrand
├── router
│   ├── index.tsx
│	  └── routes.ts
└── main.tsx
```

> 提示：这里只例举部分目录，仅供参考。

**②. 安装依赖**

```shell
$ yarn add react-router-dom
```

> 提示：目前我使用的版本是 `^6.0.2`，关于 v6 版本的路由使用请 [参考这里 >>](https://reactrouter.com/)

**③. 文件代码**

> `src/layout/index.tsx`

```tsx
import TabBar from '@/components/@lgs-react/TabBar';
import React from 'react';
import { Outlet } from 'react-router-dom';

const Layout: React.FC = () => {
  return (
    <>
      {/* 视图容器，类似于vue中的 router-view */}
      <Outlet />
      {/* 标签栏 */}
      <TabBar />
    </>
  );
};

export default Layout;
```

> `src/App.tsx`

```tsx
import React, { Suspense } from 'react';
import { BrowserRouter as Router, Route, Routes } from 'react-router-dom';

import Fallback from '@/components/@lgs-react/Fallback';
import Layout from '@/layout';
import IndexPage from '@/pages/IndexPage';
import Integral from '@/pages/Integral';
import Mine from '@/pages/Mine';
import PrivilegeBrand from '@/pages/PrivilegeBrand';
import Auth from '@/pages/Auth';
import NotFound from './pages/NotFound';
import NotEnv from './components/@lgs-react/NotEnv';

import Tools from 'lg-tools';

const Details = React.lazy(() => import('@/pages/Details'));
const Download = React.lazy(() => import('@/pages/Download'));
const Test = React.lazy(() => import('@/pages/Test'));

/**
 * 环境判断
 * 如果 VITE_APP_SOURCE === 'mp'，即表示公众号/生活号
 * 那么在浏览器环境将提示 “请在微信或支付宝客户端打开链接”
 * @param param0
 * @returns
 */
export const GuardEnv: React.FC = ({ children }) => {
  return import.meta.env.VITE_APP_SOURCE === 'mp' &&
    ['weixin', 'alipay'].indexOf(Tools.getEnv()) === -1 ? (
    <NotEnv />
  ) : (
    <>{children}</>
  );
};

/**
 * appRouter
 * @returns
 */
export const AppRouter: React.FC = ({ children }) => {
  return (
    <Suspense fallback={Fallback}>
      <Router basename={import.meta.env.VITE_APP_BASE}>{children}</Router>
    </Suspense>
  );
};

/**
 * appRoutes - 渲染路由
 * @returns
 */
export const AppRoutes: React.FC = () => {
  return (
    <Routes>
      <Route path='/' element={<Layout />}>
        <Route index element={<IndexPage />} />
        <Route path='privilege-brand' element={<PrivilegeBrand />} />
        <Route path='integral' element={<Integral />} />
        <Route path='mine' element={<Mine />} />
      </Route>
      <Route path='/details' element={<Details />} />
      <Route path='/download' element={<Download />} />
      <Route path='/test' element={<Test />} />
      <Route path='/auth/:type' element={<Auth />} />
      <Route path='*' element={<NotFound />} />
    </Routes>
  );
};
```

> `src/main.tsx`

```tsx
import React from 'react';
import ReactDOM from 'react-dom';
import ErrorBoundary from '@/components/@lgs-react/ErrorBoundary';

import '@/utils/rem';
import '@/index.css';
import { AppRouter, AppRoutes, GuardEnv } from './App';
import Schemes from 'lg-schemes';

// 1. 开发环境 & 测试环境 启用vconsole --- Tips：目前启用vconsole打包会出现异常
import vconsole from 'vconsole';
if (import.meta.env.VITE_APP_ENV !== 'pro') {
  new vconsole();
}

// 2. 渲染视图
ReactDOM.render(
  <React.StrictMode>
    <ErrorBoundary>
      <GuardEnv>
        <AppRouter>
          <AppRoutes />
        </AppRouter>
      </GuardEnv>
    </ErrorBoundary>
  </React.StrictMode>,
  document.getElementById('root')
);
```

### - [Antd-mobile](https://mobile.ant.design/zh)

目前使用的  <u>Antd-mobile v5（白杨）</u> 版本。

**① 安装依赖**

```shell
$ yarn add antd-mobile@next
```

> 在 beta 阶段我们有可能会在后续的新版本中发布 break change，所以强烈建议使用 lockfile 锁定版本或手动锁定版本

**② 引入**

直接引入组件即可，antd-mobile 会自动为你加载 css 样式文件：

```tsx
import { Button } from 'antd-mobile'
```

### - 二级目录部署

**①. 在 `package.json` 文件中配置指令时时设置 `--base=/二级目录名/`**。

**②. 在各环境配置文件中根据需要设置 `VITE_APP_BASE` 字段值**。

**②. 在配置路由时设置 `basename` 属性，如下所示：**

```tsx
/**
 * src/router/index.tsx
 * appRouter
 * @returns
 */
export const AppRouter: React.FC = ({ children }) => {
  return (
    <Suspense fallback={Fallback}>
      <Router basename={import.meta.env.VITE_APP_BASE}>{children}</Router>
    </Suspense>
  );
};
```

# 六、模板地址

闲暇之余，结合 `Vite` 封装了两个模板，大家可以参考：

- [Vite2 + Vue3 + TypeScript + Vant Mobile >>](https://github.com/LiHongyao/vite-vue-template)

- [Vite2 + React +_TypeScript + Ant Design Mobile 5.x >>](https://github.com/LiHongyao/vite-react-template)

> 提示：本文中的代码片段均摘自上述模板文件。
