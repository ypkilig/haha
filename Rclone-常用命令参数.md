# 设置

| 命令               | 说明                       |
| :----------------- | :------------------------- |
| rclone config      | 添加、删除、管理网盘等操作 |
| rclone config file | 显示配置文件的路径         |
| rclone config show | 显示配置文件信息           |

```
root@SunPma:~# rclone config
Current remotes:

Name                 Type
====                 ====
DB                 dropbox
GD                 drive

e) Edit existing remote
n) New remote
d) Delete remote
r) Rename remote
c) Copy remote
s) Set configuration password
q) Quit config
e/n/d/r/c/s/q>
```

# 语法

```
# 本地到网盘
rclone [功能选项] <本地路径> <配置名称:路径> [参数] [参数]

# 网盘到本地
rclone [功能选项] <配置名称:路径> <本地路径> [参数] [参数]

# 网盘到网盘
rclone [功能选项] <配置名称:路径> <配置名称:路径> [参数] [参数]

# [参数]为可选项
```

**示例**

```
# 复制到网盘，并显示实时传输进度，设置并行上传数为8

rclone copy -P /home/SunPma GD:/home/SunPma --transfers=8

# 如果需要服务端对服务端的传输可加以下参数（不消耗本地流量）

rclone copy 配置名称:网盘路径 配置名称:网盘路径 --drive-server-side-across-configs
```

# 功能

| 命令          | 说明                                                         |
| :------------ | :----------------------------------------------------------- |
| rclone copy   | 复制                                                         |
| rclone move   | 移动，如果要在移动后删除空源目录，加上 --delete-empty-src-dirs 参数 |
| rclone sync   | 同步：将源目录同步到目标目录，只更改目标目录                 |
| rclone size   | 查看网盘文件占用大小                                         |
| rclone delete | 删除路径下的文件内容                                         |
| rclone purge  | 删除路径及其所有文件内容                                     |
| rclone mkdir  | 创建目录                                                     |
| rclone rmdir  | 删除目录                                                     |
| rclone rmdirs | 删除指定环境下的空目录。如果加上 --leave-root 参数，则不会删除根目录 |
| rclone check  | 检查源和目的地址数据是否匹配                                 |
| rclone ls     | 列出指定路径下的所有的文件以及文件大小和路径                 |
| rclone lsl    | 比上面多一个显示上传时间                                     |
| rclone lsd    | 列出指定路径下的目录                                         |
| rclone lsf    | 列出指定路径下的目录和文件                                   |

# 参数

| 命令                                | 说明                                                         |
| :---------------------------------- | :----------------------------------------------------------- |
| -n = --dry-run                      | 测试运行，查看Rclon在实际运行中会进行哪些操作                |
| -P = --progress                     | 显示实时传输进度，500mS刷新一次，否则默认1分钟刷新一次       |
| --cache-chunk-size 5M               | 块的大小，默认5M越大上传越快，占用内存越多，太大可能会导致进程中断 |
| --onedrive-chunk-size 100M          | 提高OneDrive上传速度适用于G口宽带服务器（默认为320KB）       |
| --drive-chunk-size 64M              | 提高Google Drive上传速度适用于G口宽带服务器（默认为8M）      |
| --cache-chunk-total-size SizeSuffix | 块可以在本地磁盘上占用的总大小，默认10G                      |
| --transfers=N                       | 并行文件数，默认为4                                          |
| --config string                     | 指定配置文件路径，string为配置文件路径                       |
| --ignore-errors                     | 跳过错误                                                     |
| --size-only                         | 根据文件大小校验，不校验hash                                 |
| --drive-server-side-across-configs  | 服务端对服务端传输                                           |

# 日志

有4个级别的日志记录：`ERROR` `NOTICE` `INFO` `DEBUG`
默认情况下`Rclon`将生成`ERROR` `NOTICE`日志

| 命令              | 说明                                      |
| :---------------- | :---------------------------------------- |
| -q                | rclone将仅生成ERROR消息                   |
| -v                | rclone将生成ERROR NOTICE INFO 消息        |
| -vv               | rclone 将生成ERROR NOTICE INFO DEBUG 消息 |
| --log-level LEVEL | 标志控制日志级别                          |

**输出日志到文件**
使用`--log-file=FILE`选项`rclone`会将`Error` `Info` `Debug`消息以及标准错误重定向到`FILE`
这里的`FILE`是你指定的日志文件路径

# 过滤

| 命令      | 说明                                                         |
| :-------- | :----------------------------------------------------------- |
| --exclude | 排除文件或目录                                               |
| --include | 包含文件或目录                                               |
| --filter  | 文件过滤规则，相当于上面两个选项的其它使用方式。包含规则以+开头，排除规则以-开头 |

**文件类型过滤**
例如`--exclude "*.bak" --filter "- *.bak"`排除所有`bak`文件
例如`--include "*.{png,jpg}" --filter "+ *.{png,jpg}"`包含所有`png`和`jpg`文件，排除其他文件
例如`--delete-excluded`删除排除的文件。需配合过滤参数使用，否则无效
**目录过滤**
目录过滤需要在目录名称后面加上`/`否则会被当做文件进行匹配
以`/`开头只会匹配根目录（指定目录下），否则匹配所目录，这同样适用于文件
`--exclude ".git/"`排除所有目录下的`.git`目录
`--exclude "/.git/"`只排除根目录下的`.git`目录
`--exclude "{Video,Software}/"`排除所有目录下的`Video`和`Software`目录
`--exclude "/{Video,Software}/"`只排除根目录下的`Video`和`Software`目录
`--include "/{Video,Software}/**"`仅包含根目录下的`Video`和`Software`目录的所有内容
**大小过滤**
默认大小单位为`kBytes`但可以使用`k` `M`或`G`后缀
`--min-size`过滤小于指定大小的文件。比如`--min-size 50`表示不会传输小于`50k`的文件。
`--max-size`过滤大于指定大小的文件。比如`--max-size 1G`表示不会传输大于`1G`的文件。
**实际使用中发现大小过滤两个选项不能同时使用**
**过滤规则文件**
`--filter-from <规则文件>`从文件添加包含`/`排除规则
比如`--filter-from filter-file.txt`
**过滤规则文件示例：**

```
- secret*.jpg
+ *.jpg
+ *.png
+ file2.avi
- /dir/Trash/**
+ /dir/**
- *
```

# 环境变量

`rclone`中的每个选项都可以通过环境变量设置。环境变量的名称可以通过长选项名称进行转换，删除`--`前缀，更改`-`为`_`大写并添加前缀`RCLONE_`环境变量的优先级会低于命令行选项，即通过命令行追加相应的选项时会覆盖环境变量设定的值。
比如设置最小上传大小`--min-size 50`使用环境变量是`RCLONE_MIN_SIZE=50`当环境变量设置后，在命令行中使用`--min-size 100`那么此时环境变量的值就会被覆盖
**常用环境变量**

| 命令                          | 说明                                                         |
| :---------------------------- | :----------------------------------------------------------- |
| RCLONE_CONFIG                 | 自定义配置文件路径                                           |
| RCLONE_CONFIG_PASS            | 若 rclone 进行了加密设置，把此环境变量设置为密码，可自动解密配置文件 |
| RCLONE_RETRIES                | 上传失败重试次数，默认 3 次                                  |
| RCLONE_RETRIES_SLEEP          | 上传失败重试等待时间，默认禁用，单位s、m、h分别代表秒、分钟、小时 |
| CLONE_TRANSFERS               | 并行上传文件数                                               |
| RCLONE_CACHE_CHUNK_SIZE       | 块的大小，默认5M                                             |
| RCLONE_CACHE_CHUNK_TOTAL_SIZE | 块可以在本地磁盘上占用的总大小，默认10G                      |
| RCLONE_IGNORE_ERRORS=true     | 跳过错误                                                     |

# 转载链接

https://sunpma.com/864.html
