# mmo场景管理

mmo场景处理主要在Logic进程，SceneMgr进程，Scene进程。
玩家登陆成功后，玩家相关相关数据加载完成会触发事件 EVENT_DATA_LOAD_COMPLETE 该事件其中一个回调函数是 SceneClientMgr::PlayerDataLoaded,执行流程如下图所示：

![](https://i.imgur.com/Ry3mAxh.png)