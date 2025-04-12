# Unity协程和线程，进程的区别
* 进程，Windows系统，包含运行一个进程所需要的基本资源，一个正在运行的应用程序，进程可以包括一个或多个线程。
* 一般应用一个应用程序只使用线程这一资源
* Unity只使用了一个线程，但为了同时做很多事，引申出协程的概念。
* 协同程序，协作式程序，协程是一种伪线程，互相依赖，依次使用CPU，每次只有一个协程工作，其他协程处于休眠状态，每个协程对CPU进行分时，协程可以访问和使用unity的所有方法和component。
* 同一时间只能执行某个协程，协程适合对某个任务进行分时处理。
* 控制代码在特定的时间执行。
* 协程不是线程，也不是异步执行，跟Update一样，在主线程中执行。
* 不用考虑同步和锁的问题
* 协程是一个分部组件，遇到条件yield return会挂起，知道条件满足才会被唤起执行后面的语句。
# 本地坐标系 世界坐标系
* 世界坐标系：世界坐标是指物体在场景中的坐标，当某个物体没有父物体时，它的position就是世界坐标的position，rotation同理；
* 本地坐标相对于父物体的坐标，以父物体本身为坐标轴进行计算，与世界坐标没有必然联系。没有父物体的坐标不存在本地坐标的概念。
* 本地坐标系，物体存在父物体时，inspector栏transform中的position实际是localpsition，即本地坐标。
* TransformPoint方法将本地坐标系转为世界坐标系。
# Unity调整旋转
* 矩阵旋转
  * 优点，旋转轴可以是任意向量
  * 缺点，旋转其实只需要知道一个向量+一个角度（4个信息值），矩阵用了16个元素，消耗内存和时间
* 欧拉角旋转
  * 优点，容易理解，形象直观，只需3个值x，y，z的旋转角度
  * 缺点，需要按照固定坐标轴的顺序旋转，不同顺序会造成不同结果；欧拉角旋转会造成万向锁现象，某些坐标轴重合，丢失一个方向上的旋转能力（两个旋转轴（环）重叠），需打破原先旋转顺序或同时旋转3个轴。
  * 由于万向锁，欧拉旋转无法实现球面平滑插值
  * 万向锁的简单解决办法，构造一个不同的旋转层级顺序，但总是会在某一个顺序发生，Unity使用Z-X-Y顺规，出现万向锁的概率最小。
  * 解决办法，欧拉角转换四元数，进行Slerp插值，四元数再转换为对应欧拉角，作用于需要旋转的对象，缺点在于消耗内存，但可以使物体任意旋转，灵活度高。
  * 欧拉旋转出现旋转路径偏移的根本原因，万向锁情况下对欧拉角的插值不是线性的（突变）。
  * 静态欧拉角，旋转轴使用静止不同的参考系。
  * 动态欧拉角，使用object本身的坐标系，随着object旋转而旋转。（局部坐标系会随着对象的旋转而旋转）
* 四元数旋转
  * 优点：可以避免万向锁，只需一个4维的四元数就可以执行绕任意过原点的向量的旋转，方便快捷，在某些实现下比旋转矩阵效率更高；可以提供平滑插值。
  * 缺点：比欧拉角稍微复杂，多了一个维度，理解困难，不直观。
# UGUI的渲染流程
[UGUI渲染流程](https://www.zhihu.com/question/312113725/answer/2378626636)
## 显示一个UI，并捕获用户的输入和操作从而驱动应用程序作出反应
* 显示，点->三角面->UI。
* 捕获，射线检测，从摄像机发出射线过当前Pointer或者Touch所在位置，获得碰撞的GameObject列表。
* 调度，发生在EventSystem的Update中，找到当前的Module并处理，包含对移动点击拖动事件的处理。
* 处理：对于发生的事件，由ExecuteEvents中的Execute方法处理。
## 1.事件数据
* EventData：BaseEventData, AxisEventData, PointerEventData
* BaseEnventData:事件数据类的父类，包括EventSystem,InputModule和当前选中GameObject的引用；
* AxisEventData:滚轮事件数据，只记录滚动的方向数据。
* PointerEventData:点位事件数据，包含当前位置，滑动距离，点击时间以及不同状态下GameObject的引用
* 点击事件发生，UGUI获取点位事件数据，在整个事件处理流程中传递
## 2.输入模块
* 输入检测模块，规定对事件的处理逻辑和细节，处理鼠标点击事件，拖拽和移动，TouchInputModule面向触摸平台和移动设备的输入检测模块，StandaloneInputModule面向标准鼠标键盘
* 去看源码
## 3.射线碰撞检测
* 从摄像机的屏幕位置上进行射线碰撞检测并获取碰撞结果，返回给事件处理逻辑类，交由事件处理模块处理。
* 2D射线碰撞检测，预留了2D的层级次序
* 3D射线碰撞检测，以距离大小为依据进行排序（距离越近越优先）。
* GraphicRaycaster类为UGUI元素点位检测的类，放在Core渲染块，主要针对ScreenSpaceOverlay模式下的输入点位进行碰撞检测，不依赖于射线碰撞，通过遍历所有可点击的UGUI元素进行检测比较，特殊。
## 4.事件调度
* 事件逻辑处理模块的主要逻辑都集中在EventSystem类中，其余类都只对它起辅助作用。
* EventInterfaces类、EventTrigger类、EventTriggerType类定义了事件回调函数，ExecuteEvents类编写了所有执行事件的回调接口。
* EventSystem类处理由射线碰撞检测后引起的各类事件。
* 唯一继承MonoBehavior类并在Update帧循环中做轮询。
# NGUI与UGUI
* 可视化操作，便携，省心
  * ugui根目录canvas,ngui根目录uiroot
  * 屏幕自适应，ugui为render mode;ngui为scaling style
  * anchor锚点，固定位置，ugui花瓣锚点不好调
  * ngui高灵活性，创建sprite，添加boxcollider，作为按钮，滑动条
  * ugui的sprite切图；ngui使用图集而不能直接拖拉
  * ngui的tween动画功能省心，无需额外定义代码，使用封装好的脚本即可，叠加脚本实现复杂动画效果
* 总结
  * ugui的canvas有世界坐标和屏幕坐标
  * ugui的image可以使用material
  * ugui通过mask裁剪，ngui通过panel的Clip
  * ngui渲染前后顺序通过widget的depth，ugui渲染顺序根据Hierarchy的顺序，越下面渲染在顶层
  * ugui不需要绑定colliders，ui可自动拦截事件
  * ugui的anchor是相对父对象，没有提供高级选项，操作方便
  * ugui没有atlas，使用sprite packer
  * ugui的Navigation在Scene中能可视化
  * ugui的事件需要实现事件系统的接口，写起来简单
* 优缺点
  * ngui保留图集，需要进行图集的维护，ugui没有图集的概念，可以充分利用资源，避免重复资源
  * ugui出现锚点的概念，方便屏幕自适应
  * ngui支持图文混排
  * ugui没有UIwrap来循环scrollview内容
  * ugui没有Tween组件
* 新项目建议使用ugui，或新开发的ui toolkit
# UI事件渗透
* 推荐ugui，处理UI和3d模型的点击
* UI和模型叠加，优先响应最前面的UI
```
//把事件透下去
    public void  PassEvent<T>(PointerEventData data,ExecuteEvents.EventFunction<T> function)
        where T : IEventSystemHandler
    {
        List<RaycastResult> results = new List<RaycastResult>();
        EventSystem.current.RaycastAll(data, results); 
        GameObject current = data.pointerCurrentRaycast.gameObject ;
        for(int i =0; i< results.Count;i++)
        {
            if(current!= results[i].gameObject)
            {
                ExecuteEvents.Execute(results[i].gameObject, data,function);
                //RaycastAll后ugui会自己排序，如果你只想响应透下去的最近的一个响应，这里ExecuteEvents.Execute后直接break就行。
            }
        }
    }
```
* UI下面对应接收点击事件的地方都可以响应
# DoTween
* ShortcutExtensions, 定义Transform，Rigidbody，Material等常用组件的扩展方法
* DOTweenComponent，Unity的MonoBehaviour类，主要负责驱动TweenManager
* DoTween，负责全局的把控，时间，版本，初始化，把每个TweenerCore加入到TweenManager进行管理
* TweenManager，主要负责维护所有动画数据集，并在Update接口驱动
* TweenerCore，Tween的模板子类
* ABSTweenPlugin具体实现类
* ColorPlugin，用于计算颜色的子类
* VectorPlugin，用于计算Vector3的子类
* EaseManager，分量计算类，通过Ease枚举，以及当前已花费的时间，得出真正的比率（0~1）
* 扩展函数inC#，像调用类的原生函数一样调用自定义函数，简洁，高效，减少了入参的数量和相关的有效性判断，一举多得。
# 碰撞器和触发器的区别
* 碰撞器是触发器的载体，触发器只是碰撞器身上的一个属性。
* isTrigger=false，碰撞器根据物理引擎引发碰撞，产生碰撞的效果，可以调用OnCollisionEnter/Stay/Exit函数。
* isTrigger=true，没有碰撞效果，可以调用碰撞函数
* 触发器使用场景，检测到物体的接触但不让碰撞影响物体移动；检测一个物体是否经过空间中的某个区域
# UGUI canvas的三种模式
* Canvas画布，UI元素的根目录
* RenderMode三种渲染模式
* Screen Space-Overlay（覆盖）模式，画布填满整个屏幕空间，UI置于屏幕最上层，画布会自适应屏幕。
  * 参数Pixel Perfect，使UI元素像素对应，边缘清晰不模糊
  * Sort Layer，指示画布深度
* Screen Space-Camera模式，画布填满屏幕，自适应，画布被放置在摄影机前方，由视角设定，视角广度由Filed of view设置。
  * 可用来在UI上显示3D模型，如MMO游戏查看人物装备界面
  * Render Camera：渲染摄像机
  * Plane Distance：画布距离摄像机的距离
  * Sorting Layer，画布深度，可设置标签和层，越下面显示优先级越高
  * Order in Layer，相同的Sort Layer下画布显示先后顺序，数字越大，优先级越高
* World Space，画布被视为Plane面片，UI为场景的一部分
  * Event Camera，指定接受事件的摄像机，通过画布上GraphicRaycaster组件发射射线产生事件
  * 用来实现跟随人物移动的血条
# UnityMonoBehaviour生命周期
* MonoBehaviour是Unity中所有脚本的基类，显式继承
* 可重写函数
  * Awake：当一个脚本实例被载入时调用，成员变量初始化
  * OnEnable：对象变为可用或激活状态时调用
  * Start：仅在第一次Update之前调用，在Awake之后调用，初始化一些需要依赖Awake的变量，执行StartCoroutine进行协程的触发，C#必须用StartCoroutine开始一个协程
  * Update：MonoBehaviour启用时，每一帧被调用
  * FixedUpdate：每一固定帧被调用
  * LateUpdate：在所有Update函数调用后被调用，用户调整脚本执行顺序，例如实现相机跟随
  * OnGUI：渲染和处理GUI事件时调用，不是每帧都调用
  * OnDisable:对象变为不可用或非激活状态时调用
  * OnDestroy：当MonoBehaviour将被销毁时调用，不保证调用后对象是否被销毁
* 编辑器，初始化，更新，GUI渲染，销毁
# Unity中的GC以及优化
[【Unity3d游戏开发】浅谈Unity中的GC以及优化](https://blog.csdn.net/worisaa/article/details/64121436)
* GC，垃圾回收
* 堆内存，大且时间较长
* 堆栈内存，小且时间短暂
* 变量激活，占用内存标记为使用，变量不激活，回收到内存池，堆栈上回收快速，堆上不及时回收
* 垃圾回收主要指堆上的内存分配和回收，定期做GC
* 栈内存分配和回收，类比于stack后进先出
* 堆内存分配和回收机制
  * 存储，先检测是否有闲置内存单元；没有，则触发GC释放内存，缓慢，有则分配；回收完还没有，扩展堆内存大小，很缓慢
## GC操作
* 检查堆内存上的每个存储变量
* 对每个变量检测其引用是否处于激活状态
* 不激活的，标记为可回收
* 移除被标记的变量，堆内存++
* 及其耗时，堆上变量和引用越多操作越多
## 何时触发垃圾回收
* 堆内存不够
* 自动GC，不同平台运行频率不同
* 强制执行
* 当堆内存不足时，GC会频繁触发
## GC带来的问题
* 耗时，关键时刻GC导致游戏帧率下降
* 内存碎片，堆内存分配和回收导致内存碎片，导致游戏占用内存越来越大，GC频繁触发
* 问题表现：帧率运行低，性能间歇中断或降低，通过打开Unity的Profiler Window查看，CPU usage分析窗口，检测任何一帧cpu的内存分配情况，GC alloc，定位函数
## GC优化
* 值类型in栈，引用类型in堆
* 减少GC运行次数
* 减少单次GC运行时间
* 将GC的运行时间延迟，避免在关键时候触发，如在场景加载的时候调用GC
* 代码重构，减少堆内存的分配和引用的分配，减少GC的检测个数
* 降低堆内存分配和回收的频率，关键时刻，更少的事件触发GC擦欧总，降低堆内存碎片
## 减少内存垃圾的数量
* 缓存：针对频繁调用函数里的新定义的引用类型，可以定义成员变量将其缓存起来，避免频繁的GC
* 不要在频繁调用的函数Update里进行堆内存分配，可在Start和Awake函数中进行内存分配
* 存储前一帧的变量，仅在这一帧发生改变时调用函数，避免每帧都进行堆内存分配
* 采用计时器，避免每帧调用，很常见啊，主要就是Update的优化
* 清除链表，clear函数，替代反复多次创建分配链表，也很常见，这都是C++常识了
* 对象池，针对大量对象产生和销毁，重复使用objects，例如枪的子弹
## 造成不必要的堆内存分配的因素
* 字符串，引用类型变量，造成内存垃圾，不可变更，字符串加操作会新建一个字符串来存储，造成内存垃圾
* 减少不必要的字符串创建，如被多次使用，创建并缓存
* 减少不必要的字符串操作，如Text组件，不变和经常变的分成两个组件
* 实时创建字符串，采用StringBuilder代替，不需要进行内存分配
* 移除Debug.Log()代码，即使为空，至少创建一个字符（空字符）的字符串，造成内存垃圾
## Unity函数调用
* 调用Unity自带函数或插件函数，都可能产生内存垃圾，缓存，减少调用频率，重构代码
* 函数返回数组，会new一个新的数组，最好采用引用传递
* 迭代器，每个迭代器都会产生一个新的数组
* 缓存一个数组的引用
* GameObject.name,GameObject.tag,将结果存为新的字符串返回，使用GameObject.CompareTag()避免内存垃圾的产生
* 用Input.GetTouch()和Input.touchCount()来代替Input.touches
* 或者用Physics.SphereCastNonAlloc()来代替Physics.SphereCastAll()。
## 装箱
* 值类型转引用类型，比如String.Format()函数需要传入字符串和对象类型参数，如果传入字符串和int类型数据，就会触发装箱操作（int2String）
* 值类型会在堆内存上分配一个System.Object类型的引用来封装该值类型变量，其对应的缓存就会产生内存垃圾
* 尽量避免或移除代码
## 协程
* StartCoroutine()产生少量内存垃圾，生成实体来管理协程，在游戏关键时刻应限制该函数调用，特别是延迟回调的协程
* yield不会产生堆内存分配，但yield带有参数返回，会造成不必要的内存垃圾，返回0触发装箱，可以返回yield return null;
* 不要每次返回new同一个变量，可以缓存
* 用协程管理时间，最好在update函数中保持对时间的记录
* 用协程控制游戏中事件的发生顺序，最好对不同事件有信息通信的方式
## foreach循环
* 会装箱，在堆内存上生产一个System.Object用来实现迭代循环操作，unity5.5解决了这个问题
* 最好改用for或while循环
## 函数引用
* 无论指向匿名函数还是显式函数，都是引用类型变量，在堆内存上进行分配
* 匿名函数调用完成后会增加内存的使用和堆内存的分配
* 具体函数的引用和终止取决于操作平台和编译器设置
## LINQ和常量表达式
* 会装箱
## 重构代码
* 增加GC工作量，检查不必检查的对象
* struct是值类型的变量，如果包含有引用类型的变量，就必须检测整个struct
* 可将struct数组拆分成多个数组
* 保存不必要的Object引用，返回对象框id而不是对象框引用
## 定时执行GC操作
* 主动调用GC操作，System.GC.Collect()，主动回收堆内存
