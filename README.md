# TINY-Web


### 内容分析
TINY-Web 只实现了 GET 请求，并且一次只能连接一个客户端。在获得 GET 请求后，会忽略掉所有请求头。然后判断 uri 是不是静态文件，返回对应的静态内容或动态内容。

如果要返回动态内容，则 Fork 一个子进程调用 CGI 程序，并且父进程阻塞直到子进程终止。

从功能上来看，这个程序只能算做是个理论上的东西。不能投产，只做演示。根据用户访问生成覆盖率信息


编译器：gcc 4.9

不统计覆盖率和启动代码覆盖率的区别（加 -lgcov -fprofile-arcs -ftest-coverage ），如下是启动代码覆盖率的方式：
```shell

gcc -lgcov -fprofile-arcs -ftest-coverage tiny.c csapp.c -o tiny 
cd cgi-bin
gcc -lgcov -fprofile-arcs -ftest-coverage  adder.c -o adder -I../
cd ..

./tiny 8888
```

然后用浏览器访问后查看覆盖率
```
http://localhost:8888/cgi-bin/adder?1&2 
```



```shell 


gcov-4.9 -b ./*.c
cd cgi-bin
gcov-4.9 -b ./*.c 
cd .. 
```

```shell

Usage: gcov [OPTION]... SOURCE|OBJ...

显示代码覆盖信息。

  -h, --help                      打印此帮助并退出
  -v, --version                    打印版本号并退出
  -a, --all-blocks                显示每个基本块的信息
  -b, --branch-probabilities      输出包含分支概率
  -c, --branch-counts             给出跳转的分支数，而不是百分比
  -n, --no-output                 不创建输出文件
  -l, --long-file-names           为包含进来的源文件使用长输出文件名
  -f, --function-summaries        输出每个函数的小结信息
  -o, --object-directory DIR|FILE 在 DIR 中搜索目标文件，或搜索名为 FILE 的目标文件
  -s, --source-prefix DIR         Source prefix to elide
  -r, --relative-only             Only show data for relative sources
  -p, --preserve-paths            保留所有路径名
  -u, --unconditional-branches    同时显示无条件跳转数
  -d, --display-progress          显示进度信息
  
```


上述生成的gcov 文件已经可以和sonar集成

sonar官方文档：
https://docs.sonarqube.org/latest/analysis/coverage/
https://docs.sonarqube.org/pages/viewpage.action?pageId=5312222



lcov -c -o collector.info -d .

读coverage.info大概覆盖率
lcov --list collector.info

生产html
genhtml collector.info -o collector_result

