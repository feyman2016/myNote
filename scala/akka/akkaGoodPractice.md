# Akka Good Practice  

&ensp; &ensp; 以下内容总结自lightbend akka培训时与讲师Eric交流

- 什么是Actor模型？  
    &ensp; &ensp; Actor就像真实世界中的人，人都是独立的，可以自己决定对外来的信息作出何种反应  
    &ensp; &ensp;一个Actor系统中可能有很多Actor，Actor相互通信的方式就是发消息    
    &ensp; &ensp;Actor系统中，一切都是Actor  
    

## 1.Actor监管
事实上Actor系统就类似于一个军队或者家族，有着鲜明的上下级关系，上级可以对下级进行监督。

**拆分Actor**

&ensp; &ensp; 根据单一指责原则，如果一个master Actor需要处理的消息太多，那么Actor内的代码和逻辑可能会变得非常复杂而不利于维护，
通常的做法是，使用master Actor创建不同类型的worker Actor，专门用来处理特定的任务，master Actor每次收到特定类型的消息，
都交给相应地worker Actor来处理，这样会使得不同Actor的职责更清晰。

&ensp; &ensp; 以上类似于一个将军可能面对敌军突击，他可能会需要知道什么战术可以突围，怎样可以解决粮草问题，并且需要与敌军刚正面，
诸如此类，显然将军不会亲自干这些事情，他会让军师研究下战术阵型，让排头兵去冲锋陷阵，诸如此类。

**push down可能发生异常的Actor**

&ensp; &ensp; 考虑战争中一些危险的任务，比如炸碉堡，潜入敌营等任务，如果一个将军去干失败会怎么样？  
我想大概只有两个字：血崩！第二天就会传出大将军阵亡的消息，军心大乱，全军覆没。当然，这件事如果让一个
武艺高强的底层小兵来干，即使失败了，也不会有什么影响，毕竟他只是一个底层小兵。

&ensp; &ensp; 类似的，在Actor系统中，如果一个Actor因为某些异常而终止，那么他所有的下级都会终止，因此面对那些容易
抛出exception的消息，我们尽可能让更底层的Actor来处理，这样可以尽可能缩小发生风险时影响的范围。总而言之，
我们要尽量push down此类Actor在Actor系统中的层级。

## 2.FSM

&ensp; &ensp; FSM是Finite State Machine，在Actor系统中可以用于表示一个Actor的状态，一般来说，Actor在不同状态下
对收到的消息会有不同的行为。

*“阿花啊，饿不饿啊，我下碗面给你吃啊”*   
*“没胃口，吃龙肉都没味道啊～～”*

&ensp; &ensp; 阿花可能平时是个吃货，但是今天可能心情不好，啥都不想吃，那么阿花的心情就可以用状态机来表示。

&ensp; &ensp; FSM很不错，但是如果你写的Actor像阿花这么单纯质朴，只有两种状态的话，完全可以定义两个receive函数，
来定义在普通状态下对接受的消息的处理，使用context become/unbecome ${someState}来处理状态之间的转换，
这种方式简洁，易维护，但是debug需要自定义log语句。

&ensp; &ensp; FSM相对比较重，但是带有强大的log系统，可以方便的配置，使得Actor在状态切换时的日志都被保存下来，便于
debug。

&ensp; &ensp; 现实系统中实现有状态的Actor，需要根据状态的复杂程度来选择实现方法，一般状态比较多且复杂的建议使用FSM，
而状态简单建议自定义。

## 3. Router的监管策略
&ensp; &ensp; 如果一个工人每天能搬100块砖，建筑施工每天需要1000块砖，那么最好招一个包工头，包工头找十个工人，让每个
工人搬10块。

&ensp; &ensp; Actor系统中的Router就类似包工头，不过Router分两种，一种叫做Pool router，这种包工头自己招工，工人归自己管；
还有一种Group Router，这种包工头找到现成的工人，把搬砖任务发给他们，但是这些工人不归他管。

&ensp; &ensp; 我们来谈谈这些工人的监管策略

Pool Router：  
&ensp;  &ensp; 工人直接归包工头管，工人工伤不能干活了，包工头的监管策略是直接上报给自己的上级总工程师，由总工程师来决策。
&ensp;  &ensp; Actor系统中之所以这么实现的逻辑是：本来搬砖的活不多没有包工头的时候，总工程师直接监管工人，后来搬砖的活多了，
总工程师招了个包工头，包工头管工人，根据包工头对工人的监管策略，我们知道，最终对监管工人的还是总工程师，因此加入Pool Router 
不会影响原来的代码逻辑。

Group Router：
&ensp;  &ensp; 这种Actor不会自己招工，只是把活发给工人，这些工人本来就有自己的直接上级，因此系统中加入包工头后也不需要修改系统
中其他Actor的逻辑。

&ensp;  &ensp; 综上，引入不同类型的Router都很方便，无需修改原来Actor的逻辑。

## 4. Ask pattern
Akka中发消息的两种方式 :  
        `a ! msg    //normal way  `  
        `a ? msg   // ask pattern`

当使用第一种方式是发了就发了，并没有期待对方对这条消息的直接回应；第二种方式是：给a发了消息后，就像给a写了
情书，盼着受到她的回复，针对这种场景，akka中提供了两种处理方式：

 2.   使用Future[T]   
    一般会结合Timeout参数使用，对于一个Future类型的f，可以使用如下方式处理：
    
        f.onComplete match {
        case Success(x) => //XXX
        case Failure(e) => //YYY}
        
    在timeout内，如果收到回复，就进入Success，否则，就match Failure，这里要注意的一点是：
    
    **尽量不要在f.onComplete的case branch内使用sender（）方法，因为future本身会在另一个线程中，导致sender（）方法返回值可能不是你想要的
    ，从安全性出发，建议是用的方法是pipeTo**
    
 2.   使用pipeTo  
    `a ? msg pipeTo b   //使用ask patter给a发消息，pipeTo内部以一种安全的方式将收到的来自a的response转发给b`  
    `a ? msg pipeTo self //与上面例子的差别就是消息最后又发给了自己，可以在receive函数中定义相应的处理方法。`  
 
