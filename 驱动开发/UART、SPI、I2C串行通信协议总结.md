#### 1.串行通信

*   从传输方向上可以分为单工、半双工、全双工通信

    *   单工通信：数据只能从发送端发送给接收端，不能反向发送
    *   半双工通信：数据可以在发送端和接收端之间互相传输，**但不能同时发送**
    *   **全双工通信：数据可以在发送端和接收端之间同时互相传输**
*   波特率(单位bps):发送二进制数据位的速率，表示每秒传输二进制位的数量。例如：256bps即每秒能发送256个数据位。
*   **异步通信：发送方发出数据后，不等接收方发回响应，接着发送下个数据包的通讯方式---非阻塞式**。
*   **同步通信：发送方发出数据后，等接收方发回响应以后才发下一个数据包的通讯方式---阻塞式。**

![4.jpg](https://upload-images.jianshu.io/upload_images/13407176-d369210ee4875ef0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 2.UART

*   两条数据线和一条地线，支持全双工异步通信。

![Snipaste\_2023-07-19\_21-14-35.jpg](https://upload-images.jianshu.io/upload_images/13407176-ebfce245c8716a6e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   UART是从低位到高位依次发送8bit数据，UART实际一次发送和接收的是10位数据。注意:发送和接收方的波特率要保持一致。当总线处于空闲状态时，线路保持高电平；发生数据前会先发送一个0，先让总线从高电平变为低电平，提醒数据接收方做好准备。依次从低位到高位发送8位数据。8位数据完成传输后，会发送一个1让总线重新回到高电平状态。如果要发送新的数据，需要重新发生起始位重复上面的过程。RS-232、USB转串口，多用于板间通信。

![1.jpg](https://upload-images.jianshu.io/upload_images/13407176-bac5e24e3cfe9eca.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### &#x20;3.I2C总线协议

![2.jpg](https://upload-images.jianshu.io/upload_images/13407176-f9cabc7f65220621.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   **I2C总线有两条线，一条SCL时钟线用于同步，一条SDA数据线用于传输数据。I2C总线能挂载多个器件且支持多主机模式即线路上的任何一个器件都可以作为主机，但受限于只有一根信号线，同一时刻只能有一个主机。主机拥有该时刻下总线的控制权即发起和结束一次通信的权利，而从机只能被主机呼叫**。
*   由于I2C总线上挂载有多个器件，那么主机是如何识别出自己要呼叫的从机呢？

    *   **在I2C协议中，每个器件都有一个自己固定的号码，它是一个7位的地址。**

![4.jpg](https://upload-images.jianshu.io/upload_images/13407176-407c4330d4914832.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   \*\*当MCU为主机要读取E2PROM中的数据时，MCU主机会向总线发送一个0x31找到E2PROM。\*\*I2C协议发送数据的规则和时序如下:

    *   (1).假设我们需要向地址为0x31的E2PROM发送0x96这个数据，I2C总线数据发送的顺序和UART不同，它是从高位到低位依次发送。当总线空闲时，SCL时钟线和SDA数据线均保持高电平；

        ![5.jpg](https://upload-images.jianshu.io/upload_images/13407176-02384b1c3f4184bf.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
    *   (2).当主机要开始传输数据时，会先将SDA数据线拉低，而此时SDA数据线上这个从高到低的跳边沿即起始位。

    &#x9;![6.jpg](https://upload-images.jianshu.io/upload_images/13407176-89ca5e82b98e03ae.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

    *   **(3).接下来就是要进行器件寻址，在SCL低电平时依次发送7位地址位。0x31发送完成后，紧接着主机会发送一个读写指示位(低电平表示要发送数据，高电平表示要请求数据)。主机发送完以上数据，从机如果成功接收会发送一个应答位到总线上。**

    ![7.jpg](https://upload-images.jianshu.io/upload_images/13407176-85710bfbb2f1049e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

    *   **(4).这里需要特别注意:只有 SCL低电平时，SDA可以变化；SCL高电平时，SDA需要保持；以方便数据接收方读取操作。**

        ![8.jpg](https://upload-images.jianshu.io/upload_images/13407176-66e6a30e096a9b1b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

    *   **(5).发送完寻址位找到了要通信的器件，接下来主机就可以正式开始发送数据，发送数据的过程和寻址的过程一样。特别说明:当一个字节即8bit数据发送完成后，要有一个应答位才能发送下一个字节的数据。当要传输的所有数据都发送完毕后，主机要将SCL时钟线拉到高电平并且将SDA数据线从低电平拉到高电平，即这个从低到高的跳边沿表示了停止位。**

        ![q1.jpg](https://upload-images.jianshu.io/upload_images/13407176-bf7b357ace459dab.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   I2C总线通信一般流程如下:

    *   主机发送起始位并进行从机寻址；
    *   得到应答后主机开始发送/读取数据位；
    *   数据发送/读取完成后，主机发送停止位结束此次通信；

#### &#x20;4.SPI总线协议

![10.jpg](https://upload-images.jianshu.io/upload_images/13407176-a54096766a416c87.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   标准SPI协议有4根线，SCLK(必须存在的，其他三条线都可以根据实际情况进行删减)、MOSI、MISO、CS。

![11.jpg](https://upload-images.jianshu.io/upload_images/13407176-8bcd05a8892621f6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   在同一时刻，主机只能跟一个从机进行通信。当总线上存在多个从机时需要进行片选，将从机的CS接口电平拉高或拉低。

![13.jpg](https://upload-images.jianshu.io/upload_images/13407176-3b5bbe8f75615f40.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   SPI和I2C一样，数据是从高位到低位依次发送，SPI协议中SCLK在空闲时可以是高电平也可以是低电平。下面以空闲时，SCLK为高电平举例。

    *   当SCLK出现下降沿，从高电平跳到低电平时进行数据输出；当SCLK出现上升沿，从低电平跳到高电平时进行数据采样。

    &#x9;![1.jpg](https://upload-images.jianshu.io/upload_images/13407176-505f37355527f3bc.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
*   和I2C协议相比，SPI协议没有开始位、结束位、应答位，规则上简单很多。SPI协议中SCLK在空闲时可以是高电平也可以是低电平，这个其实反映了时钟的极性。

    ![2.jpg](https://upload-images.jianshu.io/upload_images/13407176-65b57135e823992c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
*   时钟的相位，它决定了什么时候进行数据输出，什么时候进行数据采样。

    ![3.jpg](https://upload-images.jianshu.io/upload_images/13407176-54ba4db646620d11.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

