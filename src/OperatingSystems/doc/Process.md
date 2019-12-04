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
- 净值阻塞->活动阻塞

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
1) 链接方式<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/PCB链接队列示意图.JPG)<br>
2) 索引方式<br>
![Image text](https://github.com/panhongjin/AndroidLearningPath/raw/master/src/OperatingSystems/assets/索引方式组织PCB.JPG)<br>


## 2.2 进程控制

### 2.2.1 进程的创建


































































































































































 