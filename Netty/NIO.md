NIO IO
面向缓冲区	面向流
非阻塞		阻塞
选择器		无

NIO的核心在于：通道（Channel）和缓冲区（Buffer），通道表示打开IO设备（例如：文件、套接字）
的链接。若需要使用NIO系统，需要获取用于连接IO设备的通道以及用于容纳数据的缓冲区。然后操作缓冲区，对数据进行处理。
**简而言之，Channel负责传输，Buffer负责存储。**

###Buffer
Buffer：负责数据的存取。缓冲区就是数组。

根据数据基本类型（除boolean）,提供了相应类型的缓冲区：

ByteBuffer、CharBuffer...

上述缓冲区的管理方式基本一致，通过allocate()获取缓冲区。

核心方法：
	put()：存入数据到缓冲区中
	get():获取缓冲区中的数据

	flip():切换读模式
	rewind():重复读
	clear():清空缓冲区，但是缓冲区中的数据依然存在，但是储物“被遗忘”状态（参数都重置，不能正确的读取数据）

	mark():记录当前position的位置
	reset():reset()进行重置position的位置。

	remaining()：获取缓冲区中剩余的数量
缓冲去中的四个核心属性：
capacity：容量，表示缓冲区中最大存储数据的容量
limit： 界限，表示缓冲区中可以操作数据的大小（limit 后数据不能进行读写）调用flip()后改变。
position： 位置，表示缓冲区中正在操作数据的位置
mark： 标记，记录当前position的位置，通过reset()进行重置。
mark <= position <= limit <= capacity


###直接缓冲区与非直接缓冲区：
非直接缓冲区：通过allocate()分配缓冲区（分配位置：JVM内存）
直接缓冲区：通过allocateDirector()分配直接缓冲区（分配位置：物理内存），提高效率（适用于大数据，大文件的操作）


### Channel：

一、通道：Channel类似于流，用于源节点与目标节点的连接。在Java NIO中负责缓冲区数据的传输但是Channel不能直接访问数据，需要和Buffer结合起来使用

二、通道的主要实现类

	java.nio.chnnels.channel 接口：
		|--SelectableChannel
			|--SocketChannel
			|--ServerSocketChannel
			|--DatagramChannel
			|--Pipe.SinkChannel
			|--Pipe.SourceChannel
		|--FileChannel
三、获取通道
1.Java针对支持通道的类提供了getChannel()方法

	本地IO：
	FileInputStream/FileOutStream
	RandomAccessFile

	网络IO：
	Socket
	ServerSocket
	DatagramSocket (收发UDP包的通道)
2.JDK1.7中的NIO.2 针对各个通道提供了静态方法open()
3.JDK1.7中的NIO.2 的Flies工具的newByteChannel()

四、通道之间的数据传输(直接缓冲区)
transferFrom（）
transferTo()

五、分散（Scatter）与聚集（Gather）
分散读取：将通道中的数据分散到多个缓冲区中
聚集：将多个缓冲中数据聚集到通道中去

六、Pipe（单向通道）
管道是2个线程之间的单向数据连接，Pipe有一个source通道和一个sink通道。数据会被写到sink通道，从Source通道读取