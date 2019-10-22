# 十三、打包发布

## 构建打包

```bash
npm run build
```

VueCLI 会把打包结果存储到项目的 `dist` 目录中。

## 测试打包结果

将 dist 放到一个 Web 服务器中运行测试。

- Ngxin
- Apache
- tomcat
- IIS
- 。。。。
- Node.js



这里推荐使用 Vue 官方推荐的一个命令行 http 服务工具：[serve](https://github.com/zeit/serve)。

安装：

```bash
# yarn global add serve
npm install -g serve
```

使用：

```bash
# dist 是运行 Web 服务根目录
serve -s dist
```

> serve 默认占用 5000 端口并启动一个服务

然后在浏览器中访问给出的地址访问测试。

## 部署

- 公司有专门的 devops，说白了就是运维
  - 有些公司没有专门的运维人员，那就后端负责
- 你只需要把打包结果给人家就可以了


