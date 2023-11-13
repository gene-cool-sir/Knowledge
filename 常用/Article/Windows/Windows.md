# Windows

1.  安装wsl :&#x20;

<https://blog.csdn.net/weixin_44904239/article/details/130820174>

1.  问题处理:&#x20;

wsl -l --all

wsl --setdefault Ubuntu-20.04

<https://www.nuomiphp.com/a/stackoverflow/zh/6380bd98ea0a3c7e1d109d86.html>

wsl 配置颜色：

```java
https://ohmyposh.dev/
1. windows下进行配置wsl主题
code $Profile  
输入一下内容：
····
D:\ProgramSoftware\powershell\posh\oh-posh.exe init pwsh --config 'D:\ProgramSoftware\powershell\posh\themes\night-owl.omp.json' | Invoke-Expression

# Import-Module Terminal-Icons
$OutputEncoding = [console]::InputEncoding = [console]::OutputEncoding = New-Object System.Text.UTF8Encoding

Import-Module Terminal-Icons

Set-PSReadLineOption -PredictionSource History

Set-PSReadlineKeyHandler -Key Tab -Function Complete # 设置 Tab 键补全
Set-PSReadlineKeyHandler -Key "Ctrl+u" -Function DeleteLine # 清空当前行内容
Set-PSReadlineKeyHandler -Key "Ctrl+k" -Function ForwardDeleteLine # 删除当前光标所在位置到结尾的内容
Set-PSReadlineKeyHandler -Key "Ctrl+a" -Function BeginningOfLine # 将光标移动到行首
Set-PSReadlineKeyHandler -Key "Ctrl+e" -Function EndOfLine # 将光标移动到行尾
Set-PSReadlineKeyHandler -Key "Ctrl+w" -Function BackwardDeleteWord # 删除光标所在位置到前一个单词开头
Set-PSReadLineKeyHandler -Key "ctrl+d" -Function MenuComplete # 设置 Ctrl+d 为菜单补全和 Intellisense
Set-PSReadLineKeyHandler -Key "Ctrl+z" -Function Undo # 设置 Ctrl+z 为撤销
Set-PSReadLineKeyHandler -Key UpArrow -Function HistorySearchBackward # 设置向上键为后向搜索历史记录
Set-PSReadLineKeyHandler -Key DownArrow -Function HistorySearchForward # 设置向下键为前向搜索历史纪录

# work
# 拷贝文件到164
function copyTo164($path,$target='~') {
    scp -r -i 'D:\carl\doc\ssh\Company.172.16.5.164' $path suunto@172.16.5.164:$target
}

# 从164拷贝文件
function copyFrom164($path,$target='.'){
    scp -r -i 'D:\carl\doc\ssh\Company.172.16.5.164' suunto@172.16.5.164:$path $target
}

Set-Alias cpt164 copyTo164
Set-Alias cpf164 copyFrom164
$OutputEncoding = [console]::InputEncoding = [console]::OutputEncoding = New-Object System.Text.UTF8Encoding
····

其中“night-owl.omp.json”为界面样式

2.如果需要在ubunto 中设置
1. sudo /usr/local/bin 目录下添加 oh-my-posh 文件
2.在mkdir ~/.poshthemes 并把windows 中下载的主题theme对应的json文件拷贝进来
3.新建启动类加载主题配置信息 vim /etc/profile.d/env.sh  
  eval "$(oh-my-posh init bash --config '~/.poshthemes/night-owl.omp.json')"



```

1.  在wsl 中使用并配置git

git config --global [user.name](http://user.name "user.name") zhangbing.xie
git config --global user.email xxx\@mail
git config --global credential.helper store  #设置存储用户名/密码

git config --global core.autocrlf input # 在wsl 中以windows格式拉取
./runall1.sh git pull

1.  抓包工具

ios:&#x20;

<https://blog.csdn.net/weixin_44504146/article/details/121946958>

Charles电脑抓包

<https://juejin.cn/post/7017013378291859486>

虚拟短信: d74c16d79e231cA011f498A389f4c67A

<https://sms-activate.org/cn/profile>
