> ### 起点 k8s.io/tools/cache/shared_informer.go
* 主要组件
    * 带索引的本地缓存Storage、索引Indexer
    * 控制器Controller：主要是使用ListerWatcher从apiserver拉取资源对象数据和资源变更通知。并推送这些通知到DelatFIFO队列中，并发地把事件弹出DeltaFIFO队列，然后使用sharedIndexInformer::HandleDeltas来处理这些事件。
    * 事件处理器type sharedProcessor struct{} : 主要是分发
```go
type sharedIndexInformer struct {
    indexer    Indexer
    controller Controller
    processor             *sharedProcessor
    cacheMutationDetector MutationDetector
    listerWatcher ListerWatcher
    // objectType is an example object of the type this informer is
    // expected to handle.  Only the type needs to be right, except
    // that when that is `unstructured.Unstructured` the object's
    // `"apiVersion"` and `"kind"` must also be right.
    objectType runtime.Object
    // resyncCheckPeriod is how often we want the reflector's resync timer to fire so it can call
    // shouldResync to check if any of our listeners need a resync.
    resyncCheckPeriod time.Duration
    // defaultEventHandlerResyncPeriod is the default resync period for any handlers added via
    // AddEventHandler (i.e. they don't specify one and just want to use the shared informer's default
    // value).
    defaultEventHandlerResyncPeriod time.Duration
    // clock allows for testability
    clock clock.Clock
    started, stopped bool
    startedLock      sync.Mutex
    // blockDeltas gives a way to stop all event distribution so that a late event handler
    // can safely join the shared informer.
    blockDeltas sync.Mutex
}
```




> ### Reflector k8s.io/tools/cache/reflector.go
* Run(stopCh chan struct{}) 这个函数会启动list/watch，如果stopCh被关闭了则Run函数会退出，如果发生错误也会退出
```go
// 这里是informer包的的start，会启动cache包的Run，会初始化在Start函数之前申明的informer、lister,如
t.factory = informers.NewSharedInformerFactory(kubeclient, time.Second*60)
t.storageClassLister = t.factory.Storage().V1().StorageClasses().Lister()
t.factory.Start(t.stopCh)

// Start initializes all requested informers.
func (f *sharedInformerFactory) Start(stopCh <-chan struct{}) {
    f.lock.Lock()
    defer f.lock.Unlock()

    for informerType, informer := range f.informers {
        if !f.startedInformers[informerType] {
            // informer 这里执行cache包的reflector的Run函数
            go informer.Run(stopCh)
            f.startedInformers[informerType] = true
        }
    }
}
```


> ### DeltaFIFO
* 事件类型有：Sync(定期执行), Replaced, Added, Updated
* 注意这个函数里面的isSync，他会触发OnUpdate函数调用，且是定期执行的，所以要检查resourceVersion是否变化了
    ```go
    func (s *sharedIndexInformer) HandleDeltas(obj interface{}) error { 
    ```