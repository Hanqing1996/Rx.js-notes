#### 像操作数组一样操作异步任务

#### Observable
```
Observable = Collections + Time
```

#### 两种设计模式
* 迭代器模式（Generator）
假设 A 是一个迭代器，那么 B 可以主动使用 A.next() 来要求 A 产生变化。（B主动要求A变化）
* 观察者模式（proxy）
假设 B 是一个观察者，在观察着 A，那么 A 一旦变化，A 就会主动通知 B。（A变化之后B被动接收通知）
```
var data={
    name:'libai'
}
// dataProxy 就是 data 的代理
var dataProxy=new Proxy(data,{
    get(target,key){
        return data[key]
    },
    set(target,key,value){
        data[key]=value
    }
})

console.log(dataProxy.name) // 'libai'
dataProxy.name='jack' // 更新 name
dataProxy.age=12 // 新增属性 age
console.log(dataProxy.age) // 12
```
#### 拖拽操作在 Rx.js 中可以用数组的思想进行操作
```
// 获取若干段拖拽操作，将它们放入一个数组
let getElemenetDrags = el =>
    el.mouseDowns // 每次 mounseDown 的后续动作会被放入 mouseDowns 数组中
        .map( mouseDown => 
            document.mouseMoves
                // takeUntil 表示以 mouseUp 为终点，截取该次 mounseDown 的后续动作
                // 即该次 mounseDown 引发的拖拽动作（注意不包括mounseDown,mounseUp）
                .takeUntil(document.mouseUps) 
        )
        .concatAll() // 将若干段拖拽操作放入一个数组
```
```
// 遍历每段拖拽操作，根据拖拽位置修改 div 位置
getElementDrags(div)
    .forEach(position => img.position = position )
```

#### 竞姿问题
* 请求响应的顺序问题
在一个搜索框中，如果用户输入 a，然后 300 毫秒后输入 b，那么你会发两个请求，一个请求查询 a 相关的热词，一个请求查询 ab 相关的热词，你能保证这两个请求响应的顺序吗？答案是不能。
* 解决方法
```
let authorizations = 
    player.init()
        .map(()=>
            playAttempts
                .map(movieId=>
                    player.authorize(movieId)
                        .retry(3)
                        .takeUntil(cancels)
                )
                .concatAll()
        )
        .concatAll()

authorizations.forEach(
    license => player.play(license),
    error => showError(error)
)
```
