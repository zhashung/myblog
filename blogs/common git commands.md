
本人将经常用到的Git命令总结如下表，未来用到更多高级操作后也会时常更新。

如有错误，还请多指正。
## 分支操作
| 命令                                       | 内容                                     |
| ------------------------------------------ | ---------------------------------------- |
| git merge &lt;branch&gt;                   | 把branch合并到当前分支                   |
| git rebase &lt;branch&gt; &lt;branch2&lt;  | 把branch2分支变基到branch分支            |
| git branch                                 | 查看本地分支                             |
| git branch -r                              | 查看远程跟踪仓库分支                     |
| git branch -D &lt;branch&gt;               | 删除branch分支                           |
| git branch -r -d &lt;origin/branch&gt;     | 删除远程跟踪仓库分支，注意添加origin/    |
| git switch &lt;branch&gt;                  | 切换到branch分支(只能切换本地分支)       |
| git switch -c &lt;branch&gt; &lt;SHA1&gt;  | 在SHA1处建立branch分支                   |
| git checkout &lt;branch                    | 切换到branch分支（可以切换到origin分支） |
| git checkout -b &lt;branch&gt;&lt;SHA1&gt; | 在SHA1处建立 branch分支                  |
| git remote prune origin                    | 清理无效本地分支                         |

&emsp;&emsp;<small>tip：不建议在分支操作上使用checkout命令</small>

## 远端操作
| 命令                                          | 内容                                             |
| --------------------------------------------- | ------------------------------------------------ |
| git clone -b &lt;branch&gt; &lt;url&gt;       | 只clone branch分支                               |
| git fetch origin &lt;branch&gt;               | 只拉取远端的branch分支,不指定则拉取全部分支      |
| git push                                      | 默认只推送当前分支                               |
| git push -f                                   | 强制推送当前分支，如果远端和本地有冲突时建议使用 |
| git push origin &lt;branch&gt;                | 推送branch分支到远端origin库                     |
| git remote rm origin                          | 删除对应的远程仓库                               |
| git remote add origin &lt;url&gt;             | 添加远端仓库地址                                 |
| git push -u origin &lt;branch&gt;             | 将本地<branch>添加并推送到远端仓库               |
| git push --set-upstream origin &lt;branch&gt; | 将本地当前分支与远端仓库关联并push               |
| git branch --unset-upstream                   | 取消当前分支与远端仓库分支的关联                 |

## 提交修改
| 命令                                            | 内容                                                                                                                      |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| git log --graph                                 | 查看最近提交日志                                                                                                          |
| git restore --staged                            | 全部取消暂存+同时当stash pop如果有冲突也会回退stash                                                                       |
| git add .                                       | 全部添加到stage                                                                                                           |
| git add -p                                      | 将文件分割，分块确定是否提交到stage                                                                                       |
| git checkout --&lt;file&gt;                     | 撤销file文件在最近commit中修改至stage                                                                                     |
| git stash                                       | workspace添加到stash                                                                                                      |
| git stash pop                                   | 从stash区取出，如果有conflict则二进制文件不会替换，可使用checkout，代码文件需要resolve，最后需要add                       |
| git stash drop                                  | 如果pop后conflicts，解决后需手动drop                                                                                      |
| git commit --amend                              | 将暂存区提交，如果没有改动，则只修改当前commit的message                                                                   |
| git reset (--hard/--soft) (HEAD~n/&lt;SHA1&gt;) | 回退commit至（HEAD-n）或SHA1                                                                                              |
| git  rebase -i HEAD~n                           | 修改n个历史commits                                                                                                        |
| git checkout &lt;file&gt; --ours/--theirs       | 如果conflict文件是二进制文件。直接选择使用--ours还是--theirs。注意当merge时ours表示当前分支，rebase时theirs表示当前分支。 |

## 其他操作
| 命令                                                               | 内容                        |
| ------------------------------------------------------------------ | --------------------------- |
| git config (--global/--local) --list                               | 查看全局或当前仓库的配置    |
| Git config  (--global/--local) (user.name/user.email) &lt;名称&lt; | 修改全局或本地git名字或邮箱 |

