# mmo场景管理

mmo场景处理主要在Logic进程，SceneMgr进程，Scene进程。
玩家登陆成功后，玩家相关相关数据加载完成会触发事件 EVENT_DATA_LOAD_COMPLETE 该事件其中一个回调函数是 SceneClientMgr::PlayerDataLoaded,执行流程如下图所示：

![](https://i.imgur.com/xVV8HP7.png)

> Logic 进程内一旦触发事件EVENT_DATA_LOAD_COMPLETE，就会向该玩家客户端发送一条同步消息包括位置，方向等信息。设置玩家的属性z(坐标）的改变对应的回调函数，该函数会将SYNCTOSCENE设为1，定时器就会将玩家的位置信息发送到SceneMgr进程中(用来同步)。还会给进程SceneMgr进程发送LOGIC_MSG_ENTER_SCENE消息，给玩家客户端发送场景相关的数据。
> SceneMgr进程收到LOGIC_MSG_ENTER_SCENE消息会保存相关数据，向Scene进程发送SCENEMGR_MSG_CREATE_SCENE消息和SCENEMGR_MSG_ENTER_SCENE消息。
> Scene进程在收到SCENEMGR_MSG_CREATE_SCENE消息后，如果场景不存在就会创建一个场景对象，设置相关属性。创建场景相关的AreaBase对象(用于刷新视野）触发一些事件。收到SCENEMGR_MSG_ENTER_SCENE消息，主要创建一个场景单元对象，设置一些属性。触发一些事件。



客户端进入游戏时会向服务器发送CLIENT_MSG_ENTER_SCENE消息，相关处理流程如下图所示：

![](https://i.imgur.com/B5QGJJX.png)

> Logic进程在收到玩家发送的CLIENT_MSG_ENTER_SCENE消息，会向SceneMgr进程发送LOGIC_MSG_APPEAR_SCENE消息。
> SceneMgr进程在收到消息后，会向Scene进程发送SCENEMGR_MSG_APPEAR_SCENE消息。
> Scene进程收到消息后主要把场景单元对象放到AreaBase(该对象基于RTree [https://en.wikipedia.org/wiki/R-tree](https://en.wikipedia.org/wiki/R-tree "RTree"))


玩家退出游戏，将会触发事件EVENT_PLAYER_DESTROY ，处理流程：

![](https://i.imgur.com/MBEJe04.png)

> Logic进程在玩家退出游戏（一段时间后，定时器）会触发事件EVENT_PLAYER_DESTROY，向SceneMgr进程发送LOGIC_MSG_LEAVE_SCENE消息
> SceneMgr进程收到消息后，会删除相关数据，向Scene进程发送SCENEMGR_MSG_LEAVE_SCENE消息。
> Sence进程收到消息后，删除玩家在场景中的对象和从AreaBase移除相关对象。


如果玩家掉线，该玩家会触发事件EVENT_PLAYER_GATE_LOST，如果gate的进程和logic进程之间连接断掉，gate上的所有玩家都会触发该事件，相应的处理流程：

![](https://i.imgur.com/UcKeEOV.png)

>Logic进程发送消息LOGIC_MSG_DISAPPEAR_SCENE
>SceneMgr进程收到消息后，发送SCENEMGR_MSG_DISAPPEAR_SCENE消息
>Scene进程收到消息后，从AreaBase移除相关对象。


在场景中加入Monster,Npc的过程和玩家进入场景差不多。
![](https://i.imgur.com/OVINjE3.png)

