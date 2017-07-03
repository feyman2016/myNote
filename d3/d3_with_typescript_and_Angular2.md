## How to integrate d3 with angular2 and typescript
​	本文主要介绍如何将可视化库d3.js与基于typescript的angular2框架进行整合，重点探讨如何在开发过程中以一种最贴近best practice的方法来实现可视化模块的组件化以及高可复用性。

#### 1. 背景介绍

- typescript

  typescript是微软推出的一门新语言，是javascript的超集，开发人员开发的ts代码可以编译成js代码被执行，且可以与现有的js代码无缝结合，并且适用于任何浏览器，因此，理论上讲，js可以实现的语言特性，ts都可以实现。同时，ts语言是强类型的，支持interface，class，继承，借口组合，交集，类型推断等特性，可以使得开发者以典型的面向对象方式来组织代码，又可以在编译期就规避很多错误。在设计决策上，ts采用了“最贴近js需求的刚好不太重的实现方式”。


-   angular 

    谈到前端框架，就不得不得不提angular，该项目是google推出的基于DOM(*Document Object Model*)的前端框架，也就是说angular是以HTML为中心的，angular主要提供了一种前端代码模块化的组织方式，最终html页面中呈现的元素可以很好的映射到angular中定义的模块或者组件。angular1.X 是基于javascript的，不过angular2.X开始全面拥抱typescript，为typescript也提升了不少热度，目前angular已经出到4.X版本。

- d3

    d3是Data-Driven Documents的缩写，顾名思义，d3是数据驱动的，d3.js基于javascript，可以将数据集合以多种方式可视化呈现。简单的说，d3以HTML中中的<svg></svg> 或者<canvas></canvas>作为画布，以d3提供的点，线，基本多边形（如方形，圆形等）来做图，而数据集合最终以这种方式被d3在html渲染为不同的图，d3支持的图包括但不限于柱状图，力学图，树形图等。由于d3提供的元素比较基本，用d3来“画”一幅图，你需要从最基本的元素开始画起，但是却提供了最大的灵活性，理论上你可以使用d3画一切图形，目前也有许多基于d3的可视化库，它们大多比d3更加high-level，可以按照其提供的recipe更方便的画出高级的图形而不用关心基本图形的实现细节，相应的，灵活性会相对低一些。

- 如何结合？

    **方案1:直接在html中包含或引用js代码**:

    ​	在angular2的html模版中直接嵌入基于d3.js的script代码，不过这“很不angular”，按照angular的设计哲学，静态模版是html，模版中需要的数据由动态代码逻辑提供，这些逻辑完全存在于angular的component中，component实例化以后，得到的对象在其生命周期中可以提供数据和方法来渲染模版。

    **方案2：使用DefinitelyTyped**

    ​	很自然地，我们会想到在angular的component中编写d3可视化代码，然而d3是一个js库，如何使用typescript来调用d3的接口呢？这就需要引入DefinitelyTyped了，[DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped)是主要为js库的接口提供 *.d.ts文件，这些文件为js接口提供类型定义，以与typescript兼容，有了这些定义，在typescript代码中调用js库的接口函数就像在js代码中调用这些借口一样容易。目前，DefinitelyTyped已经为很多js库提供的支持，并且会和官方的js库保持同步更新。


#### 2. 在angular2+项目中使用d3

​	我们假定读者已经对于typescript和angular2已经有一定了解，可以基于angular2框架开发简单的前端应用。

 -  下载d3

    我们使用[d3-ng2-service](https://github.com/tomwanzek/d3-ng2-service)来实现d3库的引入，该项目中还有一个[demo project](https://github.com/tomwanzek/d3-ng2-demo)，演示如何使用该库在angular2项目中使用d3画图。

    ```
    npm install d3-ng2-service --save
    ```

    该包提供了D3Service：Angular D3可注入服务；

    D3： Typescript中d3对象（变量）的别名，该对象（变量）可以从D3Service中获得；

    ​一些列D3模块的提供的TypeScript 接口如Selection，Transition，Axis等

    ​

-  模块(module)中引入d3

     在component所属的module中引入d3

    ​```
    import { BrowserModule } from '@angular/platform-browser';
    import { NgModule, ApplicationRef } from '@angular/core';
    import { CommonModule } from '@angular/common';
    import { FormsModule } from '@angular/forms';
    import { AppComponent } from './app.component';
    
    import { D3Service } from 'd3-ng2-service'; // <-- import statement


    @NgModule({
      declarations: [
        AppComponent,
        TestD3Component // <-- declaration of the D3 Test component used below
      ],
      imports: [
        BrowserModule,
        CommonModule,
        FormsModule
      ],
      providers: [D3Service], // <-- provider registration
      entryComponents: [AppComponent],
      bootstrap: [AppComponent]
    })
    export class AppModule {
    
    }
    ​```

-  组件(component)中使用d3

   ```
   import { Component, OnInit, ElementRef } from '@angular/core';
   import { D3Service, D3, Selection } from 'd3-ng2-service'; // <-- import the D3 Service, the type alias for the d3 variable and the Selection interface

   @Component({
     selector: 'app-test-d3',
     templateUrl: 'test-d3.component.html',
     styleUrls: ['test-d33.component.css']
   })
   export class TestD3Component implements OnInit {

     private d3: D3; // <-- Define the private member which will hold the d3 reference
     private parentNativeElement: any;

     constructor(element: ElementRef, d3Service: D3Service) { // <-- pass the D3 Service into the constructor
       this.d3 = d3Service.getD3(); // <-- obtain the d3 object from the D3 Service
       this.parentNativeElement = element.nativeElement;
     }

     ngOnInit() {
       let d3 = this.d3; // <-- for convenience use a block scope variable
       let d3ParentElement: Selection<any, any, any, any>; // <-- Use the Selection interface (very basic here for illustration only)

   // ...

       if (this.parentNativeElement !== null) {

         d3ParentElement = d3.select(this.parentNativeElement); // <-- use the D3 select method 

         // Do more D3 things 

       }
     }

   }
   ```

-  绘制force-directed graph(力导向图)

   d3库提供了很多图形元素，如饼状图，力导向图，集群图，树状图，打宝图，地图等，本文主要阐述如何绘制力导向图.


```
ngOnInit() {
    let self = this;
    let d3 = this.d3;
    let d3ParentElement: Selection<HTMLElement, any, null, undefined>;
    let svg: Selection<SVGSVGElement, any, null, undefined>;
    let d3G: Selection<SVGGElement, any, null, undefined>;
    let width: number;
    let height: number;

    let index = 10;
    let link ;
    let node ;
    let simulation: Simulation<any, undefined>;

    
       let nodes = [
   {"id": 1, "name": "server 1"},
   {"id": 2, "name": "server 2"},
   {"id": 3, "name": "server 3"},
   {"id": 4, "name": "server 4"},
   {"id": 5, "name": "server 5"},
   {"id": 6, "name": "server 6"},
   {"id": 7, "name": "server 7"},
   {"id": 8, "name": "server 8"},
   {"id": 9, "name": "server 9"}
 ]

 let links = [
   {source: 1, target: 2},
   {source: 1, target: 3},
   {source: 1, target: 4},
   {source: 2, target: 5},
   {source: 2, target: 6},
   {source: 3, target: 7},
   {source: 5, target: 8},
   {source: 6, target: 9},
 ]



    function update() {
   link = svg.selectAll(".link")  
    .data(links, function(d:any) { return d.target.id; })    <-- 绑定links数据

  link = link.enter()  <-- 集合操作，为所有link元素设置属性，返回值是line集合
    .append("line")
    .attr("class", "link")
    .style("stroke","#ccc")
     .style("stroke-width",1);

   node = svg.selectAll(".node") 
    .data(nodes, function(d:any) { return d.id; })   <--绑定nodes数据

  node = node.enter()  <-- 集合操作，为所有node元素<g>设置属性，每个<g></g>中包含<circle>,<title>,<text>等元素，返回值是g元素集合，返回给node
    .append("g")
    .attr("class", "node")
    .on("click", click)
    .call(d3.drag()  <-注册·拖拽回调函数 所有<g>元素有相应事件时，都会调用注册的回调函数
        .on("start", dragstarted)
        .on("drag", dragged)
        .on("end", dragended));

  node.append("circle")  <-- 绘制<circle>，返回的依然是node<g>集合
    .attr("r", 25)
    .style("fill",function(d,i) { return d3.schemeCategory20c[i*3 + 5] })

  node.append("title")  <-- 绘制title，返回的依然是node<g>集合
      .text(function(d) { return d.id; });

  node.append("text")  <-- 添加文本元素，返回的依然是node<g>集合
      .attr("dy", 3)
      .text(function(d) { 
        console.log(d);
        return d.name._1.name;
     });

  simulation
      .nodes(nodes)  <-- 将nodes数据应用于simulator
      .on("tick", ticked);  <-- 按照力学规则计算后，使用ticked函数刷新集合中所有元素属性

  simulation.force<ForceLink<any, any>>("link")  <-- 应用link类型的force
      .links(links);    <--通过force重新计算links的属性
}


function ticked() {  <-- 定期刷新<line>和node<g>集合中元素属性的方法
  link
      .attr("x1", function(d) { return d.source.x; })
      .attr("y1", function(d) { return d.source.y; })
      .attr("x2", function(d) { return d.target.x; })
      .attr("y2", function(d) { return d.target.y; });

  node
      .attr("transform", function(d) { return "translate(" + d.x + ", " + d.y + ")"; });
}

function dragstarted(d) { <--拖拽开始
  if (!d3.event.active) simulation.alphaTarget(0.3).restart()
}

function dragged(d) {  <-拖拽过程， 位置修正
  d.fx = d3.event.x;
  d.fy = d3.event.y;
}

function dragended(d) {  < -- 拖拽结束
  if (!d3.event.active) simulation.alphaTarget(0); 
  d.fx = undefined;
  d.fy = undefined;
}

    if (this.parentNativeElement !== null) {  <--确保父HTML元素加载完成

      d3ParentElement = d3.select(this.parentNativeElement);
      svg = this.d3Svg = d3ParentElement.select<SVGSVGElement>('svg');  <--选中svg元素作为画布。
      width = +svg.attr('width');   <-- 获取画布宽度
      height = +svg.attr('height');  <--获取画布长度

    simulation = d3.forceSimulation()    <-- 定义一个力导向图simulator，绑定nodes数据
    .force("link", d3.forceLink().distance(200).id(function(d:any) { return d.id; })) <-- 绑定links数据
    .force("charge", d3.forceManyBody())   <-- 设置力学模拟，所有点nodes都有作用力
    .force("center", d3.forceCenter(width / 2, height / 2));  <--设置力学中心点为画布中心

    update();
    }
  }
```



以上只是在component内部提供了nodes和links数据，如果有需要的话，可以在component中使用Service获取数据或者通过父模板传入数据。

- d3中比较重要的几个概念：

  **理解以下几个概念对于理解d3至关重要：**

  1. **集合操作**

     d3提供的接口大多是先获取一个 selection（选择集），然后对集合元素进行整体操作，对于集合元素需要相应的事件，多数以注册的回调函数来处理

  2. **enter-update-exit状态**

     d3需要获取数据和元素之间的映射关系，之后在对选择的元素集进行操作时，每个元素都和相应的数据绑定

     如果A是数据集合，B是元素集合，那么

     enter()是 A-A∩B：尚未和映射到元素集合的数据集，初始化的时候由于元素集合中并没有数据，所以常用enter()方法进行绑定

     update()是A∩B  ：已经在元素集合中的数据集

     exit()是B-A∩B   ：元素集合中数据已经删除，这个集合中的元素已经不再具有任何数据，因此正好和名称吻合。

- **在typescript中调用d3库需要注意的问题**


  1. 使用d3 v4:

     d3 v4版本进行了十分彻底的模块化，使用更方便，并且很多案例也是基于v4版本

  2. 接口一致，类型补充

     typescript代码中使用的d3接口与js中使用d3的接口是完全一致的，但是，有时候需要根据*.d.ts文件中的接口描述添加入参或者返回值类型，这需要对typescript语言有一定了解，熟悉typescript中的interface,class,generics和union types等概念，这些概念大体上与一般面向对象语言中相应的概念比较类似，但是会有一些差异点，此处不再赘述。

     ​