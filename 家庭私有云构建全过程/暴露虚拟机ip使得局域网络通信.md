### 家庭网络的虚拟机通信

#### 准备：一台笔记本和一台台式电脑

##### 原因：因为太懒了，所以才有创造！！！



###### 房间A        房间B

#### 概述：房间A 一台台式主机，房间B自己的卧室，使用笔记本操作两台所有的可以使用的计算资源。



![C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\配置ip](C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\配置ip.png)



配置通过nat的虚拟机ip地址为静态ip。



##### 通过配置端口转发，使得外部可以正常访问到虚拟机的ip中的ssh服务。



步骤一 ：



![C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\虚机配置项](C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\虚机配置项.png)



![C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\虚机配置2](C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\虚机配置2.png)





![C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\sshd](C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\sshd.png)



![C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\sshd2](C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\sshd2.png)



至此我们完成了虚机端口映射的所有准备工作。



步骤二：

1   关闭所在主机的防火墙

![C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\防火墙](C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\防火墙.png)



2  防火墙规则设置



![C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\进出站规则1](C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\进出站规则1.png)



![C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\进出站2](C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\进出站2.png)



![C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\进出站3](C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\进出站3.png)



![C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\进出站4](C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\进出站4.png)

![C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\进出站5](C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\进出站5.png)



![C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\进出站6](C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\进出站6.png)

![C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\进出站7](C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\进出站7.png)



#### win10需要特别设置



如果你是 win 10 系统，即使全部按照上边的操作，可能还是连接不上，那是因为:

win 10 系统默认不允许同一局域网中各用户之间进行连接(即同一局域网中的用户相互之间 ping 不通)

需要进行一下防火墙的设置

还是在防火墙的「高级设置」面板

在「入站规则」和「 出站规则」中打开以下选项

文件和打印机共享(回显请求-ICMPv4-….)

![C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\win10tebie](C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\win10tebie.png)





### 测试连接验证



1   尝试连接通过端口映射的10010虚拟机的sshd服务，尝试连接通过端口映射的10020虚拟机的sshd服务



![C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\测试1](C:\Users\Administrator\Desktop\实验\谐云科技工作\云原生\SystemConfiguration\家庭私有云构建全过程\images\测试1.png)





完全验证成功！



到此为止，我们已经将家庭中的所有计算资源做了集中管理。小朋友们来个赞呀！过年回家制作。






