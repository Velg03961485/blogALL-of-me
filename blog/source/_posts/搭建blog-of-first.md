---
title: 搭建blog of first
date: 2018-05-24 18:57:09
tags:

你想拥有自己的一个博客吗？
只要需要半天时间，你就拥有属于自己的一个博客网站
首先，你要具备，安装node.js、下载git、熟悉git命令、拥有github账号、注册阿里云购买域名
（手把手式教程）

一、首先，你要在自己的电脑上拥有以上这些环境，或者安装东西
在自己的电脑上安装一些插件，或者环境，首先要具备终端安装的npm命令
npm单独下载比较麻烦，幸好，node.js里面自带这些，并且帮你安装好，轻松使用上手
https://nodejs.org/en/  下载node.js 
安装好后，打开终端输入 
node -v  出现版本号
npm -v   出现版本号
恭喜你，node 你已经安装成功

二、下载git
https://git-scm.com/  安装，走起，简单轻松，安装完毕
右击查看一下，看到 Git Bash Here ，这步OK 

三、走到这里，你就离自己的博客还差...嗯...五分之一吧(*╹▽╹*)
1.在D盘，建立一个文件夹（命名blog），进入文件夹 ，右击Git Bash Here，进入Git命令
2.接下来就要敲命令啦，这对你来说，太简单了，只要跟着我一步一步走就行啦
npm install -g hexo-cli

hexo init blog

cd blog

npm install

ヾ(@^▽^@)ノ四个步骤下来，你又离成功近了一步
3.试试吧，先对待生成的网站，添加自己的第一笔
执行命令
hexo new "first blog of velg"   创建文本

vi first blog of velg    进入文本

i   在命令行编写文本内容o(￣▽￣)ｄ，随便填些内容吧，来一首三毛的诗歌更好哟

Esc  编写好后，Esc一下，然后

:wq  + Enter  退出编辑状态，你的第一篇blog就编写成功啦ヾ(@^▽^@)ノ
4.那就开始啦，走向网站的最终生成
执行命令
hexo generate  (简写 hexo g)

hexo server   (简写 hexo s)

5.试着在浏览器中，输入 localhost:4000
看到没，你刚编写的第一个blog就在里面啦

四、本地是好了，可是只有放在网上才是真正的blog呀，别急，接下来就是大招了
1.最简单的建站，是放在github上面，可以快速搭建起来，并实时对自己的blog快速更新
打开github,注册自己的一个账号,(友情提示啊，千万不要用自己注册阿里云的邮箱去注册guthub，不然就有得玩了，我就深受此坑(꒦_꒦) )
2.在github界面，点击+ new repository  
3.在 Repository name 填写  你的用户名.github.io  (比如,velg09361485.github.io),不要填错呀
4.Description 随便填一下啦
5.选择 Public
6.勾选 Initialize this repository with a README
7.点击 Create repository

五、存放自己博客的地方已经弄好了，但是，github 有一个要求就是要用ssh 和自己的电脑创连接，才能把本地的内容传输到github上
1.这是有些苦逼的一步，初玩GitHub的时候，ssh秘钥差点把自己玩哭
2.现在自己的电脑上生成秘钥和公钥，看网络教程吧，简单是简单，中间是有很多坑的
3.查看自己电脑的公钥，C:\Users\Administrator\.ssh ，看到文件id_rsa.pub
4.用记事本打开，复制一下
5.在GitHub / Setting 找到 SSH and GPG keys
6.点击 New SSH key 把复制的公钥粘贴进去  ，ok 你的GitHub就和本地创建连接了

六、接下来，就要把自己的项目上传上去了
执行命令
cd blog (这里是第二个blog)

hexo g

cd public

git init

git add .

git commit add origin git@--------   (你刚创的项目连接)

git remote  (如果输出 origin ,说明和远程仓库已经连接成功)

git pull --rebase origin master (把远程仓库的 README 文件拉下来，好多人忘了这一步，会导致接下来上传不成功的)

git push -u origin master  (这次的推送，一定记得带  -u 哟，以后再推送就不用了)

七、在浏览器输入，你的GitHub的项目名，（例如，velg03961485.github.io）
惊不惊喜(*^▽^*)，你的blog，在网上搭建成功

八、后记，下一篇将会给你介绍，如何用自己的个性域名进入自己的blog网站和如何设置自己的blog封面样式
