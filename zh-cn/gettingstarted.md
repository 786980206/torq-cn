
Getting Started
===============

kdb+是高度可定制的，用于控制进程行为的函数和变量一般定义在q脚本（.q文件）中。
每个q进程都可以加载一个单独的q脚本，或者一个包含q脚本或q数据文件的目录。kdb+
提供了钩子好使得程序员能够将自定义函数应用到进程的每个入口点（.z.p\*）上，以
便在定时器（.z.t）中，或者当在顶级命名空间中的变量被修改时（.z.vs）被调用。
默认情况下，这些钩子都没有实现。

我们提供了一个具有基础代码的单独的主脚本，torq.q。torq.q可以看作是一个定制功
能的封装器，它可以用来加载其他的脚本或目录，也可以从其他脚本中进行加载。
torq.q应该尽可能的直接调用，并用于根据需要加载其他脚本。torq.q能够：

-   确保环境变量设置正确;

-   定义一些公共的功能函数（例如日志记录）;

-   执行进程管理任务，例如发布进程的名称和类型，并且将输出重定向到日志文件;

-   加载配置项;

-   加载公共代码;

-   设置消息句柄;

-   加载任意必要的定制脚本.

torq.q的行为可以通过命令行参数和或配置项来修改。尽管我们尽可能多地保留配置
到配置文件中了，但是如果参数对进程有全局影响，或者我们需要在读取配置之前知
道参数，那么就必须通过命令行参数的方式来实现。

<a name="torq"></a>

Using torq.q
------------

torq.q文件可以直接在命令行中被调用并将其设置为源指定的文件或目录。torq.q需要
设置5个环境变量（参见envvar一节）。如果使用的是UNIX环境，可以执行setenv.sh来
实现环境配置。你不用修改任何配置文件（例如process.csv），只需要在参数中指定
进程的类型和名称，就可以在当前窗口中启动一个进程。下面是一个示例。

    $ . setenv.sh
    $ q torq.q -debug -proctype testproc -procname test1 

如果需要指定父进程类型，你可这样做:

    $ q torq.q -debug -parentproctype testparentproc -proctype testproc -procname test1

如果需要加载一个文件，你可以这样做:

    $ q torq.q -load myfile.q -debug -proctype testproc -procname test1
    
同样可以通过另外的脚本来引用加载。如果是这种情况，脚本中一些变量将会被覆盖，并且
使用信息可以被修改或扩展。任何一个像下面这样定义的变量都可以通过加载脚本进行覆盖。

    myvar:@[value;`myvar;1 2 3]

可用的命令行参数如下所示：

  |Cmd Line Param|            Description|
  |-------------------------| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
  |-procname x -proctype y   |指定进程名称和进程类型                  |
  |-parentproctype x         |指定父进程类型                          |
  |-procfile x               |指定获取进程信息的文件名                |
  |-load x \[y..z\]          |指定加载的文件或数据库目录              |
  |-loaddir x \[y..z\]       |加载指定目录下所有的 .q, .k 文件        |
  |-localtime                |设置在日志记录或者定时器等动作中中使用本地时间而不是GMT时间。这个更改是向后兼容的; 如果不设置 -localtime 标志，那么进程在打印日志等动作中使用GMT时间， 这和.z.P的时间有所不同|
  |-trap                     |在初始化过程中加载外部文件时遇到的所有错误都将被捕获并记录，然后将继续向下执行|
  |-stop                     |如果发生错误将停止加载文件但是不退出    |
  |-noredirect               |不把 std out/std err 重定向到文件中 (调试中很有用)|
  |-noredirectalias          |不为日志文件创建别名 (别名会丢掉所有的后缀，例如时间撮)|
  |-noconfig                 |不加载配置项|
  |-nopi                     |将.z.pi重置为初始值 (调试中很有用)      |
  |-debug                    |等同于 \[-nopi -noredirect\]             |
  |-usage                    |输出使用提示信息并退出|
  |-onelog                   |将所有消息写入stdout日志文件中，需要注意的是未捕获到的错误仍将写入stderr日志文件中|
  |-test x                   |用于单元测试。传递测试目录的位置          |
  
除了上述参数之外，命名空间(.\*.\*)中的任何过程变量都可以通过命令行进行重写覆盖。
命令行上提供的参数优先级将高于任何其他地方的预定义值（例如，配置项或封装器中）。
在命令行中的变量名需要提供提供完整的路径，例如 -.servers.HOPENTIMEOUT 5000。

<a name="env"></a>

Using torq.sh
---------------------

torq.sh是一个能在torq中运行具有附加功能的进程的脚本，其中一个关键强项就是所有
进程配置现在都会位于同一位置。默认的进程文件位于 $KDBCONFIG/process.csv。 这个
脚本只能在Linux上使用。和torq.q一样它也需要设置环境变量。关于这个脚本的用法说明
可以通过在UNIX环境中运行`./torq.sh`来查看：

Environment Variables 
---------------------

需要设置的5个环境变量:

  |Environment Variable|   Description|
  |----------------------| -----------------------------------------------------|
  |KDBCONFIG              |基础的配置文件目录|
  |KDBCODE                |基础的代码目录|
  |KDBLOGS                |standard out/error 和使用日志记录的目录|
  |KDBHTML                |包含Html文件的目录|
  |KDBLIB                 |包含支持库的目录|


torq.q将检查这些环境变量，如果没有设置将会退出进程。如果torq.q是被其他脚本文件所
调用,那么这些需要的环境变量可以在加载torq.q文件前，通过设置 .proc.envvars 来进行
扩展。

<a name="procid"></a>

Process Identification
----------------------

AquaQ TorQ的关键在于进程是如何识别自己的。这主要是通过.proc.proctype和.proc.procname
这两个所需变量来定义的，它们分别代表了进程的类型和名称。另外一个通过定义一个可选
的变量parentproctype来指定初始化时需加载的基础代码库和配置。.proc.proctype和
.proc.procname这两个需要的值决定了加载的基础代码和配置，以及它们是怎样被其他进程
连接的。如果两个所需的变量没有被定义，那么TorQ将会尝试使用进程启动时的端口号来确
定加载的基础代码和配置。

其中最重要的是proctype。指定进程类型决定了用户将进程定义在了什么级别之上。例如，
在一个生产环境中，指定进程类型为“hdb”（历史数据库）和“rdb”（实时数据库）是有效的。
我们也可以基于功能的相似性，来进行更有效地分离，例如将进程类型指定为“hdbEMEA”和
“hdbAmericas”。在这个例子中，将parentproctype设置为“hdb”，并将所有公共代码放入
“hdb”的配置中进行预先加载，最后再来加载特定区域的配置将会显得更加明智。一个进程的
实际功能可以需要更具体地定义，这个将放在后面进行讨论。procname值仅用作识别进程的
目的。进程可以通过多种方式来指定它的类型和名称：

1.  从默认位置的进程文件中
    $KDBCONFIG/process.csv;

2.  从通过命令航参数定义的进程文件中
    -procfile;

3.  从启动时指定的端口中,这会参照进程文件做进一步的处理;

4.  使用命令行参数 -proctype and -procname;

5.  通过在加载torq.q的脚本文件中定义.proc.proctype and .proc.procname。

对于方式4和5，两个参数都必须使用该方法进行定义，否则将不生效（值将会从进程文件中读取）。

对于方式3，TorQ将检查进程文件，根据进程启用的端口号进行匹配，并根据该端口号和相
应的主机名条目类推断它的proctype和procname。

进程文件格式如下.

    aquaq$ cat config/process.csv 
    host,port,proctype,procname,U,localtime,g,T,w,load,startwithall,extras,qcmd
    aquaq,9997,rdb,rdb_europe_1,appconfig/passwords/accesslist.txt,1,1,3,,${KDBCODE}/processes/rdb.q,1,,q
    aquaq,9998,hdb,hdb_europe_1,appconfig/passwords/accesslist.txt,1,1,60,4000,${KDBHDB},1,,q
    aquaq,9999,hdb,hdb_europe_2,appconfig/passwords/accesslist.txt,1,1,60,4000,${KDBHDB},1,,q


进程将会读取这个文件并尝试根据它启动的主机和端口来识别它自己。主机可以是由.z.h
返回的值，也可以是服务器的IP地址。除非proctype和procname都作为命令行参数传递，
否则如果进程不能自动识别自己，那么它将会自动退出。如果这些个参数都被传入了，那
么进程将使用默认配置进行设置。


进程名之后的参数包括了这些:

  |Parameter                        |  Description|
  |---------------------------------| ----------------------------|
  |U                                |  权限认证需要的一个 usr:pwd 文件|
  |localtime                        |  设置进程基于本地时间，而非GMT时间运行|
  |g                                |  立即 (1) 或推迟 (0) 进行垃圾回收|
  |T                                |  客户端查询超时时间（s）, 0 代表做超时限制|
  |w                                |  工作空间大小（MB）|
  |load                             |  加载的文件或数据库目录|
  |startwithall                     |  确定是否在所有确定情况下都将启动进程| 
  |extras                           |  指定其他额外的参数|
  |qcmd                             |  允许使用不同版本的q或不同的命令行选项 - rlwap, numactl|

其中U/g/T/w是q标准的命令行参数，localtime和load负载是torq的命令行参数。

Running processes using torq.sh
-------

torq.sh 能够一次性全部启动或分多次批量的启动/停止进程。在进程启动/停止之前，
这个脚本会检查进程是否已经在运行了，并且执行这个脚本的时间将会被打印到屏幕上。

```
$ ./torq.sh start rdb1 hdb1 tickerplant1
    15:42:00 | Starting rdb1...
    15:42:00 | hdb1 already running
    15:42:00 | Starting tickerplant1...
            
$ ./torq.sh stop all
    15:46:19 | Shutting down hdb1...
    15:46:19 | Shutting down hdb2...
```
通过 torq.sh 脚本可以将所有进程的状态汇总信息输出到屏幕上，信息包括检测时间、
进程名称、状态、端口号以及进程的PID。
```
$ ./torq.sh summary
    TIME     | PROCESS        | STATUS | PORT   | PID
    11:33:59 | discovery1     | up     | 41001  | 14426
    11:33:59 | tickerplant1   | down   |
```
通过 torq.sh 脚本可以查看所有进程的启动代码。这对于同一脚本在启动时可以使
用不同命令行参数启动时是很有用的。
```
$ ./torq.sh print discovery1 
    Start line for discovery1:
    nohup q deploy/torq.q -procname discovery1 -stackid 41000 -proctype discovery -U appconfig/passwords/accesslist.txt -localtime 1 -g 0 -load deploy/code/processes/discovery.q -procfile deploy/appconfig/process.csv </dev/null > deploy/logs/torqdiscovery1.txt 2>&1 &
```
调试命令行的参数可以直接通过启动 torq.sh 时传递，通过这样的方式在调试模式
下启动进程。注意，在调试模式下，一次只能启动一个进程。
```
$ ./torq.sh debug tickerplant1 
```
如果指定的进程名称不在 process.csv 中，输入进程名将会作为无效输入返回。可
以通过以下命令查看所有的进程。
```
$ ./torq.sh procs
```
torq.sh 脚本可以在启动的时候可以通过 -csv 指定不同的进程文件， 要求参数后跟
着的是 process.csv 文件的完整路径。
```
$ ./torq.sh start all -csv ${KDBAPPCONFIG}/process.csv
```
可以通过 torq.sh 脚本来添加或者重写 g, T, w 或进程脚本的其他默认参数值。
```
$ ./torq.sh start rdb1 -extras -T 60 -w 4000
$ ./torq.sh start sort1 -extras -s -3
```


<a name="logging"></a>

Logging
-------

默认情况下，每个进程都会将输出重定向到标准输出日志和标准错误日志文件，并为
它们创建别名，每天凌晨会进行滚动更新。日志文件会输出到 $KDBLOGS 目录，创建
的日志文件包括：

  |Log File|                          Description|
  |---------------------------------| ----------------------------|
  |out\_\[procname\]\_\[date\].log   |Timestamped out log|
  |err\_\[procname\]\_\[date\].log   |Timestamped error log|
  |out\_\[procname\].log             |Alias to current log log|
  |err\_\[procname\].log             |Alias to current error log|

日期后缀可以通过修改 .proc.logtimestamp 函数并从另一脚本引用 torq.q 来重写。
比如，可以将后缀更改为完整的时间戳。

在设置了 -onelog 的情况下，TorQ会尝试将所有输出重定向到输出日志文件，但不幸
的是，这个机制并不完美。

TorQ 使用 \1 和 \2 来重定向 stderr 和 stdout，onelog只能将处理过的错误重写到 \1 
重定向。这是因为同时将两个输出重定向到同一个文件还存在问题，（定向到同一文件
会导致消息的排序不正确），这个问题是 kdb+ 的问题而不是 TorQ 的问题。

因为这个错误的存在，\1 和 \2 不能重定向到同一个文件，因此由 kdb+ 抛出并且未
处理的错误将会仍然指向 err 日志文件。

<a name="config"></a>

Configuration Loading
---------------------

### Default Configuration Loading

TorQ默认的进程配置包含在q脚本中，并存储在 $KDBCONFIG/settings 目录中。
每个进程尝试加载它能找到的所有配置，并尝试按以下顺序加载多个配置文件:

-   default.q:所有进程加载的默认配置。通过标准安装中，这个文件中应该包
    含内容是可定制配置的超集，包括了注释信息。

-   [parentproctype].q: 指定父进程类型的配置(仅当指定parentproctype时生效)。

-   [proctype].q: :指定进程类型的配置。

-   [procname].q: 指定进程名的配置。

这些配置中default.q是必须要出现的。其他的配置脚本可以只包含配置变量的一个子集，
后加载的变量将会覆盖之前加载的变量。

### Application Configuration Loading

应用特定的配置可以存储在用户定义的目录中，并通过设置 $KDBAPPCONFIG 环境变量来
暴露给TorQ。如果设置了$KDBAPPCONFIG，那么TorQ将搜索 $KDBAPPCONFIG/settings 目
录并加载它能找到的所有配置。应用程序配置将在所有默认配置之后按以下顺序加载:

-   default.q: 所有进程都会加载的应用默认配置文件。

-   [\[parentproctype\]]{}.q : 指定父进程类型的应用特定的配置(仅当指定
    parentproctype时生效)。

-   [\[proctype\]]{}.q: 指定进程类型的应用特定的配置。

-   [\[procname\]]{}.q: 指定进程名的应用特定的配置。

所有加载的配置将覆盖以前加载的所有内容。这些脚本都不是必须的，脚本的内容是默认
配置目录的默认配置变量的子集。

所有配置文件都会在代码执行前加载。

### Application Dependency

TorQ将自动检查应用程序版本和依赖项信息。TorQ会检查 $KDBAPPCONFIG 目录中的
 dependency.csv 文件。该文件应包含以下格式的信息:

|app  |version |dependency           |
|-----|--------|---------------------|
|app0 |1.0.0   |app1 1.1.1;app2 2.1.0|

TorQ同样会在 $KDBCONFIG 目录中搜索TorQ的 dependency.csv 文件。如果发现任何依
赖项的版本超过应用程序版本，TorQ将退出并记录错误日志。

如果 dependency.csv 的记录 中没有提供依赖文件，TorQ会正常运行，如果只提供一个
应用程序依赖文件，TorQ将退出并记录错误信息。

每个版本号的长度最多可达5位，以'.'分隔。当前的kdb+版本将自动添加，格式为
major.minor.yyyy.mm.dd

<a name="code"></a>

Code Loading
------------

代码是从 $KDBCODE 目录中进行加载。以下目录中包含一个通用的代码基础，每个进程
类型各自的代码基础，每个进程名各自的代码基础，，并按照如下顺序进行加载:

-   $KDBCODE/common: 所有进程共用的代码基础;

-   $KDBCODE/\[parentproctype\]: 指定父进程类型的特定的代码基础(仅当指定
    parentproctype时生效);

-   $KDBCODE/\[proctype\]: 指定进程类型的特定的代码基础;

-   $KDBCODE/\[procname\]: 指定进程名的特定的代码基础;

对于需要加载的任何目录，可以通过在目录中添加 order.txt 来指定加载顺序。
order.txt 指定了加载目录中的文件的顺序。如果文件不在 order.txt 中，它将在所有
列在order.txt文的文件之后进行加载。

除了从$KDBCODE中加载代码之外，还可以将特定应用程序的需要加载的代码保存在与上
面相同结构的用户定义目录中，并通过设置 $KDBAPPCODE 环境变量暴露给TorQ。

如果设置了这个环境变量，TorQ将按照以下顺序加载代码基础：

-   $KDBCODE/common: 所有进程共用的代码基础;

-   $KDBAPPCODE/common: 所有进程共用的应用特定的代码基础;

-   $KDBCODE/\[parentproctype\]：指定父进程类型的特定的代码基础(仅当指定
    parentproctype时生效);

-   $KDBAPPCODE/\[parentproctype\]: 指定父进程类型的应用特定的代码基础(仅当指定
    parentproctype时生效);;

-   $KDBCODE/\[proctype\]: 指定进程类型的特定的代码基础;

-   $KDBAPPCODE/\[proctype\]: 指定进程类型的应用特定的代码基础;

-   $KDBCODE/\[procname\]: 指定进程名的特定的代码基础;

-   $KDBAPPCODE/\[procname\]: 指定进程名的应用特定的代码基础;


您还可以使用 -loaddir 命令行参数来加载其他目录。

<a name="init"></a>

Initialization Errors
---------------------

初始化错误可以用不同的方式处理。默认操作是任何初始化错误导致进程退出。这是为
了启用快速故障类型条件（This is to enable fail-fast type conditions），在这
种情况下，进程完全且立即失败比以不确定的状态启动
要好。可以使用 -trap 或 -stop 命令行参数可以重新指定初始化失败时的行为。

使用 -trap ，进程将捕获错误，记录并继续向下运行。这在某些情况下非常有用：例如，
在加载一个不会被立即调用，可以在稍后重新加载的存有某些函数的文件时。

使用 -stop 时，进程将在错误点停止，但不会退出。

-stop和-trap对于调试都很有用。
