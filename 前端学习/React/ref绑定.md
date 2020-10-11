

### ref

> ref：reference（引用）的缩写

1. 使用方法——在数据流之外直接操作子孙组件：
   * ref属性赋值给Html元素，那么 `this.ref.current` 就获取到html元素
   * ref属性赋值给常规React组件，那么 `this.ref.current` 就获取到组件的实例 ✅

2. 注意⚠️：不要过度使用，有需要直接触达和操作子孙组件实例或者操作子dom元素的情况，可以使用React Ref；如果属性下传能够解决问题，应使用声明式的属性传递，而非命令式的ref。

3. 使用场景：
   * 用来处理非受控组件的焦点
   * 用来与第三方库对接，第三方库需要获取dom或者节点时

##### ref的3种绑定方式

1. 引用传递（Ref forwarding）是一种通过组件向子组件自动传递 **引用ref** 的技术，

   >  在父组件中通过创建ref【 const ref = React.createRef() 】并向内传递参数至子组件，来抓取子组件中的DOM节点；子组件需使用 `React.forwardRef` （React.forwardRef((props,ref)=><Compnent/>)）包裹，接收的第二个ref参数需进行子组件内对应DOM的绑定



当getFieldDecorator内包含的子组件是一封装组件，该组件需要使用react.forward包裹