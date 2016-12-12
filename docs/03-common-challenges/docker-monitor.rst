练习二：获取容器日志并对容器进行监控
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


01. 使用docker logs -f获取容器日志实时输出
^^^^^^^^^^^^^^^^^^^^^^^^

使用以下命令链接到之前创建的docker-machine上，并针对php-webapp:1这个容器执行docker logs -f {容器id}命令，然后你可以使用浏览器刷新这个应用并看到日志的实时输出。

.. code-block:: shell

    D:\docker-training
    λ @FOR /f "tokens=*" %i IN ('docker-machine env {主机名称}') DO @%i

    D:\docker-training
    λ docker ps
    CONTAINER ID        IMAGE                                        COMMAND                  CREATED             STATUS              PORTS
    NAMES
    2d6d22b5aa93        harbor.devopshub.cn/google/cadvisor:latest   "/usr/bin/cadvisor..."   7 minutes ago       Up 7 minutes        0.0.0.0:8080->8080/tcp
    cadvisor
    34f7b90869cb        localhost:5000/php-webapp:1                  "apache2-foreground"     About an hour ago   Up About an hour    0.0.0.0:81->80/tcp
    sleepy_heyrovsky
    95488ebcbeba        harbor.devopshub.cn/library/registry         "/entrypoint.sh /e..."   2 hours ago         Up 2 hours          0.0.0.0:5000->5000/tcp
    agitated_galileo
    c369822797c6        harbor.devopshub.cn/library/nginx            "nginx -g 'daemon ..."   2 hours ago         Up 2 hours          0.0.0.0:80->80/tcp, 443/tcp
    elated_pasteur

    D:\docker-training
    λ docker logs -f 34f7
    AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 172.17.0.4. Set the 'ServerName' directive globally to suppress this message
    AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 172.17.0.4. Set the 'ServerName' directive globally to suppress this message
    [Mon Dec 12 13:47:44.156252 2016] [mpm_prefork:notice] [pid 1] AH00163: Apache/2.4.10 (Debian) PHP/7.0.13 configured -- resuming normal operations
    [Mon Dec 12 13:47:44.156480 2016] [core:notice] [pid 1] AH00094: Command line: 'apache2 -D FOREGROUND'
    114.242.250.8 - - [12/Dec/2016:13:48:24 +0000] "GET / HTTP/1.1" 200 393 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36"

02. 使用google/cAdvisor对容器主机和应用进行监控
^^^^^^^^^^^^^^^^^^^^^^^^

在以上主机中运行如下命令部署google/cadvisor

.. code-block:: shell

    D:\docker-training\
    λ docker run --volume=/:/rootfs:ro --volume=/var/run:/var/run:rw --volume=/sys:/sys:ro --volume=/var/lib/docker/:/var/lib/docker:ro --publish=8080:8080 --deta ch=true --name=cadvisor harbor.devopshub.cn/google/cadvisor:latest
    time="2016-12-12T23:35:25+08:00" level=warning msg="Unable to use system certificate pool: crypto/x509: system root pool is not available on Windows"
    Unable to find image 'harbor.devopshub.cn/google/cadvisor:latest' locally
    latest: Pulling from google/cadvisor
    e110a4a17941: Pull complete
    e17fa94aae07: Pull complete
    cb2aa9e31624: Pull complete
    Digest: sha256:902449e23fc2cab1ca9788e2610b0928f27ee91e503f3326c7a463fa17d11dd9
    Status: Downloaded newer image for harbor.devopshub.cn/google/cadvisor:latest
    2d6d22b5aa9313382b9d63cbc3ab884b33acbb96cf21353169bfbcc1b892665b

部署完成后，使用浏览器导航到 http://{主机ip}:8080 ，可以看到如下页面，尝试对这台主机进行一些操作，如拉取镜像，启动和停止容器，可以看到监控数据的实时变化。

.. figure:: images/monitor-01-cAdvisor.png



