#### &#x20;1.CAN总线

*   多主机局部网络串行通信协议（controller Area Network）,20世纪80年代，德国博世公司
*   1991年，CAN2.0发布，分为A/B两个部分；1993年，CAN成为ISO标准

    ![1.jpg](https://upload-images.jianshu.io/upload_images/13407176-ef18cb1e39b18db9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
*   CAN通信可以简单地理解成是一场电话会议。当一个人讲话时，其他人只能听，这个过程叫做广播；当多个人同时讲话时，根据一定规则来决定谁先讲话，谁后讲话，这个过程又叫做仲裁。在这场会议中，讲话人会确认听话人是否成功接收信息，如果说话人传递的信息有错误，听话人会及时指出错误。

#### &#x20;2.CAN总线的构成和通信机制

*   CAN总线使用双绞线进行差分电压传输，两条信号线分别被称为CAN高和CAN低，两端有防止信号反射的120欧终端电阻。能接入CAN总线的电控单元ECU，通常由微控制器、CAN控制器、CAN收发器三部分组成。很多功能强大的主控芯片都已经集成CAN控制器，例如STM32系列，只要外界一个CAN收发器就能正常工作。

    ![2.jpg](https://upload-images.jianshu.io/upload_images/13407176-de8b692df1f01fa9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
*   CAN2.0A和CAN2.0B规定，空闲时CAN高和CAN低上的电压是2.5V。此时，它们的差值是0，表示逻辑1为隐性电平；当总线有控制器传输信号时，两条导线上的电压出现差异，CAN高等于3.5V，CAN低等于1.5V。此时，它们的差值是2V，表示逻辑0为显性电平。

    ![3.jpg](https://upload-images.jianshu.io/upload_images/13407176-88ebec6cf05df4bc.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
*   为什么逻辑0是显性电平，逻辑1是隐性电平？主要原因是CAN总线采用“线与”规则进行冲突仲裁。当多个CAN信号同时发送时，有的发1，有的发0；而只要有0，当前总线就是0。所以，看上去1被0覆盖了。因此，逻辑0是显性电平。

    ![4.jpg](https://upload-images.jianshu.io/upload_images/13407176-4522b4dd0e277ec0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
*   CAN2.0A规定了11位标识符的标识符的标准帧格式；CAN2.0B在此基础上，又增加了具有29位标识符的扩展正格式。CAN消息帧根据用途不同，又分成4种类型：数据帧、远程帧、错误帧、超载帧。
*   总线空闲时处于隐性电平，开始发送数据时，首先要发一个显性电平0，标识帧起始。
*   **紧接着是ID仲裁场，由11位标识符和RTR远程发送请求位组成。11位标识符用来确定一条报文，表明报文的含义和优先级。上图中发动机报文CAN ID 0x100就是指这个位置的值，电平根据具体值变化。RTR是远程传送请求位，数据帧RTR为必须是显性电平，远程帧RTR为必须是隐性电平**。
*   控制场包括标识符扩展位IDE、保留位r、占4个bit的数据场长度码DLC。**IDE和r位均为显性电平0，数据场长度根据数据实际长度变化。数据场是CAN报文中最重要的部分，有用信息都在这0到8个字节中**。
*   CRC场包括CRC序列和CRC界定符DEL，CRC校验是为了通信双方的安全可靠性制定的暗号。只有发送方根据发送信息计算的CRC值与接收方根据接收信息计算的CRC值对上，才能判断这次通信成功。否则，就会报错。CRC算法一般是简单的多项式算法。界定符DEL为固定的隐性电平1。
*   应答场包括2位，应答间隙ACK和应答界定符DEL。发送节点发出的报文中，ACK和DEL均是隐性电平1。接收节点正确接收后，会用显性电平0覆盖隐性电平1，表示正确接收。同时，发送节点会进行回读，确认信号被正确接收。
*   帧结束，由7个连续的隐性位表示，表示数据帧结束。节点在检测到11个连续的隐性位后，认为总线空闲。

    ![5.jpg](https://upload-images.jianshu.io/upload_images/13407176-eb2954767a9567aa.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
*   扩展帧和数据帧的区别主要是ID仲裁场和控制场不同，多了远程代替请求位SRR，它默认为隐性电平即SRR等于1；标识符扩展符为隐性电平即IDE等于1。多了18位ID扩展识别符，可以支持更长的ID。两个保留位r0和r1仍然等于0。目前，用11位标准帧的情况比较多。

    ![6.jpg](https://upload-images.jianshu.io/upload_images/13407176-a96745d17e2b5758.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
*   远程帧就是在数据帧的基础上将数据场干掉，然后让RTR等于1。远程帧主要是用来请求其他节点发生相应数据信息的。

    ![7.jpg](https://upload-images.jianshu.io/upload_images/13407176-d6f33e19285286d5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### &#x20;3.非破坏性仲裁

*   **在CAN总线上发送的每一条报文，都具有唯一的11位或29位的报文ID。当节点同时发送报文时，将按照“线与”机制对ID的每一位进行判断。当有一个节点发送0，则总线的状态就是0。所以，ID的值越小，优先级越高**。0x65F=0110 0101 1111 、0x67F=0110 0111 1111 、0x659=0110 0101 1001，从高到低位开始比较，然后进行线与。

    ![8.jpg](https://upload-images.jianshu.io/upload_images/13407176-7b66600855b14a84.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### &#x20;4.位填充机制

*   CAN总线其中一种抗干扰措施，以减少消息帧在传送过程出错。CAN总线规定信号的跳变沿即为同步信号，所以只要信号发生了变化，节点时钟就会被同步。但是，如果有连续相同的信号发送，没有跳变发生，则可能出现发送/接收节点不同步，从而导致信号异常。CAN协议规定，当检测到5个连续相同的位信号时，实际发送会自动插入一个补码发送，然后再接着发送原有信号。位填充的取悦是SOF到CRC之间。

    ![9.jpg](https://upload-images.jianshu.io/upload_images/13407176-f7637b3d0f315e3c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### &#x20;5.位时序及同步

*   位定时是指CAN总线上一个数据位的持续时间，主要用于CAN总线上各节点的通信波特率设置。同一总线上的通信波特率必须相同，正常的位时间等于1/波特率。每一位发送的时间看上去很短，但电平变化速度可以更快，所以时间还可以分的更细。由于CAN通信没有时钟线，随着时间的推移信号的误差积累会越来越大。这时，同步的作用就不可或缺。
*   CAN总线上一个位数据可以被分成4个部分，同步段用于同步总线上不同的节点，该段内需要有一个跳边沿；传播段用于补偿CAN网络上的物理延迟；相位缓冲段用于补偿相位误差，可以通过重同步加长或缩短；CAN采样器采样的时间通常位于两个相位缓冲段之间。

    ![1.jpg](https://upload-images.jianshu.io/upload_images/13407176-b0a5bb1b05c18c60.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
*   同步有两种，发生在帧起始位SOF内的同步叫硬同步。发送节点发送SOF位时，SOF的下降沿与接收节点的下降沿不同步，接收节点会强行将自己当前同步段拉到根接收节点SOF位的同步段保持一致，从而保证采样点的一致。

    ![2.jpg](https://upload-images.jianshu.io/upload_images/13407176-282b3a42f7efe0f5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
*   另一种同步发生在其他位，叫做重同步。发送节点发送SOF位以外的位数据时，当发送节点和接收节点的同步段不同步，存在时间差。接收节点会通过延长或缩短相位缓冲段来保证采样时间的相同。

    ![3.jpg](https://upload-images.jianshu.io/upload_images/13407176-d3c25b9da03c2c8f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

    ![4.jpg](https://upload-images.jianshu.io/upload_images/13407176-3e144b8e1604fb76.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



