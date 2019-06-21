##npm
####nrm 的使用

1. 下载 npm install nrm -g
   - 作用:管理 npm 的一个工具
2. nrm ls 查看 npm 源地址
3. nrm test 测试哪个源比较快
4. nrm use +名称可以切换 npm 源
5. npm set registry + 源地址
   npm 源 https://registry.npmjs.org/
   淘宝源 https://registry.npm.taobao.org/
6. 查看源 npm config get registry

#### npm 发布包

https://www.npmjs.com/ 注册账号

1. 发布包的时候必须切换到 npm 源 nrm use npm
2. 登录 npm 管理 npm login
   用户名:阮野 密码：yezui1314A
3. npm publish
