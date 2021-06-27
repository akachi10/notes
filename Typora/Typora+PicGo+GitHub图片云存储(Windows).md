# Typora+PicGo+GitHub 图片云存储

让Typora 使用图片云存储。

**全程根据官方提示操作教程**

1. 安装nodejs

   安装NPM必须用-g

   访问 https://nodejs.org/en/ 下载稳定版本并安装

   加入环境变量

   "NODE_PATH":"D:\nodejs\node_global\node_modules"

   "PATH":"D:\nodejs\node_global"

2. 安装PicGo

   (也可以选择使用yarn安装PicGo-Core)

   通过官方文档

   https://picgo.github.io/PicGo-Doc/zh/guide/#%E5%BA%94%E7%94%A8%E6%A6%82%E8%BF%B0

   得知在https://github.com/Molunerfinn/PicGo/releases/tag/v2.2.2

   中下载稳定版本

   windows可以下载exe

3. 安装PicGo-Core

   ```bash
   #安装PicGo-Core
   npm install picgo -g
   #设置图库
   C:\Users\dell\Pictures\Screenshots>picgo set uploader
   ? Choose a(n) uploader github
   ? repo: akachi10/notes
   ? branch: master
   ? token: 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
   ? path: pic/
   ? customUrl: https://raw.githubusercontent.com/akachi10/notes/master
   #手动设置C:\Users\dell\.picgo\config.json
   中的uploader和current为github
   ```

4. 提交图片测试

   ```shell
   picgo u [fileName]
   ```

5. 安装后缀插件

   ```shell
   #安装
   picgo install super-prefix
   #在config.json中加入这段配置
     "picgo-plugin-super-prefix": {
       "fileFormat": "YYYYMMDDHHmmss"
     }
   ```

   
