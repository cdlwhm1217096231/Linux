#### 1.CAN总线概要

*   应用最多的是汽车领域，Controller Area Network（控制器局域网），控制器在汽车中的专业术语叫ECU(电子控制单元Electronic Control Unit)，它可以看成是一个超小型的计算机（ECU内部包含供电系统、单片机、驱动系统，是汽车中最小的控制模块）。为了让ECU之间可以通信，人们设计了一种CAN协议。
*   要进行CAN通信，需要专门的CAN收发芯片。单片机发送或接收的是普通电平信号(1是高电平，0是低电平)，经过CAN收发器后，普通电平信号就会被转化成差分信号。
    ![1.jpg](https://upload-images.jianshu.io/upload_images/13407176-78a9b9f56ef44575.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
*   差分线是用两根线表示一个信号。**如果单片机发送一个普通逻辑的低电平，差分的两根线会分别输出3.5V和1.5V，两者之间的电压差是2V，则是显性电平，表示逻辑0；如果单片机发送一个普通逻辑的高电平，差分的两根线会分别输出的都是2.5V，两者之间的电压差是0V，则是隐性电平，表示逻辑1。**
    ![2.jpg](https://upload-images.jianshu.io/upload_images/13407176-442016f849a11bb2.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
*   同样地，CAN收发器也可以把接收到的差分信号转成普通电平信号，然后再发给单片机。
    ![3.jpg](https://upload-images.jianshu.io/upload_images/13407176-ecbc29eee7fcf326.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
*   使用差分信号有什么好处呢？如果是普通信号，它只有一根线。当某一点受到干扰，它的电平就会发生跳变导致传输出现错误，所以不能进行长距离传输。**而CAN通信采用的差分信号是两根线共同作用且是双绞线缠绕，这样即使受到干扰也是两根线同时受到干扰，它们的压差会保持不变，这样就能保证传递的信息不受干扰。所以CAN信号可以传递的距离很长，可达1000m（低速）**。
    ![4.jpg](https://upload-images.jianshu.io/upload_images/13407176-5f041799d5f68397.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 2.CAN通信数据传输格式

*   第一位是起始位，它必须是逻辑0。紧接着的11位是识别码，根据这11位的识别码就能知道这一帧信息是发送给哪个设备，每一个设备都有属于自己的11位识别码。
    ![5.jpg](https://upload-images.jianshu.io/upload_images/13407176-cc5954ef4e180b4c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
    ![6.jpg](https://upload-images.jianshu.io/upload_images/13407176-2236abdeb474648f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   接下来的1位RTR位是用来区分是数据帧还是远程请求帧。
    *   RTR位是1，则是远程请求帧；
    *   RTR位是0，则是数据帧；
        ![7.jpg](https://upload-images.jianshu.io/upload_images/13407176-2ca745101438664c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   接下来的6位是控制码IDE，它是控制数据长度的。
    *   它的第1位是用来区分标准格式和拓展格式。第一位是0，则在标准格式中有11位识别码；第一位是1，则在拓展格式中它的识别码有29位。
        ![8.jpg](https://upload-images.jianshu.io/upload_images/13407176-ae2817da769c26d1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
    *   它的第2位是预留位/空闲位，它固定是逻辑0。
        ![9.jpg](https://upload-images.jianshu.io/upload_images/13407176-289d6e24c082fab6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
    *   接下来的4位是DLC(数据长度代码)位，它的二进制是0\~8。如果是0001这种格式，则后面的数据位就只有1个字节即8位；如果是1000这种格式，则后面的数据就会有8个字节即64位。

        ![10.jpg](https://upload-images.jianshu.io/upload_images/13407176-3c6d217ebc2988b0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

        ![11.jpg](https://upload-images.jianshu.io/upload_images/13407176-01542d167ca6cd68.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

        ![12.jpg](https://upload-images.jianshu.io/upload_images/13407176-253abe664d670708.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   接下来是16位CRC循环冗余校验位，它是为了确保数据的准确性而设置的。首先是15位CRC校验码，设备接收端会根据数据计算出它的CRC位。如果计算出来的和接收到的CRC不一致，说明数据存在问题，就会重新发送一遍数据帧。然后是1位CRC的界定符，它是逻辑1，为了能把后面的信息隔开。
    ![13.jpg](https://upload-images.jianshu.io/upload_images/13407176-5c15c4f397b31682.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   然后是2位ACK码，第一位是ACK确认槽，发送端发送的是逻辑1而接收端回复的是逻辑0来表示应答。第二位是ACK界定位，它一定是逻辑1，作用是把后面的数据隔开。
    ![14.jpg](https://upload-images.jianshu.io/upload_images/13407176-0675a7e254f7d9b7.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   最后7位是结束位，这7位都是逻辑1，表示数据帧传输结束。
    ![15.jpg](https://upload-images.jianshu.io/upload_images/13407176-c8139a7e2f0811a8.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   **由于CAN总线上挂载了很多设备，如果是两个设备同时发送数据，此时哪一个设备发送的数据优先呢？答案是：看11位的识别码，它不仅是设备的身份证号，而且也代表了优先级。**
    ![16.jpg](https://upload-images.jianshu.io/upload_images/13407176-253dd47e721f314c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
    ![17.jpg](https://upload-images.jianshu.io/upload_images/13407176-244e4b0e5bd63923.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   例如：下图中这两帧数据是同时发送的，**当总线上同时出现逻辑0和逻辑1时，总线会被置为逻辑0(线与规则，解决冲突仲裁问题)。此后，上面那个数据帧就不会被发送。** ![18.jpg](https://upload-images.jianshu.io/upload_images/13407176-fb0ba75f65eafa1a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![19.jpg](https://upload-images.jianshu.io/upload_images/13407176-a7ce4c19a5ccaa9a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
