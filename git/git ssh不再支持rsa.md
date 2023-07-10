# git ssh 不再支持 RSA 算法

ssh 排雷工具

~~~bash

ssh-keygen -t ed25519 -C "876056609@qq.com"

ssh -vT git@e.coding.com

ssh -v git@e.coding.com

ssh -T git@e.coding.com

ssh-add -k ~/.ssh/id_rsa

ssh-agent -s

evel `ssh-agent -s`
~~~

## references

[ssh-rsa 验证失败"no mutual signature algorithm"](https://zhuanlan.zhihu.com/p/419745598)
