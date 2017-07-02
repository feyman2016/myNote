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

    **方案1**:在angular2的html模版中直接嵌入基于d3.js的script代码，不过这“很不angular”，按照angular的设计哲学，静态模版是html，模版中需要的数据由动态代码逻辑提供，这些逻辑完全存在于angular的component中，component实例化以后，得到的对象在其生命周期中可以提供数据和方法来渲染模版。

    **方案2**:很自然地，我们会想到在angular的component中编写d3可视化代码，然而d3是一个js库，如何使用typescript来调用d3的接口呢？这就需要引入DefinitelyTyped了，[DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped)是主要为js库的接口提供 *.d.ts文件，这些文件为js接口提供类型定义，以与typescript兼容，有了这些定义，在typescript代码中调用js库的接口函数就像在js代码中调用这些借口一样容易。目前，DefinitelyTyped已经为很多js库提供的支持，并且会和官方的js库保持同步更新。