# Git

## 提交修正效果：在log中，提交修正的信息 覆盖之前提交的信息
- 提交的代码有问题；或者漏提交了一些文件；或者提交信息写错了
```
git add.
git commit --amend --m "commit message"
```
- 当项目复杂，开发周期较长，针对特定功能开发时，会产生大量commit记录，导致日志冗长且重点模糊
```
git add.
git commit --amend --m "commit message 增加功能n"
```
