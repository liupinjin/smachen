---
title: Git命令行之创建SSH Key
date: 2016-12-29 15:16:16
categories:
- 文章
tags: 
- git
- ssh
---
1.查看是否已经有了ssh密钥，如果没有密钥则不会有此文件夹，有则备份删除
```command
cd ~/.ssh
```

2.生存密钥，直接回车，一般不使用密钥
```command
ssh-keygen -t rsa -C "登录github邮箱地址"
```
如看到如下提示信息，则说明创建成功，会得到了两个文件：id_rsa和id_rsa.pub
{% codeblock %}
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/Administrator/.ssh/id_rsa):       
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /c/Users/Administrator/.ssh/id_rsa.
Your public key has been saved in /c/Users/Administrator/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:DW3STxOwGurdxmUv7kzqv6KkyIHzjW3IjBXtq9Mc7R8 chenche0713@126.com
The key's randomart image is:
+---[RSA 2048]----+
|          ...    |
|         o . .   |
|     .  + = o    |
|    . .. B o .   |
|     o..S . +    |
|   ...o..o o .   |
|  o=.+.+o E o .  |
|  .+=*=o...* .   |
|    *++ .+++*.   |
+----[SHA256]-----+
{% endcodeblock %}

3.添加 私密钥 到ssh，如果之前输入密码，则此处也需要输入
```command
ssh-add ~/.ssh/id_rsa
```

如看到如下提示信息，则说明添加成功
{% codeblock %}
Identity added: /c/Users/Administrator/.ssh/id_rsa (/c/Users/Administrator/.ssh/id_rsa)
{% endcodeblock %}
否则，你有可能看到如下信息
{% codeblock %}
Could not open a connection to your authentication agent.
{% endcodeblock %}
此时你要先执行
```command
 eval $(ssh-agent)
```
如看到如下信息，则执行添加私钥命令（步奏3）
{% codeblock %}
Agent pid 6340
{% endcodeblock %}

4.在github上添加ssh密钥，添加的是“id_rsa.pub”里面的公钥，
即将此文件内容（字符串），添加到github（刚刚创建密钥是的账户）的个人中心的SSH KEY
Settings -> SSH and GPG keys - >New SSH keys

5.测试命令
```command
 ssh git@github.com
 ```
 如看到如下信息，恭喜你，创建成功
 {% codeblock %}
 PTY allocation request failed on channel 0
 Hi smachen! You've successfully authenticated, but GitHub does not provide shell access.
 Connection to github.com closed.
{% endcodeblock %}