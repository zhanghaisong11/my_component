#vue 手写下拉选择框
# 引用

```js
import SelectModel from 'SelectModel.vue'
```

```html
<select-model v-model="chooseContent" :options="month" :valueKey="'valueKey'" :labelKey="labelKey">
 </select-model>
```
当options 为复合数组的时候labelKey为必要值，

