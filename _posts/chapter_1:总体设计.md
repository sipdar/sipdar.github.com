



##Chapter 1:总体设计


###1.1 结构


####1.1.1 通讯示意图



下面这张图展示了 **sip** 的消息从前到后是怎样在 **pjsip** 的结构中传递的。


####1.1.2 层级结构示意图


###1.2终端（endpoint）

**SIP**的核心就是sip的终端,它被定义为难懂的一个类型 **pjsip_endpoint**。终端有着如下的属性跟作
用：

*	它有一个内存池工厂(pool factory)，为所有的**SIP**组件分配内存池
*	它有一个计时器堆(timer heap)的对象，为所有的**SIP**组件分配定时器(timer)
*	它有一个传输管理的对象（transport manager）， transport manager管理者**SIP**的所有传输
和控制信息的的解析与打印
*	它拥有一个 **PJLIB iOqueue**的单例，**iOQueue**管理着所有的网络事件
*	它提供一个线程安全的检测函数，应用的每个线程都可以检查**timer**跟**socekt**事件。（PJSIP 自己本身并不创建任何线程）
*	它管理着**PJSIP**组件，**PJSIP**的组件最初的意思是扩展的消息的解析跟传递。
*	它从**transport manager**接受发送过来的**SIP**消息然后分发这些消息到各个组件。

下面会讨论一些基础的功能，其他的部分在下一章讨论。

####1.2.1 内存池的创建跟销毁

所有的**SIP**组件的内存分配都是通过**ednPoint**进行的，保证了线程的安全，和对应用层跑出的警告的一致性。一个例子就是可以用作高速缓存，没有用的内存池可以一直保持着直到对象的销毁。
**endPoint**提供两个函数创建跟释放内存池

* pjsip_endpt_create_pool()
* pjsip_endpt_release_pool()

当**endPoint**创建时(**pjsip_endpt_create()**),应用必须制定一个**pool factory**给**endpoint** 使用。 **endpoint**在它的生命周期内保持着这个**pool factory**，并使用它来创建销毁内存池。

####1.2.2 定时器的管理

**endpoint**用一个定时器的堆来管理定时器。并且所有的定时器的创建跟运行都是通过**endpoint**。
**endpoint**提供如下的两个函数来管理定时器。

* pjsip_endpt_schedule_timer()
* pjsip_endpt_cancel_timer()

当**endpoint**的检测函数执行的时候会检测**timer**是否已经终止了。

####1.2.3 检测存储栈

**endpoint**提供一个函数叫做**( pjsip_endpt_handle_events() )**检测**timer**的执行跟网络事件。应用可以指定检测每个事件的等待的时间。
**PJSIP stack** 不会创建线程，所有的操作都是运行在程序创建的线程。无论是 **API**的调用还是程序调用的检测函数。

查询函数都对等待时间进行了基于定时器内容的优化。例如，如果它知道一个定时器将在接下来的5秒内触发，它就不会在等待网络事件上超过5秒，定时器的精确性是跟平台无关了。


###1.3 线程的安全和并发

####1.3.1 线程安全

线程安全是一个很复杂的事儿。幸运的是下面的设计原则贯穿这整个体系。

> 对象必须是线程安全的,但是数据结构体必须是非线程安全的 

对象跟数据结构体之间的区别并不是特别清晰，下面的例子也许会给你一些思路。

例如数据结构体是：

* **PJLIB's** 的数据结构体。例如list，array，hash table，string，内存池。
* **SIP**消息元素，例如 **URIs**， **header field**和 **SIP** messages.

这些结构体并不是线程安全的，这些结构体的线程安全由拥有它们的对象来保证。如果要创建线程安全的结构体，
将非常影响性能的需要消耗大量的系统资源。

作为对比**SIP**对象必须是线程安全的，例如

*	**PJLIB**对象， 像**ioqueue**
*	**PJSIP**对象，例如 **endpoint**, **transactions**, **dialogs**, **dialog usages**, etc.

####1.3.2 并发

一些对象的定义在头文件中，例如 **pjsip_transaction**  **pjsip_dialog**尽管从接口上看这些对象是线程安全的，但是程序在访问这些数据之前还是要使用适当的锁。例如在多线程访问中使用互斥锁** pj_mutex_lock()**。
更糟糕的是 会话使用的锁是不同的接口，替代**pj_mutex_lock()**和**pj_mutex_unlock()**程序需要调用**pjsip_dlg_inc_lock()**和 **pjsip_dlg_dec_lock()** 他们之间非常相似，会话的 **inc/dec** 锁能保证会话在函数调用的时候不会被释放，**pj_mutex_unlock()**则会因为会话的释放而导致崩溃。

	1 pj_mutex_lock(dlg->mutex);	2 pjsip_dlg_end_session(dlg, ...); 	3 pj_mutex_unlock(dlg->mutex);

在上面的例子中，第三行代码可能会导致崩溃，因为**pjsip_dlg_end_session()**在某些情况下可能会被释放，	例如在最开始发送**INVITE**的时候可能收不到任何的响应，导致这次的请求会被立即释放，导致会话也就会被立即释放，会话的 inc/dec 锁可以通过增加会话的引用计数来阻止这个问题，会话会在**dec_lock()**调用之后释放，

	1 pjsip_dlg_inc_lock(dlg);	2 pjsip_dlg_end_session(dlg, ...); 	3 pjsip_dlg_dec_lock(dlg);
最后，锁的调用顺序一定要正确，以防止死锁的发生。

####1.3.3 替换
##Chapter 2:模块
**Module framework**在**PJSIP**应用中描述**SIP**消息重要的部分，所有的**sip**组成中包括传输层跟会话层，都是**mdoule**的实现，在**module**之外，
####2.1.1 模块声明
模块的声明定义在 **<pjsip/sip_module.h>** 中：

	struct pjsip_module 
	{   		PJ_DECL_LIST_MEMBER(struct pjsip_module);      // For internal list mgmt.		pj_str_t name; 							       // Module name.		int id; 				                       // Module ID, set by endpt		int priority;						           // Priority		pj_status_t (*load) (pjsip_endpoint *endpt);   // Called to load the mod.		pj_status_t (*start) 	(void);	               // Called to start.		pj_status_t	(*stop)		(void);		           // Called top stop.		pj_status_t (*unload)	(void);			       // Called before unload		pj_bool_t (*on_rx_request) (pjsip_rx_data *rdata);      // Called on rx request		pj_bool_t (*on_rx_response) (pjsip_rx_data *rdata); 	// Called on rx response		pj_status_t (*on_tx_request) (pjsip_tx_data *tdata);	// Called on tx request		pj_status_t (*on_tx_response)(pjsip_tx_data *tdata); 	// Called on tx request		void (*on_tsx_state) (pjsip_transaction *tsx,  			// Called on transaction							pjsip_event *event); 				// state changed	}; 
####2.1.2 模块优先级
































 


