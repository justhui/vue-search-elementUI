### 搜素组件
1.功能介绍：
+ 1.1 支持单选
+ 1.2 支持多选
+ 1.3 内置基础输入框，@blur和enter触发事件
+ 1.4 提供作用域插槽，扩展其他功能
+ 1.5 首次渲染展示4个项，超出可以折叠（v-show控制）
+ 1.6 单行超出可以折叠，支持屏幕缩放响应式折叠

2.api 说明：
+ 2.1 options为接受参数,示例如下：
    label：string必传，为左侧title名，
    key: string必传，为传出时指定的字段
    type: string可选， 仅支持radio, 默认为checkbox多选
    slot: string可选，内置支持input(最基础的input),支持自定以slot名，支持作用域传参，slot-cope="{item,index}"
    checked：string||Array可选, 默认值，slot与单/多选结合使用时，可通过修改checked的值改变当前激活项
    children: Array可选，单/多选时必传，支持：children:['1'], children:[{lable:'1',value:"1"}]两种方式
  ```
  options: [
        {
          label: "多选",
          key: "name1",
          children: [
            {
              label: "张三",
              value: "1",
            }
          ],
        },
        {
          label: "单选",
          key: "name2",
          type: "radio",
          children: [ "张三","李四"],
        },
        {
          label: "输入框",
          key: "name3",
          slot: "input",
        },
        {
          label: "下拉",
          key: "name4",
          slot: "select",
        },
        {
          label: "插槽组合",
          key: "name5",
          children: [
            {
              label: "张三",
              value: "1",
            },
          ],
          slot: "select",
        },
      ],
  ```
  
+ 2.2 @onSearch 触发的方法,参数为Object, 以定义key的组成的key-value方式

+ 2.3 sliceNum, Number 可选，首次展示的最大行列数，超出才展示 "展示更多"
  
  ## Project setup
```
yarn install
```

### Compiles and hot-reloads for development
```
yarn serve
```

### Compiles and minifies for production
```
yarn build
```
