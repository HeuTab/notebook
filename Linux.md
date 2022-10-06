# Linux

## 普通用户切换到root、root切换回普通用户

```shell
sudo -i

su - zhougy
```

## xshell连接Ubuntu失败

```shell
#查看ssh是否启动
ps -e | grep ssh

#如果未启动，则开启ssh
sudo service sshd start
sudo /etc/init.d/ssh start

#安装命令
sudo apt install openssh-server
```

