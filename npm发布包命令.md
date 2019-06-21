##npm
#### nrm 的使用
1. 下载 npm install nrm -g
    - 作用管理npm
2. nrm ls 查看npm源地址
3. nrm test 测试哪个源比较快
4. nrm use +名称可以切换 npm 源
5. npm set registry + 源地址
    npm源   https://registry.npmjs.org/
    淘宝源   https://registry.npm.taobao.org/
6. npm config get registry 查看源

npm cache clear --force


#### npm 发布包
https://www.npmjs.com   注册账号

1. 发布包的时候必须切换到npm源  nrm use npm  
2. npm login 用户名: a316760006   密码: ************   邮箱:***********
3. 发布 npm publish