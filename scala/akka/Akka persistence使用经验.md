## Akka persistence使用经验 

​	 akka系统中主要是用消息通信，Actor接受消息并对根据消息作出相应的行为，根据业务需求，有时会在Actor内用可变量来保存运行时数据，但是Actor如果重启，那么这些可变量中存储的运行时数据则会丢失，为提高系统的可用性，akka使用akka-persistence来恢复Actor的数据。

​	官方文档请见：http://doc.akka.io/docs/akka/current/scala/persistence.html

下面贴出官网中文档，并对其简要介绍：

```scala

package sample.persistence

import akka.actor._
import akka.persistence._

object SnapshotExample extends App {
  final case class ExampleState(received: List[String] = Nil) {
    def updated(s: String): ExampleState = copy(s :: received)
    override def toString = received.reverse.toString
  }

  class ExamplePersistentActor extends PersistentActor {
    def persistenceId: String = "sample-id-3"

    var state = ExampleState()

    def receiveCommand: Receive = {
      case "print"                               => println("current state = " + state)
      case "snap"                                => saveSnapshot(state)
      case SaveSnapshotSuccess(metadata)         => // ...
      case SaveSnapshotFailure(metadata, reason) => // ...
      case s: String =>
        persist(s) { evt => state = state.updated(evt) }
    }

    def receiveRecover: Receive = {
      case SnapshotOffer(_, s: ExampleState) =>
        println("offered state = " + s)
        state = s
      case evt: String =>
        state = state.updated(evt)
    }

  }

  val system = ActorSystem("example")
  val persistentActor = system.actorOf(Props(classOf[ExamplePersistentActor]), "persistentActor-3-scala")

  persistentActor ! "a"
  persistentActor ! "b"
  persistentActor ! "snap"
  persistentActor ! "c"
  persistentActor ! "d"
  persistentActor ! "print"

  Thread.sleep(10000)
  system.terminate()
}
```



​        如果要对一个Actor做持久化，那么只需要extends PersistentActor,并实现如下方法：

```
def receiveCommand: Receive = {}//运行时接收命令并处理
def receiveRecover: Receive = {}//重启或启动时根据事件或快照重放事件，恢复状态
```

​        receiveCommand方法在Actor运行时接收消息并对消息进行处理，实现receiveCommand方法只需要将原先recevie函数中，改变内部状态的部分进行持久化：

```
 case s: String =>
        persist(s) { evt => state = state.updated(evt) }
```

​       上述的case branch中，persist(s)表示将事件s持久化，而{…}中的语句代表对evt的处理，这与非持久化的场景比较类似，这里持久化的事件s是Actor从故障中恢复内部状态的关键，所有改变内部状态的事件都可以以事件的方式被存储下来，Actor在restart的时候默认会replay这些事件，而针对这些事件Actor的行为在函数receiveRecover中定义，该案例中，对事件s的恢复行为如下：

```
 case evt: String =>
        state = state.updated(evt)
```

​        与receiveCommand中的行为完全一致，都是调用了state.updated(evt)方法，这样就保证了Actor在恢复的时候会按照正确的顺序重放时间，并逐步恢复Actor的状态。

​	但是这时候有一个问题，如果Actor运行了很久，比如三个月，加入这时Actor因为故障重启，那么按照Actor默认的策略，它将会依此重放三个月的所有事件，这带来的时间开销将会惊人的大，为了应对这种情况，akka-persistence提供了一种snapshot机制，即可以按照一定策略将Actor运行时状态全部保存下来，那么理论上，在Actor故障恢复的时候，在Actor某快照之前的所有事件重放的结果即可以直接以该快照的瞬时状态来表达，可以想象，这会大幅缩小系统恢复所需要的时间。当