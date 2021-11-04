官网地址：https://cn.vitejs.dev/

# 一、前言

Vite  是一种新型前端构建工具，能够显著提升前端开发体验。它主要由两部分组成：

- 一个开发服务器，基于原生ES模块提供了丰富的内建功能，如模块热更新（HMR）；
- 一套构建指令，使用 Rollup 打包你的代码，并且它是预配置的，可以输出用于生产环境的优化过的静态资源。

**其特点就是：**

- 启动快
- 更新快

**浏览器支持：**

1. 开发环境中：Vite 需要在支持 [原生 ES 模块动态导入](https://caniuse.com/es6-module-dynamic-import) 的浏览器中使用。
2. 生产环境中：默认支持的浏览器需要支持 [通过脚本标签来引入原生 ES 模块](https://caniuse.com/es6-module) 。可以通过官方插件 [@vitejs/plugin-legacy](https://github.com/vitejs/vite/tree/main/packages/plugin-legacy) 支持旧浏览器。

# 二、搭建项目

[参考这里 >>](https://cn.vitejs.dev/guide/#scaffolding-your-first-vite-project)

```shell
$ yarn create @vitejs/app
```

快速创建项目指令：

```shell
# vue + ts
$ yarn create @vitejs/app vue-ts-proj --template vue-ts
# react + ts
$ yarn create @vitejs/app react-ts-proj --template react-ts
```

# 三、Vite 配置项

Vite配置为放置在根目录中的 *vite.config.js* 文件。

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

> 提示：如果你使用 ts，那么你还需要在 **ts.config.json** 文件中添加如下配置：

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

**①. 在根目录新建  ”<u>.env.dev</u>“、”<u>.env.production</u>“、”<u>.env.test</u>“ 文件**

```ini
#　.env.dev --- 【开发环境】
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
#　.env.production --- 【生产环境】
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
#　.env.test --- 【测试环境】
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

**②. 修改 <u>package.json</u> 指令**

> Vue 配置示例：

```json
"dev": "vite --mode dev --host 0.0.0.0",
"test": "vue-tsc --noEmit && vite build --mode test --base=/二级目录/",
"build": "vue-tsc --noEmit && vite build --mode production",
```

> React 配置示例：

```
"dev": "vite --mode=dev --host=0.0.0.0",
"test": "tsc && vite build --mode=test",
"build": "tsc && vite build --mode=production",
```

**③. 配置环境变量智能提示**

> Vue 配置示例：

根目录新建 **env.d.ts** 文件，键入以下内容：

```tsx
interface ImportMetaEnv {
  VITE_APP_BASE: string;
  VITE_APP_HOST: string;
  VITE_APP_APPID_WEIXIN: string;
  VITE_APP_APPID_ALIPAY: string;
}
```

> React 配置示例：

在 **vite-env.d.ts** 文件中，键入以下内容：

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

直接安装 less/sass 即可使用，无需任何配置。

```shell
$ yarn add less -D
$ yarn add sass -D
```

## 2. 移动端适配方案

安装依赖：

```shell
$ yarn add postcss-pxtorem autoprefixer -D
```

在根目录下创建 **postcss.config.js**  文件：

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

在根目录src中新建utils目录下新建rem.ts等比适配文件

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

在 **mian.ts(x)**  引入

```ts
import "./utils/rem"
```

> 提示：
>
> - ts 提示 **无法在 "--isolatedModules" 下编译“rem.ts”，因为它被视为全局脚本文件。请添加导入、导出或空的 "export {}" 语句来使它成为模块**，此时你应该在 **tsconfig.json** 文件中将 **isolatedModules** 字段设置为 `false`。

## 3. 全局TS类型声明

在根目录创建 **typings/index.d.ts** 文件

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



#　五、Vue - 差异化配置

## 1. 配置路由

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

## 2. 状态管理

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

## 4. 二级目录部署

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

# 六、React - 差异化配置

## 1. 路由配置

**①. 说明**

本文以微信公众号/支付宝生活号开发路由配置举例。

**②. 目录结构**

这里只列举路由配置相关所包括的目录，仅供参考：

```ini
.
├── components
	├── Fallback # 懒加载loading提示 
	├── NotEnv   # 非微信/支付宝环境提示
	└── TabBar   # 标签栏
		├── index.tsx
		└── index.less
├── pages
	├── Auth
	├── IndexPage
	├── Integral
	├── Mine
	├── NotFound
	└── PrivilegeBrand
├── router
	├── index.tsx
	└── routes.ts
└── main.tsx
```

**③. 安装依赖**

```shell
$ yarn add react-router-dom react-router-guards
```

依赖包解读：

- **react-router-guards**：导航守卫；
- **react-router-dom**：Web路由核心；

**④. 文件代码**

> src/router/routes.ts

```typescript
import React from "react";
// 引入页面
import Auth from "@/pages/Auth";
import IndexPage from "@/pages/IndexPage";
import Integral from "@/pages/Integral";
import Mine from "@/pages/Mine";
import NotFound from "@/pages/NotFound";
import PrivilegeBrand from "@/pages/PrivilegeBrand";
// 引入路由类型
import { GuardedRouteProps } from "react-router-guards";

/**
 * - 如果是tab页或者授权页，为了避免跳转时闪屏，故而不采取【懒加载】路由模式；
 */

const routes: GuardedRouteProps[] = [
  {
    path: "/auth/:type",
    component: Auth,
    meta: {
      title: "授权",
    },
  },
  {
    path: "/index",
    component: IndexPage,
    meta: {
      title: "首页",
      isShowTabBar: true,
    },
  },
  {
    path: "/integral",
    component: Integral,
    meta: {
      title: "攒积分",
      isShowTabBar: true,
    },
  },
  {
    path: "/privilege-brand",
    component: PrivilegeBrand,
    meta: {
      title: "权益",
      isShowTabBar: true,
    },
  },
  {
    path: "/mine",
    component: Mine,
    meta: {
      title: "我的",
      isShowTabBar: true,
    },
  },
  // NotFount 组件需放置在路由表最后
  {
    path: "",
    component: NotFound,
    meta: {
      title: "NOT FOUND",
    },
  },
];

export default routes;
```

> src/router/index.tsx

```tsx
import React, { Suspense, useEffect, useState } from "react";
import { BrowserRouter as Router, Switch } from "react-router-dom";
import { GuardProvider, GuardedRoute } from "react-router-guards";
import {
  GuardFunctionRouteProps,
  GuardToRoute,
  Next,
} from "react-router-guards/dist/types";
import Fallback from "@/components/Fallback";
import Tools from "lg-tools";
import NotEnv from "@/components/NotEnv";
import TabBar from "@/components/TabBar";
import routes from "./routes";

/**
 * 环境判断
 * - 如果是微信/支付宝环境则渲染子组件
 * - 否则渲染提示组件（提示内容：请在微信或支付宝环境客户端打开链接）
 * - lg-tools：https://www.npmjs.com/package/lg-tools
 * @param param0
 * @returns
 */
export const GuardEnv: React.FC = ({ children }) => {
  return ["alipay", "weixin"].indexOf(Tools.getEnv()) === -1 ? (
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
      <Router>{children}</Router>
    </Suspense>
  );
};

/**
 * appGuard - 导航守卫
 * @param param0
 * @returns
 */
export const AppGuard: React.FC = ({ children }) => {
  const [showTabBar, setShowTabBar] = useState(false);
  // 守卫函数
  const guardFn = (
    to: GuardToRoute,
    from: GuardFunctionRouteProps | null,
    next: Next
  ) => {
    // 导航守卫 -- 可以做一些类似于修改标题/权限等事情
    // 设置标题
    document.title = to.meta.title;
    // 设置是否显示tabbar
    setShowTabBar(!!to.meta.isShowTabBar);
    next();
  };
  return (
    <>
      <GuardProvider guards={[guardFn]}>{children}</GuardProvider>
      {/* 根据路由表上的 “meta.isShowTabBar” 属性判断是否显示tabBar组件 */}
      {showTabBar ? <TabBar /> : null}
    </>
  );
};

/**
 * appRoutes - 渲染路由
 * @returns
 */
export const AppRoutes: React.FC = () => {
  return (
    <Switch>
      {routes.map((route, i) => {
        return (
          <GuardedRoute
            path={route.path}
            key={i}
            exact
            component={route.component}
            meta={route.meta}
          />
        );
      })}
    </Switch>
  );
};
```

> src/main.tsx

```tsx
import React from "react";
import ReactDOM from "react-dom";
import ErrorBoundary from "@/components/@lgs-react/ErrorBoundary";
import { AppRouter, AppRoutes, AppGuard, GuardEnv } from "./router";

import "@/utils/rem";
import "@/index.less";

ReactDOM.render(
  <React.StrictMode>
    <ErrorBoundary>
      <GuardEnv>
        <AppRouter>
          <AppGuard>
            <AppRoutes />
          </AppGuard>
        </AppRouter>
      </GuardEnv>
    </ErrorBoundary>
  </React.StrictMode>,
  document.getElementById("root")
);

```

## 2. [Antd-mobile >>](https://mobile.ant.design/zh)

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

## 3. 二级目录部署

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

# 七、模板地址

## 1. vue

- [公众号模板 >>](https://github.com/LiHongyao/vite-vue-template__MP)
- [H5模板 >>]()

## 2. React

- [公众号模板 >>](https://github.com/LiHongyao/vite-react-template__MP)

- [H5模板 >>]()

