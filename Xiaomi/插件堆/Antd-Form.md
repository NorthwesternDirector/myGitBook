### From

#### 使用 antd3-From

##### 1. 基础 

```jsx
import {Form} from 'antd'

const MyTable = ({form})=>{
  ...
  <Form>
    <Form.Item></Form.Item>
  </Form>
  ...
}

export default From.create()(MyTable)//包裹后 组件会自带 this.props.form 熟悉
```

##### 2. 常用 form方法

```jsx
//校验()并获取一组输入域的值域Error
form.validateFields([fieldName],[option],(err,value)=>{...}) 
//和表单进行双向绑定
form.getFieldDecorator('key',{rules:[],initialValue:[]}) 
//设置表单值
form.setFieldsValues({key:value})
//获取表单数据 
from.getFieldsValue()、from.getFieldValue()                                                    
```

##### 3. wrappedComponentRef

> 经过 Form.create 之后，如果想要拿到 ref，可以使用 wrappedComponentRef（rc-form提供）

```jsx
//组件内部
const formRef = useRef(null)
...
formRef.current.setFieldsValue() //formRef.current 等同于2.中的 form
...
return (<Form wrappedComponentRef={formRef} />)
```

