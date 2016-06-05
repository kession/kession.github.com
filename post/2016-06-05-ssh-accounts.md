
## SSH多帐号使用

#### 1. 生成各帐号的rsa文件

> 位置：~/.ssh

  ssh-keygen -t rsa -C "xxxx@qq.com" -f "xxxx_id_rsa"
  ssh-keygen -t rsa -C "yyyy@qq.com" -f "yyyy_id_rsa"
  ssh-keygen -t rsa -C "zzzz@qq.com" -f "zzzz_id_rsa"

#### 2. 修改~/.ssh/config文件

  #Default xxxx
  Host gitlab.xxxx.com
  HostName gitlab.xxxxx.com
  User git
  IdentityFile ~/.ssh/xxxx_id_rsa


  #yyyy
  Host git.coding.net
  HostName git.coding.net
  User git
  IdentityFile ~/.ssh/yyyy_id_rsa

  #zzzz
  Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/zzzz_id_rsa

#### 3. 测试配置

  ssh -T git@github.com

#### 4.设置每个res的 user info

  git config user.name "xxx"
  git config user.email "xxx@xxx.com"
