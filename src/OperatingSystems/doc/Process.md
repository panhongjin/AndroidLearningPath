# 进程管理

## 2.1 进程的基本概念

### 2.1.1 程序的顺序执行及其特征

1. 程序的顺序执行<br>
&emsp;&emsp;仅当前一操作(程序段)执行完后，才能执行后续操作。例如，在进行计算时，总需先输入用户的程序和数据，然后进行计算，最后才能打印计算结果。<br>
- S1: a = x + y;
- S2: b = a - 5;
- S3: c = b + 1;<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/程序的顺序执行.JPG)<br>
&emsp;&emsp;程序独占处理机顺序执行时特征：
- 顺序性： 各程序段严格按照规定的顺序执行。
- 封闭性： 程序运行时机内各资源只受该程序控制而改变，执行结果不受外界因素影响。
- 可再现性： 只要程序执行环境和初始条件相同，程序多次执行，可获得相同结果。

### 2.1.2 前趋图

&emsp;&emsp;前趋图(Precedence Graph)是一个有向无循环图，记为DAG(Directed Acyclic Graph)，用于描述进程之间执行的前后关系。图中的每个结点可用于描述一个程序段或进程，乃至一条语句；结点间的有向边则用于表示两个结点之间存在的偏序(Partial Order)或前趋关系(Precedence Relation)“->”。<br>
&emsp;&emsp;->={(Pi,Pj) | Pi must complete before Pj may start}，如果(Pi,Pj)∈->，可写成Pi->Pj，称Pi是Pj的直接前趋，而称Pj是Pi的直接后继。在前趋图中，把没有前趋的结点成为初始结点(Initial Node)，把没有后继的结点称为终止结点(Final Node)。<br>
&emsp;&emsp;每个结点还具有一个重量(Weight)，用于表示该结点所含有的程序量或结点的执行时间。
- Ii->Ci->Pi和S1->S2->S3<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/前趋图.png)<br>
&emsp;&emsp;对于图a所示的前趋图，存在下述前趋关系：<br>P1->P2, P1->P3, P1->P4, P2->P5, P3->P5, P4->P6, P4->P7, P5->P8, P6->P8, P7->P9, P8->P9。或表示为：<br>P={P1, P2, P3, P4, P5, P6, P7, P8, P9} -> = { (P1, P2), (P1, P3), (P1, P4), (P2, P5), (P3, P5),(P4, P6), (P4, P7),(P5, P8), (P6, P8), (P7, P9), (P8, P9)} <br>
应当注意，前趋图中必须不存在循环，但在图b中却有着下述的前趋关系：<br>S2->S3, S3->S2 

### 2.1.3 程序的并发执行及其特征

1. 程序的并发执行<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/程序并发执行的前趋图.JPG)<br>
在该例中存在下述前趋关系：<br>Ii->Ci，Ii->Ii+1, Ci->Pi, Ci->Ci+1，Pi->Pi+1<br>而Ii+1和Ci及Pi-1是重迭的，亦即在Pi-1和Ci以及Ii+1之间，可以并发执行。对于具有下述四条语句的程序段：
- S1： a = x + 2
- S2： b = y + 4
- S3： c = a + b
- S4： d = c + b<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/四条语句的前趋关系.JPG)<br>
程序并发执行特征：
- 间断性： 程序在并发执行时，由于它们共享资源或为完成同一项任务而相互合作，使在并发程序之间形成了相互制约的关系。相互制约将导致并发程序具有“执行-暂停-执行”这种间断性活动规律。
- 失去封闭性： 程序在并发执行时，是多个程序共享系统中的各种资源，因而这些资源的状态将由多个程序来改变，致使程序的运行已失去了封闭性。
- 不可再现性： 程序在并发执行时，由于失去了封闭性，也将导致失去结果的可再现性。即程序经过多次运行，虽然其各次的环境和初始条件相同，但得到的结果却各不相同。<br>
例：观察者/报告者<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/观察者报告者程序.JPG)<br>
进程的引入
- 为了提高资源利用率，系统采用多道程序设计，程序执行环境由顺序执行变为并发执行。
- 由于程序在并发执行时，可能会造成执行结果的不可再现，所以用“程序”这个概念已无法描述程序的并发执行，所以必须引入新的概念---进程来描述程序的并发执行，并要对进程进行必要的管理，以保证进程在并发执行时结果可再现。

### 2.1.4 进程的特征与状态

1. 进程的特征和定义
- 进程这一术语最早是由麻省理工学院著名的操作系统MULTICS中提出。
- 进程(Process)定义：“可并发执行的程序在一个数据集合上的运行过程”。
- 进程的特征：
1) 动态性： 动态性是进程的最基本特征，它是程序执行过程，它是有一定的生命周期。它由创建而产生、由调度而执行，因得不到资源而暂停，并由撤销而死亡。而程序是静态的，它是存放在介质上一组有序指令的集合，无运动的含义。
2) 并发性： 并发性是进程的重要特征，同时也是OS的重要特征。并发性指多个进程实体同存于内存中，能在一段时间内同时运行。而程序是不能并发执行。
3) 独立性： 进程是一个能独立运行的基本单位，即是一个独立获得资源和独立调度的单位，而程序不作为独立单位参加运行。
4) 异步性： 进程按各自独立的不可预知的速度向前推进，即进程按异步方式进行，正是这一特征，将导致程序执行的不可再现性，因此OS必须采用某种措施来限制各进程推进序列以保证各程序间正常协调运行。
5) 结构特征： 从结构上，进程实体由程序段、数据段和进程控制块三部分组成，UNIX中称为“进程映像”。<br>
较典型的进程定义有：
1) 进程是程序的一次执行。
2) 进程是一个程序及其数据在处理机上顺序执行时所发生的活动。
3) 进程是程序在一个数据集合上运行的过程，它是系统进行资源分配和调度的一个独立单位。<br>
&emsp;&emsp;在引入了进程实体的概念后，我们可以把传统OS中的进程定义为：“进程是进程实体的运行过程，是系统进行资源分配和调度的一个独立单位”。<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/进程.JPG)<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/并发运行示例.JPG)<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/进程的执行轨迹.JPG)<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/进程状态变化.JPG)<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/进程与系统.JPG)<br>

2. 进程的三种基本状态
- 运行态/执行态(Running)：当一个进程在处理机上运行时，则称该进程处于运行态。
- 就绪态(Ready)： 一个进程获得了除处理机外的一切所需资源，一旦得到处理机即可运行，则称此进程处于就绪态。
- 阻塞态(Blocked)： (又称挂起状态、等待状态)：一个进程正在等待某一事件发生(例如请求I/O而等待I/O完成等)而暂时停止运行，这时即使把处理机分配给进程也无法运行，故称该进程处于阻塞状态。<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/进程的三种基本状态及其转换.JPG)<br>
进程状态的转换<br>

三个基本状态之间可能转换和转换的原因如下：
- 就绪态->运行态： 当处理机空闲时，进程调度程序必将处理机分配给一个就绪态的进程，该进程便由就绪态转换为运行态。
- 运行态->阻塞态： 处于运行态的进程在运行过程中需要等待某一事件发生后(例如I/O请求等待I/O完成后)，才能继续运行，则该进程放弃处理机，从运行态转换为阻塞态。
- 阻塞态->就绪态： 处于阻塞态的进程，若其等待的事件已经发生，于是进程由阻塞态转换为就绪态。
- 运行态->就绪态： 处于运行状态的进程在其运行过程中，因分配给它的处理机时间片已用完，而不得不让出(被抢占)处理机，于是进程由运行态转换为就绪态。<br>
而阻塞态->运行态和就绪态->阻塞态这二种状态转换不可能发生。<br>

系统中各进程状态管理<br>
- 处于运行态进程： 如系统有一个处理机，则在任何一时刻，最多只有一个进程处于运行态。
- 处于就绪态进程： 一般处于就绪态的进程按照一定的算法(如先来的进程排在前面，或采用优先权高的进程排在前面)排成一个就绪队列RL。
- 处于阻塞态进程： 处于阻塞态的进程排在阻塞队列也按事件分成几个队列WLi。<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/进程队列.JPG)<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/系统中各进程状态转换影响.JPG)<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/系统中各进程状态转换影响.JPG)<br>
在一个多道程序设计的系统中，各进程状态转换会互相影响。例如系统中一个运行态的进程A发生I/O请求后要等待I/O完成，它的状态也由运行态转换为阻塞态，此时进程调度程序就会按照一定的算法，在就绪队列中选一个进程B，将处理机分给它，该B进程的状态也由就绪态转换为运行态。如一个进程C等待的事件完成，则它的状态由阻塞态转换为就绪态，它也从阻塞队列中抽出插入就绪队列中。如进程C从阻塞态转换为就绪态时，有一个进程D在CPU上运行。而系统采用抢占式调度算法，进程C的优先级又高于正在CPU上运行的进程D的优先级，则要发行抢占调度。即接下去的操作时由进程调度程序将正在运行的进程D由运行态转换为就绪态，插入就绪队列。<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/五状态进程模型.JPG)<br>

3. 挂起状态<br>
1) “挂起”、“激活”操作的引入<br>
&emsp;&emsp;系统管理员有时需要暂停某个进程，以便排除系统故障或暂时减轻系统负荷，用户有时也希望暂停自己的进程以便检查自己作业的中间结果。这就希望系统提供“挂起”操作，暂停进程运行，同时也要提供“激活”的操作，恢复被挂起的进程。由于被挂起前进程的状态有三种，挂起后的进程就分为两种状态：静止就绪态和静止阻塞态(有的称为挂起就绪态和挂起阻塞态)。挂起前的进程就绪态和阻塞态也改为活动就绪态和活动阻塞态。<br>
&emsp;&emsp;当进程处于运行态和活动就绪态时，执行挂起操作，进程状态转换为静止就绪态。当进程处于活动阻塞态时，执行挂起操作，进程状态转换为静止阻塞态。对被挂起的进程施加“激活”操作，则处于静止就绪的进程转换为活动就绪态，处于静止阻塞态的进程转换为活动阻塞态。被挂起的处于静止阻塞态的进程当它等待的事件发生后，它就由静止阻塞态转换为静止就绪态。具有以上五个状态和新增“挂起”、“激活”两个操作的进程状态图见下图所示。<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/五个状态进程状态图.JPG)<br>
2) 进程状态的转换<br>
- 活动就绪->静止就绪 
- 活动阻塞->静止阻塞态
- 静止就绪->活动就绪
- 静止阻塞->活动阻塞

### 2.1.5 进程控制块

1. 进程控制块的作用<br>
&emsp;&emsp;进程控制块的作用是使一个在多道程序环境下不能独立运行的程序(含数据)，成为一个能独立运行的基本单位，一个能与其它进程并发执行的进程。或者说，OS是根据PCB来对并发执行的进程进行控制和管理的。

2. 进程控制块中的信息<br>
1) 进程标记符<br>
&emsp;&emsp;进程标记符用于唯一地标记一个进程。一个进程通常有两种标记符：
- 内部标记符。 在所有的操作系统中，都为每一个进程赋予一个唯一的数字标记符，它通常是一个进程的序号。设置内部标记符主要是为了方便系统使用。
- 外部标记符。 它由创建者提供，通常是由字母、数字组成，往往是由用户(进程)在访问该进程时使用。为了描述进程的家族关系，还应设置父进程标识及子进程标识。<br>
此外，还可以设置用户标识，以指示拥有该进程的用户。<br>

2) 处理机状态<br>
&emsp;&emsp;处理机状态信息主要是由处理机的各种寄存器中的内容组成的。a、通用寄存器，又称为用户可视寄存器，它们是用户程序可以访问的，用于暂存信息，在大多数处理机中，有8~32个通用寄存器，在RISC结构的计算机中可超过100个；b、指令计数器，其中存放了要访问的下一条指令的地址；c、程序状态字PSW，其中含有状态信息，如条件码、执行方式、中断屏蔽标志等；d、用户栈指针，指每个用户进程都有一个或若干个与之相关的系统栈，用于存放过程和系统调用参数及调用地址。栈指针指向该栈的栈顶。

3) 进程调度信息<br>
&emsp;&emsp;在PCB中还存放一些与进程调度和进程对换有关的信息，包括：a、进程状态，指明进程的当前状态，作为进程调度和对换的依据；b、进程优先级，用于描述进程使用处理机的优先级别的一个整数，优先级高的进程应优先获得处理机；c、进程调度所需的其它信息，它们与所采用的进程调度算法有关，比如，进程已等待CPU的时间总和、进程已执行的时间总和等；d、事件，是指进程有执行状态转变为阻塞状态所等待发生的事件，即阻塞原因。<br>

4) 进程控制信息<br>
&emsp;&emsp;进程控制信息包括：a、程序和数据的地址，是指进程的程序和数据所在的内存或外存地(首)址，以便再调度到该进程执行时，能从PCB中找到其程序和数据；b、进程同步和通信机制，指实现进程同步和进程通信时必需的机制，如消息队列指针、信号量等，它们可能全部或部分地方在PCB中；c、资源清单，是一张列出了除CPU以外的、进程所需的全部资源以及已经分配到该进程的资源的清单；d、链接指针，它给出了本进程(PCB)所在队列中的下一个进程的PCB的首地址。

3. 进程控制块的组织方式<br>
- 链接方式<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/PCB链接队列示意图.JPG)<br>
- 索引方式<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/索引方式组织PCB.JPG)<br>


## 2.2 进程控制

### 2.2.1 进程的创建

1. 进程图(Process Graph)<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/进程树.JPG)<br>

2. 引起创建进程的事件<br>
- 用户登录；
- 作业调度；
- 提供服务；
- 应用请求。<br>

3. 进程的创建(Creation of Progress)<br>
- 申请空白PCB；
- 为新进程分配资源；
- 初始化进程控制块；
- 将新进程插入就绪队列，如果进程就绪队列能够接纳新进程，便将新进程插入就绪队列。<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/进程创建原语.JPG)<br>

### 2.2.2 进程的终止

1. 引起进程终止(Termination of Process)的事件<br>
- 正常结束<br>
&emsp;&emsp;在任何计算机系统中，都应有一个用于表示进程已经运行完成的指示。例如，在批处理系统中，通常在程序的最后安排一条Halt指令或终止的系统调用。当程序运行到Halt指令时，将产生一个中断，去通知OS本进程已经完成。在分时系统中，用户可利用Logs off去表示进程运行完毕，此时同样可产生一个中断，去通知OS进程已运行完毕。<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/进程停止原语.JPG)<br>
- 异常结束<br>
&emsp;&emsp;在进程运行期间，由于出现某些错误和故障而迫使进程终止这类异常事件很多，常见的有：a、越界错误。这是指程序所访问的存储区，已越出该进程的区域；b、保护错。进程试图去访问一个不允许访问的资源或文件，或者以不适当的方式进行访问，例如，进程试图去写一个只读文件；c、非法指令。程序试图去执行一条不存在的指令。出现该错误的原因，可能是程序错误地转移到数据区，把数据当成了指令；d、特权指令错。用户进程试图去执行一条只允许OS执行的指令；e、运行超时。进程的执行时间超过了指定的最大值；f、等待超时。进程等待某事件的时间，超过了规定的最大值；g、算术运算错误。进程试图去执行一个被禁止的运算，例如，被0除；h、I/O故障。这是指在I/O过程中发生了错误等。<br>
- 外界干预<br>
&emsp;&emsp;外界干预并非指在本进程运行中出现了异常事件，而是指进程应外界的请求而终止运行。这些干预有：a、操作员或操作系统干预。由于某种原因，例如，发生了死锁，由操作员或操作系统终止该进程；b、父进程请求。由于父进程具有终止自己的任何子孙进程的权利，因而当父进程提出请求时，系统将终止该进程；c、父进程终止。当父进程终止时，OS也将他的所有子孙进程终止。<br>
2. 进程的终止过程<br>
- 根据被终止进程的标记符，从PCB集合中检索出该进程的PCB，从中读出该进程的状态。
- 若被中止进程正处于执行状态，应立即终止该进程的执行，并设置调度标记为真，用于指示该进程被中止后应重新进行调度。
- 若该进程还有子孙进程，还应将其所有子进程予以终止，以防他们成为不可控的进程。
- 将被终止进程所拥有的全部资源，或者归还给其父进程，或者归还给系统。
- 将被中止进程(它的PCB)从所在队列(或链表)中移出，等待其他程序来搜集信息。<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/进程终止原语.JPG)<br>

### 2.2.3 进程的阻塞与唤醒

1. 引起进程阻塞与唤醒的事件
- 请求系统服务；
- 启动某种操作；
- 新数据尚未到达；
- 无新工作可做。<br>

2. 进程阻塞过程<br>
&emsp;&emsp;正在执行的进程，当发现上述某事件时，由于无法继续执行，于是进程便通过调用阻塞原语block把自己阻塞。可见，进程的阻塞是进程自身的一种主动行为。进入block过程后，由于此时改进澄海处于执行状态，所以应先立即停止执行，把进程控制块中的现行状态由“执行”改为阻塞，并将PCB插入阻塞队列，则应将本进程插入到具有相同事件的阻塞(等待)队列。最后，转调度程序进行重新调度，将处理机分配给另一个就绪进程，并进行切换，亦即，保留被阻塞进程的处理机状态(PCB中)，再按新进程的PCB中的处理机状态设置CPU的环境。<br>

3. 进程唤醒过程<br>
&emsp;&emsp;被阻塞进程所期待的事件出现时，如I/O完成或其所期待的数据已经到达，则由有关进程(比如，用完并释放了该I/O设备的进程)调用唤醒原语wakeup()，将等待该事件的进程唤醒。唤醒原语执行的过程是：首先把被阻塞的进程从等待该事件的阻塞队列中移出，将其PCB中的现行状态由阻塞改为就绪，然后在将该PCB插入到就绪队列中。<br>

### 2.2.4 进程的挂起与激活

1. 进程的挂起<br>
&emsp;&emsp;当出现了引起进程挂起事件时，比如，用户进程请求将自己挂起，或父进程请求将自己的某个子进程挂起，系统将利用挂起原语suspend()将指定进程或处于阻塞状态的进程挂起。挂起原语的执行过程是：首先检查被挂起进程的状态，若处于活动的就绪状态，便将其改为静止就绪；对于活动阻塞状态的进程，则将之改为静止阻塞。为了方便用户或父进程考察该进程的运行情况而把该进程的PCB复制到某指定的内存区域。最后，若被挂起的进程正在执行，则转向调度程序重新调度。<br>

2. 进程的激活过程<br>
&emsp;&emsp;当发生激活进程的事件时，例如，父进程或用户进程请求激活指定进程，若该进程驻留在外存而内存中已有足够的空间时，则可将在外存上处于静止就绪状态的进程换入内存。这时，系统将利用激活原语active()将指定进程激活。激活原语先将进程从外存调入内存，检查该进程的现行状态，若是静止就绪，便将之改为活动就绪；若为静止阻塞便将之改为活动阻塞。假如采用的是抢占调度策略，则每当有新进程进入就绪队列时，应检查是否要进行重新调度，即由调度程序将被激活进程与当前进程进行优先级的比较，如果被激活进程的优先级更低，就不必重新调度；否则，立即剥夺当前进程的运行，把处理机分配给刚被激活的进程。<br>


## 2.3 进程同步

### 2.3.1 进程同步的概念

1. 进程间制约关系<br>
&emsp;&emsp;在多道程序环境下，系统中各进程以不可预测的速度向前推进，进程的异步性会造成了结果的不可再现性。为防止这种现象，异步的进程间推进收到二种限制。
- 资源共享关系 <br>
&emsp;&emsp;多进程共享资源，例如各进程争用一台打印机，这时各进程使用者台打印机时有一定的限制。每次只允许一个进程使用一段时间打印机，等该进程使用完毕后再将打印机分配给其他进程。这种使用原则称为互斥使用。<br>
进程之间竞争资源面临三个控制问题：<br>
	- 互斥(mutual exclusion)指多个进程不能同时使用同一个资源；
	- 死锁(deadlock)指多个进程互不相让，都得不到足够的资源；
	- 饥饿(starvation)只一个进程一直得不到资源(其他进程可能轮流占用资源)。
- 相互合作关系<br>
&emsp;&emsp;在某些进程之间还存在合作关系，例如一个程序的输入、计算、打印三个程序段作为三个进程并发执行，由于这三个进程间存在着相互合作的关系，即先输入再计算、最后再打印的关系，所以这三个进程在并发执行时推进序列受到限制，要保证其合作关系正确，进程间这种关系称为同步关系。<br>

2. 临界资源<br>
&emsp;&emsp;像打印机这类资源一次只允许一个进程使用的资源称为临界资源。属于临界资源由硬件打印机、磁带机等，软件在消息缓冲队列、变量、数组、缓冲区等当然还有一类像磁盘等资源，它允许进程间共享，即可交替使用，所以它称为共享资源，而临界资源又称为独享资源。<br>
&emsp;&emsp;生产者-消费者(producer-consumer)问题是一个著名的进程同步问题它描述的是：有一群生产者在生产产品，并将这些产品提供给消费者进程去消费。为使生产者进程与消费者进程能并发执行，在两者之间设置了一个具有n个缓冲区的缓冲池，生产者进程将它所生产的产品放入一个缓冲区中消费者进程可从一个缓冲区中取走产品取消费。尽管所有的生产者进程和消费者进程都是以异步方式运行的，但它们之间必须保持同步，既不允许消费者进程到一个空缓冲区取产品；也不允许生产者进程向一个已装满产品且尚未被取走的缓冲区中投放产品。<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/竞争条件.JPG)<br>
多个进程读写共享数据，最后的结果取决于进程运行的时序；<br>
引起竞争条件的原因<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/引起竞争条件的原因.JPG)<br>

3. 临界区(critical sections)<br>
&emsp;&emsp;多个进程共享临界资源时必须互斥使用，例如二个进程A和B它们的程序如下：<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/临界区程序示例.JPG)<br>
&emsp;&emsp;A和B两个进程都需要使用打印机，它们必须互斥使用。如果为了保证结果的正确性限制A、B二进程推进序列，规定进程A执行好再执行进程B，或进程B执行好再执行进程A，这样的限制就显得过死，因为它已不能保证进程A、B能并发执行，所以必须把限制减少到最少，以尽可能支持并发执行。为此把各进程代码分解，把访问临界资源的那段代码(称为临界区)与其他段代码分割开来，只对各种进程进入自己的临界区加以限制，即各进程互斥地进入自己的临界区。在上述A、B两程序中我们分别把A和B的使用打印机的二段程序print result 1 by printer和print result 2 by printer称为A和B进程使用打印机的临界区A和临界区B，进程A和B必须互斥地分别进入各自的临界区A和B。<br>
临界资源与互斥关系<br>
- 临界资源： 一次只允许一个进程使用的资源。
- 临界区： 一个程序片段的集合，这些程序片段分散在不同的进程中，对某个共享的数据结构进行操作。<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/临界区.JPG)<br>
- 互斥： 不允许两个以上的共享某个资源的并发进程同时进入临界区。

4. 进程同步机制<br>
&emsp;&emsp;进程在并发执行时为了保证结果的可再现性，各进程执行序列必须加以限制以保证互斥地使用临界资源，互相合作完成任务。<br>
&emsp;&emsp;多个相关进程在执行次序上的协调称为进程同步。用于保证多个进程在执行次序上的协调关系的相应机制称为进程同步机制。<br>
&emsp;&emsp;所有的进程同步机制应遵循下述四条原则：
- 空闲让进<br>
&emsp;&emsp;当无进程进入临界区区，相应的临界资源处于空闲状态，因而允许一个请求进入临界区的进程立即进入自己的临界区。<br>
- 忙则等待<br>
&emsp;&emsp;当已有进程进入自己的临界区时，即相应的临界资源正被访问，因而其它试图进入临界区的进程必须等待，以保证进程互斥地访问临界资源。<br>
- 有限等待<br>
&emsp;&emsp; 对要求访问临界资源的进程，应保证进程能在有限时间进入临界区，以免陷入“饥饿”状态。<br>
- 让权等待<br>
&emsp;&emsp;当进程不能进入自己的临界区时，应立即释放处理机，一面进程陷入忙等。<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/进程同步机制.JPG)<br>
- 一个由临界区和剩余区1和剩余区2程序段组成的进程采用进程同步机制后的描述如下：<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/进程同步机制程序示例.JPG)<br>
&emsp;&emsp;进程同步机制在临界区前加上进入区，它负责对于访问的临界资源状态进行检查，以决定是允许该进程进入临界区还是等待。同时在临界区后加上退出区，它负责释放临界资源以便其他等待该临界资源的进程使用。
- 实现进程互斥和同步的信号量机制有软件方法、硬件指令方法、信号量机制和管程等。

### 2.3.2 信号量机制 

1. 整型信号量<br>
&emsp;&emsp;最初由Dijkstra把整型信号量定义为一个整型量，除初始化外，仅能通过两个标准的原子操作(Atomic Operation)wait(S)和signal(S)来访问。这两个操作一直被分别称为P、V操作。wait和signal操作可描述为：<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/整型信号量原语.JPG)<br>

2. 记录型信号量<br>
&emsp;&emsp;在整型信号量机制中的wait操作，只要信号量S<=0，就会不断地测试。因此，该机制并未遵循“让权等待”的准则，而是使进程处于“忙等”的状态记录型信号量机制，则是一种不存在“忙等”现象的进程同步机制。但在采取了“让权等待”的策略后，又会出现多个进程等待访问同一临界资源的情况。为此，在信号量机制中，除了需要一个用于代表资源数目的整型变量value外，还应增加一个进程链表L，用于链接上述的所有等待进程。记录型信号量是由于它采用了记录型的数据结构而得名的。它所包含的上述两个数据项可描述为：<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/记录型信号量原语.JPG)<br>
- 信号量的类型<br>
&emsp;&emsp;信号量按联系进程的关系分成两类：
- 公用信号量(互斥信号量)： 它为一组需互斥共享临界资源的并发进程而设置，它代表永久的共享的临界资源，每个进程均可对它施加P、V操作，即都可申请和释放该临界资源，其初始值置为1。<br>
- 专用信号量(同步信号量)： 它为一组需同步协作完成任务的并发进程而设置，它代表消耗性的专用资源，只有拥有该资源的进程才能对它施加P操作(即可申请资源)，而由其合作进程对它施加V操作(即释放资源)。
- 信号量S取值意义<br>
S.value > 0：表示可供使用资源数；= 0：表示资源已被占用，无其他进程等待；< 0(= -n)：表示资源已被占用，还有n个进程因等待资源而阻塞。<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/信号量保护共享数据示例.JPG)<br>
3. AND型信号量<br>
在两个进程中都要包含两个对Dmutex和Emutex的操作，即<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/AND型信号量.JPG)<br>
&emsp;&emsp;AND同步机制的基本思想是：将进程在整个运行过程中需要的所有资源，一次性全部地分配给进程，待进程使用完后再一起释放。只要尚有一个资源未能分配给进程，其它所有可能为之分配的资源，也不分配给它。亦即，对若干个临界资源的分配，采取原子操作方式：要么全部分配到进程，要么一个也不分配。由死锁理论可知，这样就可避免上述死锁情况的发生。为此，在wait操作中，增加了一个"AND"条件，故称为AND同步，或称为同时wait操作，即Swait(Simultaneous wait)定义如下：<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/AND型信号量原语.JPG)<br>
4. 信号量集<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/信号量集原语.JPG)<br>
&emsp;&emsp;一般“信号量集”的几种特殊情况：
- Swait(S, d, d)。此时在信号量集中只有一个信号量S，但允许它每次申请d个资源，当现有资源数少于d时，不予分配。
- Swait(S， 1, 1)。此时的信号量集已蜕化为一般的记录型信号量(S > 1时)或互斥信号量(S = 1时)。
- Swait(S， 1， 0)。这是一种很特殊且很有用的信号量操作。当S>=1时，允许多个进程进入某特定区；当S变为0后，将阻止任何进程进入特定区。换言之，它相当于一个可控开关。

### 2.3.3 信号量的应用

1. 利用信号量实现进程互斥<br>
利用信号量实现进程互斥的进程可描述如下：<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/利用信号量实现进程互斥原语.JPG)<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/利用信号量实现进程互斥原语1.JPG)<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/利用信号量机制实现进程互斥示例.JPG)<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/利用信号量机制实现进程互斥表.JPG)<br>

2. 利用信号量实现前趋关系<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/利用信号量实现前趋关系.JPG)<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/利用信号量实现前趋关系原语.JPG)<br>

3. 利用信号量机制实现进程同步<br>
&emsp;&emsp;利用信号量能解决进程间的同步问题，这里以下图所示的计算进程C和打印进程P通过缓冲区Buffer传送数据的同步问题为例说明。<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/利用信号量机制实现进程同步示例.JPG)<br>
C和P两进程基本算法如下：<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/计算打印算法原语.JPG)<br>
C和P两进程并发执行，必须在执行序列上遵循以下规则，才能避免错误。<br>
&emsp;&emsp;只有当C进程把数据送入Buffer后，P进程才能从Buffer中取出数据来打印，否则P进程只能等待。
&emsp;&emsp;只有当P进程从Buffer中取走数据后，C进程才能将新计算的数据再存入Buffer，否则C进程也只能等待。<br>
&emsp;&emsp;为了实现进程同步，需采用同步信号量。为了满足第一条同步规则：只有当C进程把数据送入Buffer后，P进程才能从Buffer中取出数据来打印。设置一个同步信号量full，它代表的消耗性的专用资源是缓冲器装满数据，这个资源只是后面动作(Remove from buffer)的进程(P进程)所拥有，P进程在动作前可以申请该资源，对它施加P操作，如条件满足P进程可从Buffer中取数，它的初值为0。而前面动作的进程(P进程的合作进程C)在动作(Add to Buffer)完成后对full信号量施加V操作，即当C进程将数据存入Buffer后，即可释放该资源供P进程再使用。<br>
&emsp;&emsp;实现C和P两进程第一条同步规则的类PASCAL程序：<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/计算打印full信号量程序.JPG)<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/计算打印full信号量程序表.JPG)<br>
&emsp;&emsp;为了满足第二条同步规则：只有当P进程从Buffer中取走数据后，C进程才能将新计算的数据再存入Buffer。设置另一个同步信号量empty，它代表的消耗性的专用资源是缓冲器空，这个资源只是后面动作(Add to buffer)的进程(C进程)所拥有，C进程在动作前可以申请该资源，它的初值为1。而前面动作(Remove from buffer)的进程(C进程的合作进程P)在动作完成后对empty信号量施加V操作，即当P进程从Buffer中取走数据后，即可释放该资源供C进程再使用。<br>
&emsp;&emsp;实现C和P两进程第二条同步规则的类PASCAL程序：<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/计算打印empty信号量程序.JPG)<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/计算打印full和empty信号量程序.JPG)<br>

同步物理意义<br>
&emsp;&emsp;同步也可以理解为：先做动作的进程C在动作完成后对同步信号量施加V操作，代表发送消息；后做动作的进程P在动作前对同步信号量施加P操作，代表测试消息是否到达。<br>


## 2.4 经典进程同步问题

&emsp;&emsp;经典进程同步问题是从进程并发执行中归纳的典型例子，这些问题常用来测试新的进程同步机制可行性。主要的经典同步问题有生产者-消费者、读者-写者问题、哲学家进餐问题等。

### 2.4.1 生产者-消费者问题(producer-consumer Problem)

- 生产者-消费者问题是著名的同步问题，它描述一组生产者(P1, ...... , Pm)向一组消费者(C1, ...... , Cq)提供消息。它们共享一个有界缓冲池(bounded buffer pool)，生产者向其中投放消息，消费者从中取得消息，如下图所示。生产者-消费者问题是许多相互合作进程的一种抽象。<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/生产者-消费者问题.JPG)<br>
&emsp;&emsp;假定缓冲池中有n个缓冲区，每个缓冲区存放一个消息。由于缓冲池是临界资源，它只允许一个生产者投入消息，或者一个消费者从中取出消息。即生产者之间、生产者与消费者之间、消费者之间都必须互斥使用缓冲池。所以必须设置互斥信号量mutex，它代表缓冲池资源，它的数值为1。<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/生产者-消费者问题原语.JPG)<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/生产者-消费者问题原语1.JPG)<br>
&emsp;&emsp;与计算打印两进程同步关系相同，生产者和消费者二类进程P和C之间应满足下列二个同步条件：<br>
- 只有在缓冲池中至少有一个缓冲区已存入消息后，消费者才能从中提取消息，否则消费者必须等待。
- 只有缓冲池中至少有一个缓冲区是空时，生产者才能把消息放入缓冲区，否则生产者必须等待。<br>
&emsp;&emsp;为了满足第一条同步规则：只有在缓冲池中至少有一个缓冲区已存入消息后，消费者才能从中提取消息。设置一个同步信号量full，它代表的消耗性的专用资源是缓冲器装满数据，这个资源只是后面动作的进程(消费者进程C)所拥有，C进程在动作前可以申请该资源，对它施加P操作，如条件满足C进程可从Buffer中取数，它的初值为0。而前面动作的进程(C进程的合作进程生产者进程P)在动作完成后对full信号量施加V操作，即当P进程将数据存入Buffer后，即可释放该资源供消费者进程C再使用。<br>
&emsp;&emsp;为了满足第一条同步规则：只有缓冲池中至少有一个缓冲区是空时，生产者才能把消息放入缓冲区。设置另一个同步信号量empty，它代表的消耗性的专用资源是缓冲器空，这个资源只是后面动作的进程(生产者进程P)所拥有，P进程在动作前可申请该资源，对它施加P操作，如条件满足进程P可以申请该资源，它的初值为n。而前面动作的进程(生产者进程P的合作进程消费者进程C)在动作完成后对empty信号量施加V操作，即当消费者进程C从Buffer中取走数据后，即可释放该资源供生产者进程P再使用。<br>
&emsp;&emsp;用类并行PASCAL语言和信号量机制解生产者-消费者问题程序：<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/生产者-消费者问题程序.JPG)<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/生产者-消费者问题程序1.JPG)<br>
进程同步的分析：<br>
&emsp;&emsp;用信号量机制解决进程同步问题需在程序中正确设置信号量和在适当位置安排P、V操作。例如生产者-消费者问题中，用于互斥进入临界区的P(mutex)、V(mutex)语句需紧靠着临界区前和后，而生产者用于同步的P(empty)和V(full)(消费者为P(full)和V(empty))语句相对临界区就在外面一层，格式如上程序所述。如在以上问题中生产者的P(empty)和P(mutex)先后位置对调，相应消费者的P(full)和P(mutex)先后位置也对调，则生产者和消费者在并发执行时，在某个执行序列会出现问题。<br>
&emsp;&emsp;下面介绍如何寻找会发生问题的那个执行序列，分析方法用下表表示。表左边是生产者和消费者二进程推进序列，此推进序列需用户寻找；表中间是该操作执行后信号量的变化值，表中间信号量有mutex、empty和full；表的右边是该操作执行后引起进程PCB的队列变化。表中队列有就绪队列RL，因分别等待mutex、empty、full信号量而阻塞的队列QmL、QeL和QfL。<br>
- 在表中已找到一个推进序列。该序列先执行消费者进程，由于消费者进程交换了P操作，消费者进程在先后执行P(mutex)和P(full)后阻塞在等待full信号量的队列中。这时只能执行生产者进程，由于生产者进程也交换了P操作，在生产者进程执行了P(mutex)操作后，生产者进程阻塞在等待mutex信号量的队列中。这样生产者和消费者两进程可以向前推进，系统进入死锁状态。这说明在生产者-消费者问题中对同步信号量和互斥信号量的二个P操作的顺序是不能颠倒，而对互斥信号量和同步信号量的二个V操作的顺序交换影响不大。
- 生产者、消费者交换P操作后发生问题的那个执行序列。<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/生产者-消费者问题程序表.JPG)<br>
2. 利用AND信号量解决生产者-消费者问题<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/生产者-消费者问题程序表.JPG)<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/生产者-消费者问题AND信号量程序.JPG)<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/生产者-消费者问题AND信号量程序1.JPG)<br>

2. 阅览室管理问题<br>
阅览室管理问题描述：<br>
- 阅览室有一批座位，可以坐下若干个同学自习；有一张唯一的登记表，记录空座位数目。
- 某一同学入室时，查看登记表；如果空座位数目>0，则座位数减1，并进入阅览室做下自习；否则，在阅览室门口等待。
- 当从阅览室中出来时，使登记表中空座位数加1。<br>
阅览室管理问题举例：<br>
- 阅览室中有50个座位(即最多可坐下50位同学自习)，只有一张登记表，欲进入阅览室的同学需先在登记表上登记，然后进入阅览室自习；出门时注销登记。<br>
进入与出门关系<br>
- 互斥公用信号量mutex：初值为1，实现临界资源(登记表)互斥使用。
- 空座位信号量seat：初值为50，只是空座位数目。<br>
采用信号量的阅览室管理进程描述：<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/阅览室管理问题原语.JPG)<br>

### 2.4.2 哲学家进餐问题 

1. 利用记录型信号量解决哲学家进餐问题<br>
&emsp;&emsp;经分析可知，放在桌子上的筷子是临界资源，在一段时间内只允许一位哲学家使用。为了实现对筷子的互斥使用，可以用一个信号量表是一只筷子，由这五个信号量构成信号量数组。其描述如下：<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/哲学家进餐问题信号量原语.JPG)<br>
所有信号量均被初始化为1，第i位哲学家的活动可描述为：<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/哲学家进餐问题活动原语.JPG)<br>
&emsp;&emsp;可采取以下几种解决方法：<br>
- 至多只允许有4位哲学家同时去拿左边的筷子，最终能保证至少有一位哲学家能够进餐，并在用毕时能释放出他用过的两只筷子，从而使更多的哲学家能够进餐。
- 仅当哲学家的左、右两只筷子均可用时，才允许他拿起筷子进餐。
- 规定奇数号哲学家先拿他左边的筷子，然后再去拿右边的筷子；而偶数号哲学家则相反。按此规定，将是1、2号哲学家竞争1号筷子；3、4号哲学家竞争3号筷子。即五位哲学家都先竞争奇数号筷子，获得后，再去竞争偶数号筷子，最后总会有一位哲学家能获得两只筷子而进餐。<br>

2. 利用AND信号量机制解决哲学家进餐问题<br>
&emsp;&emsp;在哲学家进餐问题中，要求每个哲学家先获得两个临界资源(筷子)后方能进餐，这在本质上就是前面所介绍的AND同步问题，故用AND信号量机制可获得简洁的解法。<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/哲学家进餐问题AND信号量原语.JPG)<br>

### 2.4.3 读者-写者问题

1. 利用记录型信号量解决读者-写者问题<br>
&emsp;&emsp;





































































































































































 
