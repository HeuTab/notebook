# Git

```
1.云端仓库
   a.在码云上创建云仓库
   b.在本地生成公钥，与远程仓库进行识别配置
   		git config --global user.name "zzy"
   		git config --global user.email "email@example.com"
   		ssh-keygen -t rsa -C "email@example.com"
   		回车三次，生成公钥
   		把公钥复制    配置到码云-管理-公钥
   c.本地仓库和远程仓库的关联
   		git remote add origin ssh
   		git remote add origin git@gitee.com:vivi_inno/zzy.git
   d.注意!
   第一次本地仓库和云端仓库对接，必须先拉取再推送
   除了第一次，以后都是直接推送即可
   拉取命令： git pull --rebase origin master

2.本地仓库
   a.初始化本地仓库  git init
   b.将文件添加到本地仓库   
           git add x.txt
   c.将添加好的文件进行提交本地仓库
            git commit  -m "提交文本"
   d.将本地仓库的内容推送到云仓库上
            git push origin master
            [注意：云端仓库提前配置完成]
```

