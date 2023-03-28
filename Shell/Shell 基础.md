# Shell 脚本基础

## 变量

赋值时不要带 `$` 符号，使用时需要带 `$` 符号。没有定义也可以使用，不会出错，按空字符串处理。

```sh
localName=xyz
echo $localName
xyz
echo $notExist
# 空行
```

注意 `=` 左右两侧不能有空格，右侧的值也不能加空格，如果加了空格，就会被认为是空格分隔的命令，可能得到这样的结果：

```sh
localName = xyz
bash: localName: command not found

localName= xyz
bash: xyz: command not found

localName=xyz 123
bash: 123: command not found
```

可以用双引号将值括起来，就可以在变量值内添加空格了。使用变量时，最好将变量用双引号括起来，否则其中的空格可能会变成参数分隔符。

```sh
localName=" xyz 123" # 注意字符串开头有个空格
echo $localName
xyz 123 # 并没有输出开头的空格
```

上例中，`echo $localName` 等价于 `echo  xyz 123`，相当于展开了变量，字符串开头的空格被用做了参数分割，而不是作为内容被输出。

```sh
localName=" xyz 123" # 注意字符串开头有个空格
echo "$localName"
 xyz 123 # 输出了开头的空格
```

使用变量时还可以加大括号 `${localName}`，大括号起到一个分隔的作用。

```sh
localName="Apple"
echo "$localNames"
# 输出空行，因为找不到一个名为 localNames 的变量
echo "${localName}s"
Apples
```

还可以使用单引号定义字符串，单引号不会将展开变量。

```sh
localName="Apple"
echo '$localNames'
$localNames # 直接输出了变量的标识符，而不是变量的值
```

变量类型都是字符串类型，无法直接进行数字计算。

```sh
x=1
y=2
echo $x+$y
1+2

echo "$x+$y"
1+2
```

## 分支语句

shell 分支语句的语法相比 C\Java\Python 等语言比较怪异，它们不使用花括号或缩进，而是使用一个回文词作为结束标识。语句中使用的布尔表达式也与众不同，有语法上的差异，也有很多针对文件的便捷方法。

### if

```sh
if condition
then statement(s)
else statement(s)
fi # if 的回文词作为结束标识
```

通常为了更美观，会写成下面这样

```sh
if condition; then
    statement(s)
else
    statement(s)
fi
```

其中 `condition` 后面的 `;` 是一行写多条语句的分割符号。如果多使用一些 `;` 还可以写成一行：

```sh
if condition; then statement; else statement; fi
```

例如

```sh
if [ '$name' == 'Jack' ]; then echo yes; else echo no; fi
```

还可以用 `elif` 来实现多分支逻辑，当然嵌套 if 语句也是可以的。

```sh
if ((1==2)); then
  echo A
elif ((3==3)); then
  echo B
else
  echo C
fi
```

等价于

```sh
if ((1==2)); then
  echo A
else
  if ((3==3)); then
    echo B
  else
    echo C
  fi
fi
```

### 条件表达式

紧跟 `if` 语句的 `condition` 实际上是就是普通的 shell 命令，这个命令的退出值就是 if 语句判断的布尔值。在 shell 编程中，这个退出值是一个整数，而整数和布尔值的映射并不是 0 对应 false，非 0 对应 true，与其他编程语言完全相反，返回 0 代表 true，返回 1 代表 false。

| shell 命令返回值 | 映射为 if 使用的布尔值 |
| :---------------: | :--------------------: |
|                0 | true                   |
|             非 0 | false                  |

通常情况下，一个程序返回 0 表示正常退出没有异常。这也是为什么通常我们在 C 代码的 `main` 函数中最后要写 `return 0;` 。

```c
int main() {
    ...
    return 0;
}
```

## test 和 []

`test` 是一个经常与 `if` 配合使用的命令，用来检测某个条件是否成立，它可以方便地检测数值、字符串和文件相关的条件。

```sh
test expression
```

`test` 命令也可以简写为 `[]`，上面的命令用 `[]` 简写为：

```sh
[ expression ]
```

注意 `[` 和 `]` 的两侧都必须有空格，否则连起来的内容就会被 shell 认为是一个标识符，进而产生各种奇怪的错误。

```sh
# if 后紧挨着 "["
if[$a == $b]; then echo yes; else echo no; fi
syntax error near unexpected token `then'
# "[" 后紧跟着数字
if [1 == 1 ]; then echo yes; else echo no; fi
[1: command not found
```

正确的用法，`[` 和 `]` 的两侧都加空格。

```sh
if [ 1 == 1 ]; then echo yes; else echo no; fi
yes
```

### 文件相关

| 选 项                   | 作 用                                                        |
| ----------------------- | ------------------------------------------------------------ |
| -b filename             | 判断文件是否存在，并且是否为块设备文件。                     |
| -c filename             | 判断文件是否存在，并且是否为字符设备文件。                   |
| -d filename             | 判断文件是否存在，并且是否为目录文件。                       |
| -e filename             | 判断文件是否存在。                                           |
| -f filename             | 判断文件是否存在，井且是否为普通文件。                       |
| -L filename             | 判断文件是否存在，并且是否为符号链接文件。                   |
| -p filename             | 判断文件是否存在，并且是否为管道文件。                       |
| -s filename             | 判断文件是否存在，并且是否为非空。                           |
| -S filename             | 判断该文件是否存在，并且是否为套接字文件。                   |
| -r filename             | 判断文件是否存在，并且是否拥有读权限。                       |
| -w filename             | 判断文件是否存在，并且是否拥有写权限。                       |
| -x filename             | 判断文件是否存在，并且是否拥有执行权限。                     |
| -u filename             | 判断文件是否存在，并且是否拥有 SUID 权限。                   |
| -g filename             | 判断文件是否存在，并且是否拥有 SGID 权限。                   |
| -k filename             | 判断该文件是否存在，并且是否拥有 SBIT 权限。                 |
| filename1 -nt filename2 | 判断 filename1 的修改时间是否比 filename2 的新。             |
| filename -ot filename2  | 判断 filename1 的修改时间是否比 filename2 的旧。             |
| filename1 -ef filename2 | 判断 filename1 是否和 filename2 的 inode 号一致，可以理解为两个文件是否为同一个文件。这个判断用于判断硬链接是很好的方法 |

### 数值比较

| 选 项         | 作 用                          |
| ------------- | ------------------------------ |
| num1 -eq num2 | 判断 num1 是否和 num2 相等。   |
| num1 -ne num2 | 判断 num1 是否和 num2 不相等。 |
| num1 -gt num2 | 判断 num1 是否大于 num2 。     |
| num1 -lt num2 | 判断 num1 是否小于 num2。      |
| num1 -ge num2 | 判断 num1 是否大于等于 num2。  |
| num1 -le num2 | 判断 num1 是否小于等于 num2。  |

### 字符串比较

| 选 项                    | 作 用                                                        |
| ------------------------ | ------------------------------------------------------------ |
| -z str                   | 判断字符串 str 是否为空。                                    |
| -n str                   | 判断宇符串 str 是否为非空。                                  |
| str1 = str2 str1 == str2 | `=`和`==`是等价的，都用来判断 str1 是否和 str2 相等。        |
| str1 != str2             | 判断 str1 是否和 str2 不相等。                               |
| str1 \> str2             | 判断 str1 是否大于 str2。`\>`是`>`的转义字符，这样写是为了防止`>`被误认为成重定向运算符。 |
| str1 \< str2             | 判断 str1 是否小于 str2。同样，`\<`也是转义字符。            |

### 逻辑运算

运算符可应用在  `test` 或 `[]` 内部：

| 运算表达式                 | 作 用                                                        |
| -------------------------- | ------------------------------------------------------------ |
| expression1 -a expression  | 逻辑与，表达式 expression1 和 expression2 都成立，最终的结果才是成立的。 |
| expression1 -o expression2 | 逻辑或，表达式 expression1 和 expression2 有一个成立，最终的结果就成立。 |
| !expression                | 逻辑非，对 expression 进行取反。                             |

使用 &&、|| 可用来连接两个命令，因此如果有两个条件判断，需要分别放在两个 test 或 [] 中：

| 命令                   | 作用                                                         |
| ---------------------- | ------------------------------------------------------------ |
| command1 && command2   | 先运行 command1，如果成功才会运行 command2，两个都成功整体才返回成功 |
| command1 \|\| command2 | 先运行 command1，如果失败才会运行 command2，两个都失败整体才返回失败 |



## [[]]

注意 `[[]]` 是 shell 关键字而不是命令，也是用来进行条件判断的，使用起来更加方便和灵活，可以替代 `test`。

```sh
[[ expression ]]
```

 `[[]]` 由于是关键字，因此没有传递参数的空格问题。

```sh
[[ -z $notExist ]]
```

但 `expression` 两侧的空格是不能省略的。

 `[[]]` 支持逻辑运算符，也就是可以直接把逻辑运算符写在里面，而不用像 `test` 那样写两个 `test` 命令。

```sh
[[ -z $str1 || -z $str2 ]]
```

 `[[]]` 支持正则表达式。

```sh
[[ str =~ regex ]]
```

正则表达式示例，判断字符串是否是手机号：

```sh
[[ $str =~ ^1[0-9]{10}$]]
```

## 脚本参数

### 基本使用

shell 中的参数都是没有名称的，需要使用固定的以 `$` 开头的标识符访问。

```sh
$0 # 表示脚本文件名
$N # 表示第N个参数
$# # 表示参数数量，不包括 $0
```

例，创建一个 test.sh 文件：

```sh
#!/bin/bash
echo "Shell 传递参数实例！共有参数 $# 个！";
echo "执行的文件名：$0";
echo "第一个参数为：$1";
echo "第二个参数为：$2";
echo "第三个参数为：$3";
```

然后执行：

```sh
./test.sh AA BB CC
```

输出：

```
Shell 传递参数实例！共有参数 3 个！
执行的文件名：./test.sh
第一个参数为：AA
第二个参数为：BB
第三个参数为：CC
```

### 遍历参数

在 shell 中既然没有参数列表，那么是否可以像 C 语言可变参数 `int sum(int count, ...)` ，或者像 Java 可变参数 `int sum(int... numbers)` 那样遍历访问呢？

当然可以，我们新建一个脚本文件 `every.sh`，遍历输出所有参数。

```sh
# every.sh
echo "$0: $#: $@"
for i in "$@"; do
    echo "  > {$i}" # 加上 {} 以便检查参数值的边界
done
```

执行 `every.sh` 文件，并传递一些参数。

```sh
$ ./every.sh AA BB CC
```

输出：

```sh
./every.sh: 3: AA BB CC
  > {AA}
  > {BB}
  > {CC}
```

### 整体传递

使用 `$*` 或 `$@` 可以整体将所有参数传递到另一个命令（函数）中，上一节的 `every.sh` 中已经使用了 `$@` 来遍历参数。而 `$*` 的特殊之处在于，在双引号中使用会将所有参数“平铺”为一个字符串。

将 `every.sh` 中的 `$@` 改为 `$*`：

```sh
#!/bin/zsh
echo "$0: $#: $*"
for i in "$*"; do
    echo "  > {$i}"
done
```

再次传入参数得到：

```sh
./every.sh: 3: AA BB CC
  > {AA BB CC}
```

第一行与之前 `$@` 的版本相同，而接下来只打印了一个将所有参数拼在一起的字符串，也就是说参数被合并为一个单独的字符串了。

整体传递参数最常见的用法是透传参数，即在一个脚本内将所有参数原封不动地传递给另一个脚本，我们举一个复杂的例子，新创建一个脚本 `test.sh` 放在 `every.sh` 的同级目录内，在 `test.sh` 内调用 `every.sh` 脚本。

```sh
# test.sh
./every.sh $*
./every.sh $@
./every.sh "$*"
./every.sh "$@"
./every.sh pre $* post
./every.sh pre $@ post
./every.sh "pre $* post"
./every.sh "pre $@ post"
```

运行 `test.sh` 脚本：

```sh
$ ./test.sh AA BB CC
```

得到结果(加了一些额外的输出)：

```sh
run every.sh with: $*
  ./every.sh: 3: AA BB CC
    > {AA}
    > {BB}
    > {CC}

run every.sh with: $@
  ./every.sh: 3: AA BB CC
    > {AA}
    > {BB}
    > {CC}

run every.sh with: "$*"
  ./every.sh: 1: AA BB CC
    > {AA BB CC}

run every.sh with: "$@"
  ./every.sh: 3: AA BB CC
    > {AA}
    > {BB}
    > {CC}

run every.sh with: pre $* post
  ./every.sh: 5: pre AA BB CC post
    > {pre}
    > {AA}
    > {BB}
    > {CC}
    > {post}

run every.sh with: pre $@ post
  ./every.sh: 5: pre AA BB CC post
    > {pre}
    > {AA}
    > {BB}
    > {CC}
    > {post}

run every.sh with: "pre $* post"
  ./every.sh: 1: pre AA BB CC post
    > {pre AA BB CC post}

run every.sh with "pre $@ post"
  ./every.sh: 3: pre AA BB CC post
    > {pre AA}
    > {BB}
    > {CC post}
```

可见，如果只是直接透传所有参数，直接使用不带引号的 `$@` 和 `$*` 都是可以的，如果带了引号，那么应该使用 `"$@"`。如果要额外添加参数，根据最后一个例子的奇怪输出，最好不要把所有参数写到一个双引号内，而是要分开写。

#### 另一种输出所有参数的方法

```sh
function every() {
    local params=""
    for i in $*; do
        params="${params}$i,"
    done
    last=$((${#params}-1))
    echo "$0 [$#] (${params:0:${last}})"
}
every AA BB CC
```

输出类似 Python 语言风格的参数列表

```sh
every [3] (AA,BB,CC)
```

