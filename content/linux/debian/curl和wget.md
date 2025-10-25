# curl

Client for URLs，不仅可以下载文件，还可以上传文件、发送各种 HTTP 请求（GET、POST、PUT、DELETE 等）、测试 API、与各种网络服务交互。

下载文件：`curl -o <保存文件名> <url>` 或者 `curl -O <url>`(使用远程的文件名)

还有例如，POST请求，发送GET请求并传递参数，设置请求头，跟随重定向，显示详细信息，下载多个文件，使用代理等功能，但是还没用到，就先省略了

# wget

World Wide Web Get，主要用于从Web上下载文件，特别适合下载整个网站、递归下载和断点续传

下载文件：`wget -O <保存文件名> <url>` 或者 `wget <url>`(使用远程的文件名)

指定目录：`-P`

后台下载：`-b`

断点续传：`-c`

