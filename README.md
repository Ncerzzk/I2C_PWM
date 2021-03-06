Simple I2C PWM Module(FPGA based)
============

一个简单的FPGA PWM模块，通过I2C控制，使用SpinalHDL编写

之前在折腾在linux板子上跑PX4，画了个V3s的板子，但是这些linux板子都有一个问题，就是PWM通道太少了，
像用在飞控上，四个通道至少的吧，但人家只有一两个PWM通道

这种板子跑px4一般也是用外置的芯片来输出PWM波，典型的比如pca9685：https://www.nxp.com/products/power-management/lighting-driver-and-controller-ics/ic-led-controllers/16-channel-12-bit-pwm-fm-plus-ic-bus-led-controller:PCA9685

但是pca9685有个问题，就是输出的pwm频率范围才24hz-1526hz，如果对于一般的电调来肯定是绰绰有余了，然而我想先将这个飞控用在
空心杯四轴上测试测试。空心杯四轴上这1k频率的pwm稍微有点不够了，网上四处搜了一下，发现这种PWM LED控制芯片大同小异，基本都无法满足
需求，于是干脆自己用FPGA撸了一个，顺便复习了一下SpinalHDL的语法

I2C通信方面Spinalhdl已经封装好一个apb3的I2C 模块，基于这个模块再封装成具体的I2C Slave 即可。不过用之前还是得充分的理解一下它的实现的，Spinalhdl的I2C相关的文档较少，
基本只能通过代码里的注释来理解。


## 使用说明
- I2C addr: 0x20

## 寄存器说明

模块寄存器都为16位寄存器

| 寄存器地址     | 寄存器说明                                                                                     |
|-----------|-------------------------------------------------------------------------------------------|
| 0x00      | period                                                                                    |
| 0x01-0x08 | CCR0-CCR7                                                                                 |
| 0x20-0x21 | pwm_channel_map(0-1)，设置某个PWM通道所使用的period，一个通道2 bits，0为默认period                            |
| 0x40      | subpwm_1_period                                                                           |
| 0x80      | config(msb为timeout功能开关,14 bit 为 sub_pwm_1_enable, low 5 bits 为预分频系数(0为1分频， 依次类推)，其他位目前保留) | 
| 0x81      | timeout_max_low16bits                                                                     |
| 0x82      | timeout_max_high16bits                                                                    |



