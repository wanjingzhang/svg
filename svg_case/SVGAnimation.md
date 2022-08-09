# SVG SMIL animation
##### Synchronized Multimedia Integration Language(同步多媒体集成语言)，类似css(关键帧创建，物体移动，颜色变化等)，但是可以实现更多的功能。
##### SVG Path带有定义路径行政的数据d，它可以通过SMIL修改但是不能用css，因为SVG属性是被SVG来描述的，CSS动画可以通过JS或SMIL的SVG动画来填补。例：http://snapsvg.io/demos/#coffee svg的jquery。
##### SVG SMIL的优势是，当svg嵌入img或者作为背景图使用时，js动画不能工作，但SMIL在这两种情况都可以执行。
##### SVG 缺点，animation元素每次只能修改一个属性。


=================

一、五大元素
=================
-----------------
1. <set> 设置属性，begin实现延时功能  
`<set attributeName="x" attributeType="XML" to="60" begin="3s" />` eg. 3秒之后从X坐标160移动到60

-----------------
2. <animate> 实现动画的过渡效果 
`<animate attributeName="x" from="160" to="60" begin="0s" dur="3s" repeatCount="indefinite" />` eg. 同上，增加过度效果

----------------- 
3. <animateTransform> 变形动画，与CSS中的一样，translate, scale, rotate, skewX, skewY
translate: tx ty
scale: sx sy
rotate: cx cy from="0 150 150" from中表示动画的起始值。三个参数分表代表：旋转起始角度，旋转的起始X坐标，旋转的起始Y坐标。
skewX: skew-angle 
`<animateTransform attributeName="transform" begin="0s" dur="3s" type="scale" from="1" to="1.5" repeatCount="indefinite" />` eg. 快速长大的马的效果1到1.5倍的效果

-----------------
4. <animateMotion> 使元素沿着path路径运动 
`<animateMotion path="M10,80 q100,120 120,20 q140,-50 160,0" begin="0s" dur="3s" repeatCount="indefinite" rotate="auto"/>` eg. 使元素沿着path路径运动, rotate 自动贴合旋转角度

-----------------
5. 自由组合 多个动画元素的组合 
`<animate attributeName="x" from="160" to="60" begin="0s" dur="3s" repeatCount="indefinite" />`
`<animate attributeName="opacity" from="1" to="0" dur="3s" repeatCount="indefinite" />`


二、属性
=================
1. animationMotion：定义了一个元素如何沿着运动路径进行移动
    1. <animateMotion dur="10s" repeatCount="indefinite" path="M20,50 C20,-50 180,150 180,50 C180-50 20,150 20,50 z" />

    1. keyPoints: 关键点用于动画沿着运动路径的距离
       keyTimes: keyTimes应用于keyPoints中的值，而不是value
    <animate attributeName="cx" dur="2s"  repeatCount="indefinite"  values="60 ; 110 ; 60 ; 10 ; 60" keyTimes="0 ; 0.3 ; 0.6 ; 0.80 ; 1" /> eg. 定点定位

    1. rotate：auto 沿着路径的方向贴合 auto-reverse 对象按外圈旋转，角度按路径切相量贴合

2. animate 元素：
    1. attributeType：属性类型 CSS,XML,auto  animatable的CSS属性名
    1. attributeName：属性名字 y
    1. cx: 指定了元素x轴的中心点
    1. cy: 指定了元素y轴的中心点
    1. from: 属性值从 to: 到
    1. dur: 动画时间
    1. begin：开始时间 (a. click+5s 事件值  b. click+01:30 时钟值 c. circ-anim.begin+1s 动画事件开始后1s执行 d. circ-anim.end-1s 动画时间结束前1s)
    `<animate xlink:href="#orange-circle" attributeName="cx" from="50" to="450" dur="5s" begin="click" fill="freeze" d="circ-anim" />`
    1. end: 指定动画结束时间 例如：在一个动画开始时结束
    `<animate xlink:href="#orange-circle" attributeName="cx" from="50" to="450" dur="30s" begin="0s" end="circ-anim.begin" fill="freeze" id="oCircAnim"/>`
    1. fill: 指定动画结束时的状态 (freeze停在最后一帧, remove动画结束后被移除)
    1. by: 相当于css的steps()
    1. restart: 重启动画 (always随时 whenNotActive只能在尚未播放时启动 never永远不)
    1. repeatCount: 2 indefinite
    1. repeatDur: "01:30" 在一定时间后停止
    1. (keyTimes) & values: 指定关键帧和值，一一对应，用逗号分割，如果指定了这个，from to by 将会被覆盖
    `<animate  xlink:href="#orange-circle" attributeName="cx" from="50" to="450"  dur="2s" begin="click" values="50; 490; 350; 450" keyTimes="0; 0.5; 0.8; 1" fill="freeze"  id="circ-anim" />`
    1. easingMode: 控制动画速度 (a. linear 默认线性 b. descrete 离散，动画直接跳转中间没有过渡，类似steps() c. pace 和linear类似会忽略keyTimes的值 d. spline 如下)
    1. KeySpline: easingMode=spline 根据三次贝塞尔曲线，取KeySpline中的值,(x1,y1,x2,y2)


三、变形路径
=================
1. morphing paths，<path>中d指代的是data，开始、结束、中间的路径中的顶点在数量和顺序上要一致，如果顶点丢失 路径就不工作。
1. 路径变换动画，需指定attributeName="d"
`<animate attributeName="d" dur="6000ms" repeatCount="indefinite" values="M 68,0 52,15.899 0,47 12,212 47,251.793 120,207 110,325.793 78,328.793 57,296.793 10,318.793 23.239,408.725 43.143,421.587 173,390 188,353 179,151 177.959,96.168 122,87 111,149 82,146 84,88 177,113 181.843,28 117,0,0  Z;`


四、运动路径
1. 把path路径上的变换矩阵的数值　添加到当前对象的坐标的XY上，得出对象的平移动画。
1. `<animateMotion  dur="1s" begin="click" fill="freeze" path="M0,0c3.2-3.4,18.4-0.6,23.4-0.6c5.7,0.1,10.8,0.9,16.3,2.3    c13.5,3.5,26.1,9.6,38.5,16.2c12.3,6.5,21.3,16.8,31.9,25.4c10.8,8.7,21,18.3,31.7,26.9c9.3,7.4,20.9,11.5,31.4,16.7 c13.7,6.8,26.8,9.7,41.8,9c21.4-1,40.8-3.7,61.3-10.4c10.9-3.5,18.9-11.3,28.5-17.8c5.4-3.7,10.4-6.7,14.8-11.5 c1.9-2.1,3.7-5.5,6.5-6.5" />`












