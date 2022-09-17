# element-UI 使用过程中遇到的问题 收集

## 💡 简介
用于收集 使用element-UI过程中遇到的问题及解决方法，以提高开发效率。



### ✨ Elemenet ui 使用
 最好 是 cdn引入或 全局引用， 按需引入会有各种问题


### ✨ 解决思路

####  思路1： 先 百度 查看有没有人之前遇到相似的问题


####  思路2： 查看 工具库 官网，对比例子代码，调试项目，找出项目 bug代码 所在





## ✨ 问题 及 解决

###  问题1： el-form 表单 中的 el-input输入框 不能输入、删除等
  
#### 原因1：el-form-item 中 只允许 el-button 的 @click 事件，不允许其他组件的 @click事件出现
  

   
```html
<el-form :model="user" ref="user" status-icon label-width="150px">
        <el-form-item label="用户名" prop="account" :rules="rules.account">
          <el-input  v-model='user.account' placeholder='请输入用户名，默认：gentle' autocomplete='off'></el-input >
        </el-form-item>
     
        </el-form-item>
        
        <!--
          <el-form-item>里面 只允许 el-button 的 @click 事件， 不允许其他组件的 @click事件出现

          <el-ink rel="nofollow" style="float:right" :underline="false" @click="forget">忘记密码</el-ink>
          <el-ink rel="nofollow" style="float:right" :underline="false" @click="toRegister">注册</el-ink>
        -->
        
        <el-form-item>
```
          



###  问题2： el-tree 树形控件 不能 渲染 出数据
  
#### 原因1：官网规定 el-tree 使用懒加载lazy属性时 需要配合 loading属性[tree树形控件](https://element.eleme.cn/#/zh-CN/component/tree#tree-shu-xing-kong-jian)

