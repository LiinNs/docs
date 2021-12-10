# git 常见问题处理

## git pull 强制覆盖本地

```sh
// 从远程拉取所有内容
git fetch --all

// reset 本地代码
git reset --hard origin/master

// 重启拉取对齐
git pull
```

例如两个代码仓库 origin、coding ，希望从 coding 仓库同步代码

`git reset --hard coding/master && git pull coding master`

## 不在跟踪某文件变化

```sh
# Remove file from the repository but keep it in your working directory
git rm --cached your_filename

# This will keep the file in the repository, but it won't commit changes to it. It will stay unchanged in the repository:
git update-index --assume-unchanged your_filename

# to undo the previous command(tell git that you do want to keep track of changes for the file), there's the opposite command, --no-assume-unchanged
git update-index --no-assume-unchanged your_filename
```

## reference

[git pull 强制覆盖本地](https://juejin.cn/post/6844903892203864078)
