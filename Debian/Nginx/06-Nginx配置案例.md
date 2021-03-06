# Nginx 配置案例

## 提供静态内容

> 一个重要的 Web 服务器任务是提供文件（例如图像或静态 HTML 页面）。

```text
我们将实现一个示例，根据请求，将从不同的本地目录提供文件：
    - 包含HTML文件的目  /data/www
    - 包含图像的目录   /data/images
- 这将需要编辑配置文件并在 http 块指令内设置 server 块指令，其中包含两个 location 块指令。
```

> 具体操作如下：

1. 首先，创建 `/data/www` 目录并将带有任何文本内容的 `index.html` 文件放入其中，然后创建 `/data/images` 目录并在其中放置一些图像。

2. 下来，打开配置文件。默认配置文件已包含几个 server 块指令的示例，主要是注释掉的。现在注释掉所有这些块并启动一个新的 server 块指令：

   ```conf
   http {
       server {
       }
   }
   ```

   ```text
   - 通常，配置文件可能包含几个 server 块指令 distinguished ，它们通过 listen 到 server names 。
   - 一旦nginx决定哪个 server 处理请求，它就会根据 server 块指令中定义的 location 指令的参数测试请求头中指定的URI。
   ```

3. 将以下 location 块指令添加到 server 块指令：

   ```conf
   location / {
       root /data/www;
   }
   ```

   ```text
   - 此 location 块指令指定与请求中的URI进行比较的“/”前缀。
   - 对于匹配请求，URI将添加到 root 指令中指定的路径，
        - 即：/data/www，以形成本地文件系统上所请求文件的路径。
   - 如果存在多个匹配的 location 块指令，则nginx选择具有最长前缀的块。
   - 上面的 location 块指令提供了最短的前缀，长度为1，因此只有当所有其他 location 块指令无法提供匹配时，才会使用此块。
   ```

4. 接下来，添加第二个 location 块指令：

   ```conf
   location /images/ {
       root /data;
   }
   ```

   > 它将匹配以 `/images/` 开头的请求（ `location /` 也匹配此类请求，但前缀较短）。

5. server 块指令的结果配置应如下所示：

   ```conf
   server {
       location / {
           root /data/www;
       }

       location /images/ {
           root /data;
       }
   }
   ```

   ```text
   - 这已经是服务器的工作配置，它可以侦听标准端口80，并且可以在本地计算机上访问 http://localhost/ 。
   - 为响应具有以 /images/ 开头的URI的请求，服务器将从 /data/images 目录发送文件。例如，
        - 响应 http://localhost/images/example.png 请求，
        - nginx将发送 /data/images/example.png 文件。
        - 如果此类文件不存在，nginx将发送指示404错误的响应。
    - URI不以 /images/ 开头的请求将映射到 /data/www 目录。例如，
        - 响应 http://localhost/some/example.html 请求，
        - nginx将发送 /data/www/some/example.html 文件。
   ```

6. 要应用新配置，请启动 nginx（如果尚未启动）或通过执行以下命令将 reload 信号发送到 nginx 的主进程：

   ```shell
   $ nginx -s reload
   ```

> 如果某些内容无法正常工作，您可以尝试在目录 /usr/local/nginx/logs 或 /var/log/nginx 中的 access.log 和 error.log 文件中找出原因。

## 设置简单代理服务器

> nginx 的一个常见用途是将其设置为代理服务器，这意味着服务器接收请求，将它们传递给代理服务器，从中检索响应，然后将它们发送给客户端。

```text
- 我们将配置一个基本代理服务器，它使用来自本地目录的文件处理图像请求，并将所有其他请求发送到代理服务器；
- 在此示例中，将在单个nginx实例上定义两个服务器。
```

1. 首先，通过向 nginx 的配置文件添加一个 server 块指令来定义代理服务器，其中包含以下内容：

   ```conf
   server {
       listen 8080;
       root /data/up1;

       location / {
       }
   }
   ```

   ```text
   - 这将是一个侦听8080端口的简单服务器（之前，自使用标准端口80以来尚未指定 listen 指令）并将所有请求映射到本地文件系统上的 /data/up1 目录。
   - 创建此目录并将 index.html 文件放入其中。请注意， root 指令放在 server 上下文中。
   - 当为请求提供服务而选择的 location 块指令不包含自己的 root 指令时，将使用这样的 root 指令。
   ```

2. 接下来，使用上一节中的服务器配置并进行修改使其成为代理服务器配置。

   > 在第一个 location 块指令中，将 proxy_pass 指令与参数中指定的代理服务器的协议，名称和端口放在一起（在我们的例子中，它是 http://localhost:8080 ）：

   ```conf
   server {
       location / {
           proxy_pass http://localhost:8080;
       }

       location /images/ {
           root /data;
       }
   }
   ```

3. 我们将修改第二个 location 块指令

   > 该块当前将带有 `/images/` 前缀的请求映射到 `/data/images` 目录下的文件，以使其与具有典型文件扩展名的图像请求相匹配。修改后的 location 块指令看起来像这样：

   ```conf
   location ~ \.(gif|jpg|png)$ {
       root /data/images;
   }
   ```

   ```text
   该参数是一个正则表达式，匹配以 .gif、.jpg 或 .png 结尾的所有URI：
       - 正则表达式应该以 ~ 开头；
       - 相应的请求将映射到 /data/images 目录。

   当nginx选择 location 块指令来提供请求时：
       - 它首先检查指定前缀的 location 指令，记住具有最长前缀的 location ，然后检查正则表达式；
       - 如果与正则表达式匹配，则nginx选择此 location ，否则，它会选择之前记住的那个。
   ```

4. 生成的代理服务器配置如下所示：

   ```conf
   server {
       location / {
           proxy_pass http://localhost:8080/;
       }

       location ~ \.(gif|jpg|png)$ {
           root /data/images;
       }
   }
   ```

   ```text
   - 此服务器将过滤以 .gif、 .jpg 或 .png 结尾的请求
       - 并将它们映射到 /data/images 目录（通过向 root 指令的参数添加URI）;
       - 并将所有其他请求传递给上面配置的代理服务器。
   - 要应用新配置，请将 reload 信号发送到nginx，如前面部分所述。
   ```

## 设置 FastCGI 代理

> nginx 可用于将请求路由到 FastCGI 服务器，这些服务器运行使用各种框架和 PHP 等编程语言构建的应用程序。

```text
- 使用FastCGI服务器的最基本的nginx配置包括使用 fastcgi_pass 指令而不是 proxy_pass 指令，以及 fastcgi_param 指令来设置传递给FastCGI服务器的参数。
- 假设可以在 localhost:9000 上访问FastCGI服务器。
    - 以上一节中的代理配置为基础，将 proxy_pass 指令替换为 fastcgi_pass 指令，
    - 并将参数更改为 localhost:9000 。在PHP中， SCRIPT_FILENAME 参数用于确定脚本名称， QUERY_STRING 参数用于传递请求参数。
    - 结果配置为：
```

```conf
server {
    location / {
        fastcgi_pass  localhost:9000;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param QUERY_STRING    $query_string;
    }

    location ~ \.(gif|jpg|png)$ {
        root /data/images;
    }
}
```

> 这将设置一个服务器，该服务器将除静态图像请求之外的所有请求路由到通过 FastCGI 协议在 localhost:9000 上运行的代理服务器。
