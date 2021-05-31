## git乱码解决探究

### 问题的产生

在使用git的时候 使用git status命令时经常会把中文字符转义，这样看起来就很不直观。

## 问题的解决

```bash
git config --global core.quotepath false 
```

这条命令解决了问题 关闭了全局的路径转义

## 命令的详情

对于输出的路径git默认对一些特殊字符进行转义 包括(TAB, n for LF，\ for backslash)以及大于0x80的字符，

当core.quotepath 设置为false 大于0x80的字符就不会被转义了，不过TAB等还是会被转义

同时大部分命令可以通过-z 选项逐字输出路径，便可以不修改core.quotepath 设置也可直观的看到路径了(然而对于git status 还是设置一下看起来比较好)

https://git-scm.com/docs/git-config

