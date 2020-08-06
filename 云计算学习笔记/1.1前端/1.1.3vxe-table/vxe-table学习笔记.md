# element-ui-admin与vxe-table集成开发总结-安装篇


## 安装篇
安装命令

```cmd	
	npm install xe-utils vxe-table vxe-table-plugin-element element-ui
```
下载比较缓慢，最好设置成 
```cmd	
	npm config set registry https://registry.npm.taobao.org
```
通过如下命令可以查看是否配置成功
```cmd
npm config get registry
npm info express
```


加载模块（这里面有坑，文档对初学者不友好，对于不擅长前端开发的我来说，耗费了好多精力，正确的配置方式应该是）

在 main.js 里面增加
```javascript

import 'font-awesome/scss/font-awesome.scss'
import 'xe-utils'
import VXETable from 'vxe-table'
import 'vxe-table/lib/index.css'
import VXETablePluginElement from 'vxe-table-plugin-element'
import 'vxe-table-plugin-element/dist/style.css'

```
font-awesome.scss 这个要装否则很多图标无法显示，安装方法

```cmd
npm install --save @fortawesome/fontawesome-svg-core
npm install --save @fortawesome/vue-fontawesome
npm install --save @fortawesome/free-solid-svg-icons
```

## 应用篇




