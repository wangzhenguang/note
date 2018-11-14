

#### set
显示shell中所有变量


#### 定义变量
变量=值（没有空格）
 
 unset 变量 撤销
 
 readonly 变量=值 这种定义不能撤销
 
 export 变成全局变量
 
 
 ### 将命令的返回值赋给变量
 a=`ls -la` 
 a=$(ls -la)
 
 ### shell中的特殊变量
 $? 上一个命令的返回值
 $$ 表示当前进程编号
 $0 表示当前脚本名称
 $n 表示n位置的输入参数（n代表数字，n>=1)
 $# 表示变量的个数，常用语循环
 $*、$@ 表示参数列表
 `
  当$*被"" 包含时，$*会把参数当做一个整体  $@保持不变 
 `
 
 ### 运算符
 
 ```shell
  expr `expr 2+3\*4`
  echo `expr \`expr 2+3\`\*4`
  
  $(((2+3)*4)) 
 ```

 ### for
 
 for I in 123
 do
    echo $I
 done
 
 for I in 123;do echo $I;done
 for I in {1..3};do echo $I;done
 
 for((i=0;i<=3;i++))
 do 
    echo $i
 done
 
 for((i=0;i<=3;i++));do echo $i;done
 
 ### while
```
while 表达式
do 
command 
done


int=1
while((int<3))
do 
echo $int
let int++
done
 

```

#### case语句
```shell
case $I in 
start)
    echo "start"
    ;;
stop)
    echo "stop"
    ;;
*)
    echo "default"
esac

```

### read
read -p (提示语句) -n(字符个数) -t(等待时间)

read -p "please enter a number :" num

### if 判断语句
```
if [ condition ] 
    then 
        statements
    elif [ condition ]
        then 
            statements
    else 
        statements
fi

[ condition ] && echo Ok || echo not ok
```

= 字符串比较
-lt 小于
-le 小于等于
-eq 等于
-gt 大于
-ge 大于等于
-ne 不等于
-r 读权限
-w 写权限
-x 有执行的权限
-f 文件存在并且是一个常规的文件
-s 文件存在且不为空
-d 文件存在并是一个目录
-b 文件存在并且是一个块设备
-L 链接

### 函数
function fun(){
    echo fun
    return 0 
}

### 调试
sh -vx xxx.sh

### 文本处理
----

#### cut

cut -d "patter" -f index

```
echo $PATH
/opt/local/bin:/opt/local/sbin:/usr/local/mysql/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin

# 取出第一个
echo $PATH | cut -d ':' -f 1 

#取出第二个以后的
echo $PATH | cut -d ':' -f 2-

# 取第一个到底3个
echo $PATH | cut -d ':' -f 1-3

# 取第一个到第三个，和第五个
echo $PATH | cut -d ':' -f 1-3,5


```


#### sort
```
# 以':'分割 第3列排序 字符排序
sort -t ':' -k 3

# 以':'分割 第3列第一个字符排序 字符排序
sort -t ':' -k 3.1

# 按数字排序
sort -t ':' -k 3n

# 去重
sort -t ':' -k 7 -u
 

```


#### uniq

uniq可以取出排序过的文件中的重复行


``` 
uniq [-icu]
-i :忽略大小写字符
-c : 进行计数显示
-u : 只显示唯一的行

cat testfile | sort | uniq

```

#### wc
wc -l /etc/password #统计行数
wc -w #统计单词出现的次数
wc -m 统计文件的字符数 


### sed todo
 替换


### last

```
last -n 5 最近登录的信息 取5行

last -n 5 | awk '{print $1}' 打印第一列

# 按 : 分割 打印1，7列
cat /etc/passwd | awk -F ':' '{print $1"\t"$7}'

# 打印表头 表尾
cat /etc/passwd | awk -F ':' 'BEGIN {print "name,shell"}' '{print $1"\t"$7}' END {print "blue,/bin/nosh"

```

### curl 
-d http post方式传送数据
-F 模拟http表单提交
-c file 操作结束后把cookie写入到这个文件中
-D header信息写入文件中
-b string/file cookie字符串或文件读取位置
-o file  指定文件输出名


``` 
curl -D cookie -d 'username=test&password=123' 'http://www.xxx'
culr -b cookie -o download.file 'url'

```