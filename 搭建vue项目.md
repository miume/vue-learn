## vue2全家桶

##来源<https://www.jianshu.com/p/32beaca25c0d>

Vue-cli（构建工具）、vue-router（路由）、vuex（状态管理器）、vue-resource（接口）

- ### 安装web pack

  ```
  npm install webpack -g  //全局安装
  webpack -v              //查看版本号
  ```

  

- ### 安装vue-cl

  ```
  npm install --global vue-cli  //全局安装
  vue-cli -V                    //查看版本号
  ```

  

- ### 用vue-cli来构建项目

  - 在电脑任意位置新建一个文件夹来作为项目存放地，然后使用命令行进入到项目目录输入

    ```
    vue init webpack projectname
    ```

  - 会在当前目录生成一个以该名称命名的项目文件夹，并且会跳出几个选项让你回答（一般选取默认选项或者输入yes之类的）

  - 配置完成  cd 进入这个项目

    ```
    npm install   //安装依赖包
    //安装完成后，目录会多出一个node_modules文件夹，这里放的所有依赖包
    ```

    

  - ![img](https://upload-images.jianshu.io/upload_images/10868449-01a038fa573b22c8.png?imageMogr2/auto-orient/)

    

    

- ### 启动项目

  ```
  npm run dev
  ```

  

- ### 打包上线

  ```
  npm run build
  ```

  

  - build      —构建脚本目录
  - config    — 构建配置目录
  - node-modules      — 依赖的node工具包目录
  - src                         — 源码目录
    - assets             — 资源目录
    - components   —组件目录
    - router
      - App.vue   — 页面级Vue组件
      - main.js     — 页面入口js文件
  - static                     — 静态文件目录
  - test                        — 测试文件目录
  - Index.html             —入口页面
  - package.json        — 项目描述文件
  - .eslintrc.js              — ES语法检查配置

  