Overview
========

<a name="what?"></a>

What is kdb+?
-------------

kdb+ 是由于 kx System 开发的市场领先的时序数据库。kdb+ 被金融服务各部门广泛
应用于获取、处理、分析每天的数十亿的数据。据 kx 公司统计，几乎所有的顶级投
行都是他们的客户。kdb+ 内置了一中因其高性能和表现力而闻名的编程语言，q语言
。鉴于kdb+卓越的数据管理和分析能力，kdb+技术的适用范围已超出金融领域，扩展
到任何需要对大型数据集进行快速预构建或临时分析的行业。其他广泛应用kdb+的行
业主要包括程序、制药、电信、制造、零售以及任何使用遥测或传感器数据的行业。

<a name="TorQ?"></a>

What is AquaQ TorQ?
-------------------
AquaQ ToRQ是一个通过实现和包含了一系列核心功能和实用工具，来构建产品级别kdb+
系统基础的框架，它使得开发人员能专注于应用的业务逻辑。我们尽可能的将好的应
用实践纳入框架之中，并且重点关注性能、进程管理、信息诊断、可维护性和可扩展
性等方面。我们尽可能的通过描述性注释、错误消息、函数名和变量名来保证代码的
可读性。在可能的情况下，我们会尽可能的避免重复的造轮子，而是直接或间接的使
用code.kx.com上的代码。文档中所有来自code.kx.com的代码都会使用引用进行标注。

AquaQ TorQ可以根据需要进行扩展或修改。我们在框架中使用了一些默认的代码习惯，
但实际TorQ所有的代码都可以被重写。AquaQ TorQ具有以下一些特性：

-   进程管理：每个进程都将被赋予一个类型和名称，并且可以可选地给定一个父类型。
    默认情况下，这些参数用于确定进程所的代码加载、配置加载、日志文件命名以及
    它是如何向发现服务进行汇报。无论何时我们尽可能地确保了所有默认代码行为都
    可以在进程类型级别上进行重写，以及在更进一步的进程名级别上进行重写。
    
-   代码管理：进程可以可选地加载公共基础代码的或与进程类型/名称关联的特定代码
    库。所有代码的加载过程中的错误都将被捕获。

-   配置管理：配置脚本可以通过标准方式加载，同时通过指定进程类型/名字来加载特
    定的配置并重写默认值。配置脚本将会以特定的顺序进行加载：默认配置<父进程类
    型的特定配置（可选的）<进程类型的特定配置<进程名的特定配置。配置文件中最
    后加载的值将重写先前加载的值。

-   日志记录：所有进程使用都将记录到单独的文本日志文件中，并进行周期性地滚动。
    日志记录内容包括打开/关闭连接、同步/异步查询以及在定时器执行函数。日志记录
    值包括请求、请求从何而来的详细信息、请求抵达时间、请求花费时间、请求前后的
    内存使用情况、请求结果数据集大小、请求状态以及任意的错误信息。

-   输入和输出连接管理：输入（客户端）和输出（服务器）连接都将被储存，并通过查
    询计数和总数据大小计数来监视它们的使用。连接被存储和检索，并且可以根据需要
    设置成自动重新开启。输出连接使用的密码可以在默认配置、父进程类型（可选）
    、进程类型、进程名称级别进行配置。

-   访问控制：提供基本的访问控制，并且支持扩展。限制包括了支持远程连接的IP地址、
    可以访问进程的用户以及每个用户可以执行的函数。访问控制使用的是和配置管理而
    类似的分层方法。

-   定时器扩展：允许为重复执行或单次执行的Timer定时器添加多个函数。为重复执行的
    Timer定时器提供了多个重新调度的算法。

-   标准的输出/错误日志记录：为标准输出和错误输出提供格式化消息打印的函数。为
    扩展输出提供必要的hooks，例如将日志发布到集中的日志数据库。标准输出和错误
    输出都将被重定向到恰当命名的、具有时间戳的和别名化的日志文件，这些日志文件
    都将进行周期性地滚动记录。
    
-   错误处理：在代码加载失败时提供不同的失败（处理）选项；包括失败时退出，停止
    在故障点或捕获异常并继续执行。  

-   可视化：提供基于websockets和hyml5，用于简化GUI开发的实用工具。

-   文档和开发工具：AquaQ TorQ内置了(查看)系统文档的功能，用户可以直接通过任意
    Q会话进行访问。开发人员可以在增加了新功能时扩展文档。工具提供了基于名称和
    定义来搜索函数和变量的功能，并对变量通基于内存使用来进行排序。文档中也包括
    了来自code.kx的标准帮助。

-   公用事业：我们打算尽可能的建立和添加那些我们发现的适用和通用的实用工具。截
    至目前，我们已经有了以下一些功能：

        1.  缓存：允许声明一个结果集缓存，将结果集存储起来并支持从缓存中进行检索。
            适用于底层数据在短期内不发生变化，并且需要使用相同参数重复运行多次
            的函数。

        2.  时区处理：源自code.kx，允许不同时区的时间戳进行相互转换。

        3.  电子邮件：一个邮件发送的库。

        4.  异步消息：允许轻松的使用高级的异步消息传递方法，例如实现一个延迟的
            同步通信或异步回调功能。

        5.  心跳：每个进程都可以设置发布心跳，并且订阅和管理来自环境中其他进
            程的心跳。

        6.  数据加载：对系统函数.Q.fsn进行了封装，用来从磁盘读取数据文件、操作
            并将其写入chunks中。

        7.  订阅：允许进程动态地检测和订阅数据源。

        8.  Tickerplant日志文件恢复：从损坏的日志文件中恢复尽可能多的消息。

        9.  数据库写入：提供用于在磁盘数据库中进行写入、排序和分离的实用函数。

        10. 压缩：允许对数据库进行压缩。可以使用一组参数对整个数据库来执行压缩，
            同时，如果需要的话，用户可以针对指定的表或列使用特定的参数来进行灵
            活的压缩。当然，我们还提配套的供了相应的解压函数。

AquaQ TorQ将围绕kdb+tick进行轻松的封装，因此其很容易应用于任何的tickerplant, RDB,
 HDB 或 real time应用程序。我们目前提供了几个自制的进程：

AquaQ TorQ will wrap easily around kdb+tick and therefore around any
tickerplant, RDB, HDB or real time processing application. We currently
have several customised processes of our own:

-   发现服务：每个进程都有一个类型、名称和一组可用属性，这些内容将会被其他进程
    用来连接到它。发现服务是一个可以用来寻找其他可用的进程的中心点。当新进程变
    为可用时，客户端进程可以通过订阅来自发现服务的更新来获取通知--发现服务将通
    知其订阅者，订阅者从而可以使用提供的各种hook来实现所需的行为，例如连接到这
    个新的可用的进程。

-   网关：提供一个完整的同步和异步网关。

    网关将会根据一个定义好的进程类型列表（可以是同构的或异构的进程）来进行连接，
    并根据接收到的请求的优先级在它们之间进行路由查询。路由算法可以很方便的进行
    地修改，例如对用户X给予优先权，或者只对存在于同一数据中心或物理区域中的进
    程进行路由查询，以避免WAN（这将需要使用进程的属性，WAN：广域网）。网关可以
    将句柄作为结果返回到订阅了的客户端，或者将其封装在客户端回调函数中供其调用。

-   实时数据库（RDB）：提供了一个定制版本的kdb+tick RDB，允许动态的进行tickerp
    lant订阅，支持使用经验证过的连接来重新加载多个HDBs，以及支持自定义的每日盘
    后数据落地储存。RDB、WDB和tickerplant日志重播共享一份共同的代码基础，以确保
    每个进程针对同一个表的修改都被保存。

-   录入数据库（WDB）：WDB的任务是将数据写入磁盘，而不是为客户端提供查询服务。
    WDBs通常在一天中周期性地输出数据，他们常适用于需要在内存中存放大量的数据或需
    要在每日盘后加速存储操作。这部分内容是基于[w.q](http://code.kx.com/wiki/
    Cookbook/w.q)建立的。

-   Tickerplant日志重播：一个通过重放Tickerplant日志文件来创建磁盘数据集的进程。
    扩展的特性仅用于重播日志文件的子集（通过消息号和/或表名）、重放块、调用定制
    的最终行为等；

-   Tickerplant Log Replay: A process for replaying tickerplant log
    files to create on-disk data sets. Extended features are provided
    for only replaying subsets of log files (by message number and/or
    table name), replaying in chunks, invoking bespoke final behaviour
    etc.;

-   记录器：这个进程将定期的在特定的数据库或网关上运行定义好的报告（Q查询或参
    数化的函数）。运行的结果被检索和处理。处理过程可以是用户自定义的，也可以是
    标准化的操作，例如将数据写入磁盘，或者将结果发送到指定的列表收件人。这对于
    运行系统检查或生成管理报告是相当有用的。

-   事务管理：一个周期性地进行事务管理的进程，例如压缩和删除不再使用的文件。事
    务管理会查找一个指令文件，并在相应目录上执行的维护任务。事务管理进程支持根
    据文件的年龄选择性地进行删除和压缩，支持根据字符串参数搜索以及从搜索结果中
    排除指定项。事务管理进程可以被调度，或者直接从命令行运行，并且可以根据需要
    扩展以合并更多的任务。

-   文件警报：一个周期性扫描一组目录的进程，并根据文件的可用性执行特定的函数。
    这对于文件可能在到达系统，并且必须被执行是相当有用的（例如，文件被用户/客
    户端上传到共享目录）。执行的功能由用户定义，并且整个过程由一个CSV文件驱动，
    该文件详细描述了需要搜索的文件、需要执行的函数，以及可以指定一个文件在被处
    理后将会被移动到的目录。

-   监控器：一个基本的监控进程，它通过发现服务来定位系统内的其他进程，监听心跳
    并订阅日志消息。进程提供能了一个基本的系统健康检查中心，也可以根据需要进行
    扩展，

-   进程关闭：用于关闭其他进程的进程，可以通过发现服务来定位需关闭的进程。


<a name="Large?"></a>

A Large Scale Data Processing Platform
--------------------------------------

驱动TorQ开发背后的关键因素之一就是，确保能获取到所有建立大数据处理平台必要的工
具。[kdb+tick](http://code.kx.com/wsvn/code/kx/kdb+tick)提供了构建的基础模块，
一个标准的平台配置通常看起来会像这样：

![Simple kdb+tick Setup](graphics/simpletickdiagram.png)

然而，事实上，它通常会更复杂。一个拥有大规模的架构，从多个数据源接收数据，为大
量的客户端提供查询支持的大数据平台可能是这样的：

![Production Data Capture](graphics/fullarchitecture.png)

一种常见的做法是使用网关（section gateway）来管理跨后端进程的客户端查询。网关
可以实现进程的负载均衡，并是故障对客户端保持透明。如果客户端通过异步调用访问网
关，那么网关可以支持同时接收多个请求，并实现额外的客户端排队算法。

其他常见的产品特征包括：

-   提供一个修改版本的RDB（section sec:rdb），它会在每天结束时执行不同的操作，
    例如重载多个HDB进程等。

-   一个录入据库(section \[sec:wdb\])，它接收来自tickerplant的数据，并周期性地将
    其写入磁盘。WDBs常适用于需要在内存中存放大量的数据或需要在每日盘后加速存储操
    作的场景。

-   将其他来源的数据直接加载到HDB中，或者通过tickerplant (section \[sec:dataloa
    der\])潜在地向RDB加载数据的进程。数据可能在那些必须被监控的特定的位置被丢弃
    (section \[sec:filealerter\])

-   一个周期性运行的报告引擎(section \[sec:reporter\])，并针对结果进行一些处理
    （例如从数据库生成xls文件，并将其以email的方式发送给高级管理人员）。报告引
    擎也可以用来定期地检测系统。

-   发现服务(section \[sec:discovery\])使得进程可以相互定位，允许进程动态地注册
    其可用性并向系统周围推送通知。

-   进程可用性的基本监控(section \[sec:monitor\])。

-   事务管理(section \[sec:housekeeping\])，用以确保日志文件被整理，tickerplant的
    日志文件被及时的进行压缩或移动。

<a name="Summary?"></a>

Do I Really Have to Read This Whole Document?
-----------------------------------------------

通常不需要。 AquaQ TorQ的核心是一个名为torq.q的脚本，我们已经尽可能的对它的内容
进行了描述，所以获取这已经足够了。首先要查看的地方是配置文件，主要的一个是
\$KDBCONFIG/settings/default.q。文件里面包含大量可修改的信息。此外：

-   我们添加了一个查看使用信息的说明:

        aquaq$ q torq.q -usage
        KDB+ 3.1 2013.10.08 Copyright (C) 1993-2013 Kx Systems

        General:
         This script should form the basis of a production kdb+ environment.
         It can be sourced from other files if required, or used as a launch script before loading other files/directories using either -load or -loaddir flags 
        ... etc ...

    If sourcing from another script there are hooks to modify and extend
    the usage information as required.

-   我们提供了一个扩展的日志记录:

        aquaq$ q torq.q -p 9999 -debug
        KDB+ 3.1 2013.10.08 Copyright (C) 1993-2013 Kx Systems

        2013.11.05D12:22:42.597500000|aquaq|torq.q_3139_9999|INF|init|trap mode (initialisation errors will be caught and thrown, rather than causing an exit) is set to 0
        2013.11.05D12:22:42.597545000|aquaq|torq.q_3139_9999|INF|init|stop mode (initialisation errors cause the process loading to stop) is set to 0
        2013.11.05D12:22:42.597810000|aquaq|torq.q_3139_9999|INF|init|attempting to read required process parameters proctype,procname from file /torqhome/config/process.csv
        2013.11.05D12:22:42.598081000|aquaq|torq.q_3139_9999|INF|init|read in process parameters of proctype=hdb; procname=hdb1
        2013.11.05D12:22:42.598950000|aquaq|hdb1|INF|fileload|config file /torqhome/config/default.q found
        ... etc ...

-   我们提供了一个用来来查找会话中定义的函数或变量，以及搜索函数定义的功能。

        q).api.f`max                                                                                                                                                                                   
        name                | vartype   namespace public descrip              ..
        --------------------| ------------------------------------------------..
        maxs                | function  .q        1      ""                   ..
        mmax                | function  .q        1      ""                   ..
        .clients.MAXIDLE    | variable  .clients  0      ""                   ..
        .access.MAXSIZE     | variable  .access   0      ""                   ..
        .cache.maxsize      | variable  .cache    1      "The maximum size in ..
        .cache.maxindividual| variable  .cache    1      "The maximum size in ..
        max                 | primitive           1      ""                   ..

        q)first 0!.api.p`.api                                                                                                                                                                          
        name     | `.api.f
        vartype  | `function
        namespace| `.api
        public   | 1b
        descrip  | "Find a function/variable/table/view in the current process"
        params   | "[string:search string]"
        return   | "table of matching elements"

        q).api.p`.api                                                                                                                                                                                  
        name        | vartype  namespace public descrip                       ..
        ------------| --------------------------------------------------------..
        .api.f      | function .api      1      "Find a function/variable/tabl..
        .api.p      | function .api      1      "Find a public function/variab..
        .api.u      | function .api      1      "Find a non-standard q public ..
        .api.s      | function .api      1      "Search all function definitio..
        .api.find   | function .api      1      "Generic method for finding fu..
        .api.search | function .api      1      "Generic method for searching ..
        .api.add    | function .api      1      "Add a function to the api des..
        .api.fullapi| function .api      1      "Return the full function api ..

-   我们在框架中加入了一个帮助文件： help.q.

        q)help`                                                                                                                                                                                        
        adverb    | adverbs/operators
        attributes| data attributes
        cmdline   | command line parameters
        data      | data types
        define    | assign, define, control and debug
        dotz      | .z locale contents
        errors    | error messages
        save      | save/load tables
        syscmd    | system commands
        temporal  | temporal - date & time casts
        verbs     | verbs/functions

-   我们给配置文件全部单独的加上了注释信息:

        aquaq$ head config/default.q 
        /- Default configuration - loaded by all processes

        /- Process initialisation
        \d .proc
        loadcommoncode:1b	/- whether to load the common code defined at
                            	/- ${KDBCODE}/common
        loadprocesscode:0b	/- whether to load the process specific code defined at 
                            	/- ${KDBCODE}/{process type} 
        loadnamecode:0b		/- whether to load the name specific code defined at 
                        	/- ${KDBCODE}/{name of process}
        loadhandlers:1b		/- whether to load the message handler code defined at 
                            	/- ${KDBCODE}/handlers
        logroll:1b		/- whether to roll the std out/err logs daily
        ... etc ...

<a name="OS"></a>

Operating System and kdb+ Version
----------------------------------

AquaQ TorQ已经在Linux和OSX操作系统上进行过编译和测试，据我们所知已经没有什么
能使它与Solaris或Windows不兼容了。我们使用的是kdB+ 3.1和2.8版本进行的测试。
如果您发现存在与其他kdb＋版本或操作系统不兼容的问题，请联系我们。

<a name="License"></a>

License
-------

代码发布遵循MIT协议。
