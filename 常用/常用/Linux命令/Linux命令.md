# Linux命令

```bash
~ Home Directory; # Superuser $ Regular User
Command [Options] [Parameters]

一。 目录处理命令
1. ls 
   -a All lists
   -l List details
   -h The way humans are used to looking
   -i serial number

2. CD [目录] Change Directory
    cd ~ 进入当前家目录
    cd  
    cd -  进入上次目录
    cd ..
    cd . 
    
 3. pwd:  print working directory
 4. mkdir  -p [目录】  
     -p  递归创建： mkdir -p  /opt/test
 5. rm -rf [文件或目录】
     -r 删除目录
     -f 强制
 6 cp [选项】 【原文件/目录】 【目标目录】
   -r 复制目录
   -p 连带文件属性复制 
   -d 若原文件是连接文件， 则复制链接属性
   -a 相当于 -pdr
 7 mv 【原文件/目录】 【目标目录】
 
 8 hexdump – 以多种进制格式查看文件内容
原文链接：https://www.linuxcool.com/hexdump
 9 查看ascii码
    sudo apt install ascii
    ascii:
    printf：打印查看 eg: printf %d 0x65  (十六进制65转十进制输出） printf %d \065  (八进制65转十进制输出） printf %c 0x65  (十六进制65转字符输出） printf  "abc=[%s]" 22 (输出字符abc=[22] ）
    
    
10. cenos 7 防火墙
  启动： systemctl start firewalld
  查看状态： systemctl status firewalld
  禁用，禁止开机启动： systemctl disable firewalld
  停止运行： systemctl stop firewalld

```

#### linux shell 编程

<https://juejin.cn/post/7271993000909373503>
