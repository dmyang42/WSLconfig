## WSL + Shadowsocks + Anaconda(Python environment) + SSH(Connected with Windows)  :hamster:  :koala:  :monkey_face: 



#### WSL - 基础配置 hyper + zsh  :waxing_crescent_moon: 

参考https://suiyuanjian.com/225.html



#### Shadowsocks  :first_quarter_moon: 

第一种方法是直接让WSL开全局代理，直接监听1080端口（即ss的default代理端口，如果是其他端口也要改这个地方）。在这种情况下，不论windows是否开启了系统代理，只有1080端口对应的进程是ss，WSL就可以利用这个端口使用代理，相当于是WSL的一个“启用系统代理”按钮。需要注意的是这样代理模式是全局代理，如果需要使用PAC代理可以google一下，有一些解决方案。

```shell
➜  topol export http_proxy="http://127.0.0.1:1080"
➜  topol export https_proxy="http://127.0.0.1:1080"
```

第二种方法自然就是下载ss到WSL上，然后配置一下。



#### Anaconda :waxing_gibbous_moon: 

下载anaconda只有一个地方需要注意下，就是我们使用的Shell是zsh，而非bash。在anaconda下载中会提示你，是否允许anaconda在~/.bashrc中加入环境变量，这个是不论你设置的default shell为什么都会安在bash中。比较直观的做法就是往~/.zshrc中加入

```shell
export PATH="/root/anaconda3/bin:$PATH"
```

但如果担心以后又出这样的问题，其实可以在~/.bashrc最后加入

```shell
bash -c zsh
```

同时在shell中执行

```shell
➜  topol chsh -s /bin/bash
```

这样默认shell为bash，在每次启动执行完~/.bashrc后环境变量都已经加载了，这时候再进入zsh作为工作的shell。非常奇怪的做法。 :pig_nose: 

不管怎样，成功安装好anaconda后，就会有如下结果

```shell
➜  topol python
Python 3.6.5 |Anaconda, Inc.| (default, Apr 29 2018, 16:14:56)
[GCC 7.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>>
```



#### SSH :full_moon: 

最后的ssh是在我们的windows系统和WSL建建立一个加密通道，当然你也可以明文通信，这里只说下用ssh（毕竟以后工作不会是WSL这种还可以访问到windows的系统，权当练习）

WSL自带了OpenSSH，所以我们不用再apt-get之类的，直接

```shell
➜  topol /usr/bin/ssh-keygen -A
➜  topol vim /etc/ssh/sshd_config
```

进入==sshd_config==后，以下内容取消注释或添加即可

```shell
# default port is 22, which is occupied by win
Port 2333

# my default account is root acount, so this line is needed
PermitRootLogin yes

PasswordAuthentication yes

UsePAM yes

X11Forwarding yes
```

而后在shell中输入命令

```shell
➜  topol /etc/init.d/ssh start
```

此时可以再打开一个WSL窗口输入指令验证

```shell
➜  topol ssh localhost -p 2333
root@localhost's password:
Welcome to Ubuntu 18.04.1 LTS (GNU/Linux 4.4.0-17134-Microsoft x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed Aug 15 21:13:25 DST 2018

  System load:    0.52      Memory usage: 59%   Processes:       14
  Usage of /home: unknown   Swap usage:   0%    Users logged in: 0

  => There were exceptions while processing one or more plugins. See
     /var/log/landscape/sysinfo.log for more information.


  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.


Last login: Wed Aug 15 21:11:28 2018 from ::1
➜  ~ exit
Connection to localhost closed.
➜  topol python
```

成功了 :sunflower: 

此后我们可以用windows下的ssh工具PuTTY / XShell来控制WSL，也可以用PyCharm这些IDE远程调试代码（在PyCharm中有SSH远程编译器的选项，在“设置-编译器-添加”中可以看到）
