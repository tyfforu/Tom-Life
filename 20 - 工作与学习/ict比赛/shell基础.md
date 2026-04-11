
---

## **第一部分：Shell 脚本基础使用**

### 1. 什么是 Shell 脚本？
- **Shell**：用户与操作系统内核之间的命令行接口。
- **Shell 脚本**：包含一系列 Shell 命令的文本文件，用于自动化任务。

### 2. 第一个 Shell 脚本
```bash
#!/bin/bash
# 这是一个注释
echo "Hello, World!"
```
- `#!/bin/bash`：**Shebang**，指定解释器。
- `echo`：输出文本。

**运行方式**：
1. `chmod +x script.sh` → `./script.sh`
2. `bash script.sh`

---

## **第二部分：Shell 编程基础**

### 1. 变量
**定义与使用**：
```bash
name="Alice"
echo "Hello, $name"  # 输出：Hello, Alice
```
**注意**：
- 等号两边不能有空格。
- 使用变量时加 `$` 符号。

**环境变量**：
```bash
echo $HOME    # 家目录
echo $PATH    # 执行路径
```

### 2. 输入与输出
**读取输入**：
```bash
echo "Enter your name:"
read name
echo "Hello, $name"
```

**命令行参数**：
```bash
echo "第一个参数: $1"
echo "所有参数: $@"
echo "参数个数: $#"
```
运行：`./script.sh arg1 arg2`

### 3. 条件判断
**基本语法**：
```bash
if [ condition ]; then
  # 命令
elif [ condition ]; then
  # 命令
else
  # 命令
fi
```

**常用条件**：
- 字符串比较：`=`, `!=`
- 数字比较：`-eq`, `-ne`, `-gt`, `-lt`
- 文件测试：`-f` (存在且为文件), `-d` (目录), `-x` (可执行)

**示例**：
```bash
if [ $1 -gt 100 ]; then
  echo "大于100"
elif [ -f "$1" ]; then
  echo "文件存在"
fi
```

### 4. 循环
**for 循环**：
```bash
for i in 1 2 3; do
  echo "Number: $i"
done

# 遍历文件
for file in *.txt; do
  echo "处理文件: $file"
done
```

**while 循环**：
```bash
count=1
while [ $count -le 5 ]; do
  echo "Count: $count"
  ((count++))
done
```

### 5. 函数
**定义与调用**：
```bash
greet() {
  echo "Hello, $1"
}
greet "Alice"  # 调用函数
```

**返回值**：
- 使用 `return` 返回退出状态（0-255）。
- 使用 `echo` 返回字符串结果。

---

## **第三部分：Shell 编程实践**

### 1. 错误处理
**检查命令是否成功**：
```bash
if ! mkdir /new_dir; then
  echo "创建目录失败！"
  exit 1
fi
```

**调试脚本**：
- `bash -x script.sh`：显示每个命令的执行过程。

### 2. 字符串操作
```bash
str="Hello World"
echo ${#str}          # 长度
echo ${str:0:5}       # 子字符串
echo ${str/World/Bob} # 替换
```

### 3. 数组
```bash
fruits=("apple" "banana" "cherry")
echo ${fruits[1]}      # banana
echo ${fruits[@]}      # 所有元素
echo ${#fruits[@]}     # 数组长度
```

### 4. 实战示例

**示例 1：备份脚本**
```bash
#!/bin/bash
# 备份指定目录

src_dir=$1
backup_dir=$2
timestamp=$(date +%Y%m%d_%H%M%S)
backup_file="backup_$timestamp.tar.gz"

tar -czf $backup_dir/$backup_file $src_dir
echo "备份完成: $backup_file"
```

**示例 2：系统监控脚本**
```bash
#!/bin/bash
# 检查系统资源使用情况

echo "CPU 使用率: $(top -bn1 | grep "Cpu(s)" | awk '{print $2}')%"
echo "内存使用率: $(free | grep Mem | awk '{print $3/$2 * 100.0}')%"

# 检查磁盘使用率超过90%的分区
df -h | awk '$5 > 90 {print "警告: 分区 "$1" 使用率超过90%"}'
```

**示例 3：日志分析**
```bash
#!/bin/bash
# 统计日志文件中不同 HTTP 状态码的数量

log_file=$1
echo "HTTP 状态码统计:"
grep -o 'HTTP/1.[0-1]" [0-9][0-9][0-9]' $log_file | awk '{print $2}' | sort | uniq -c
```

---

## **第四部分：最佳实践**

1. **代码风格**：
   - 使用双引号包围变量：`"$var"`
   - 函数使用小写字母和下划线：`my_function`

2. **可移植性**：
   - 使用 `/bin/bash` 而不是 `/bin/sh`
   - 避免 Bash 特有特性（如需跨平台）

3. **安全性**：
   - 验证用户输入
   - 避免使用 `eval`

4. **文档**：
   - 在脚本开头添加描述、作者、版本信息

---

## **学习资源**

1. **在线教程**：
   - [Bash 指南](https://github.com/Idnan/bash-guide)
   - [Shell 脚本教程](https://www.shellscript.sh/)

2. **书籍**：
   - 《Linux命令行与Shell脚本编程大全》
   - 《Advanced Bash-Scripting Guide》

3. **练习平台**：
   - [LeetCode Shell 题库](https://leetcode.com/problemset/shell/)

---

通过系统学习这些内容并不断实践，你能够掌握 Shell 脚本编程，大大提高在 Linux/Unix 环境下的工作效率！