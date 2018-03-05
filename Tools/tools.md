
# 工具脚本汇总

## cygwin + zsh：
* http://blog.lyndon.pw/2015/12/04/windows-shell/。<br>
* git zsh 需要使用相对路径下载，绝对路径存在linux windows 转换问题。<br>
* 使用find . -name "*.sh" | xargs dos2unix批量修复 linux 换行问题
* 在cygwin快捷方式目标修改为：C:\cygwin64\bin\mintty.exe -i /Cygwin-Terminal.ico /bin/zsh 以直接启动zsh

## 微信消息推送 Server酱
* url：https://sc.ftqq.com/3.version

## 杂项
* [markdown语法](https://github.com/guodongxiaren/README "markdown语法")
* smart git checkout
```
  git -c core.quotepath=false stash save 
  git -c core.quotepath=false checkout dev 
  git -c core.quotepath=false stash pop 
```
