# 购物车组件---------- 使用流程

## 1.在  package.json  文件中  "devDependencies":{  } 中加入:

```
 "jquery": "^3.6.0",
```

## 2.在终端里输入:

```
npm install jquery --save-dev 
```

当然用 淘宝镜像 cnpm 也是可以的

## 3.在 vue.config.js 文件中添加:

```
  var webpack=require('webpack');
```

 在module.exports里输入：

```
configureWebpack:{
    plugins: [ 
      new webpack.ProvidePlugin({ 
            $:"jquery", 
            jQuery:"jquery", 
           "windows.jQuery":"jquery"
      }) 
    ], 
  }
```

## 4.在终端中输入：

```
npm run dev
```

