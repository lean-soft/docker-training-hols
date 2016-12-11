练习一：使用docker基本命令
~~~~~~~~~~~~~~~~~~~~~~

实验准备

运行pullimage.cmd来拉取ubuntu和php-sample这2个容器镜像。

使用容器运行Hello World
^^^^^^^^^^^^^^^^^^^^^^^^

使用命令行运行以下命令，此命令将启动一个ubuntu的容器并在其中输出 Hello World文本，执行完毕后，容器自动退出。

.. code-block:: shell

    $ docker run ubuntu /bin/echo 'Hello world'
    Hello world

与容器进行交互
^^^^^^^^^^^^^^^^^^^^^^^^

使用命令行运行以下命令，此命令将启动一个ubuntu容器并在其中运行bash交互命令行界面，你可以尝试运行pwd，ls，ps等命令查看容器内环境，就如同远程操作一台服务器一样。

.. code-block:: shell

    $ docker run -t -i ubuntu /bin/bash
    root@af8bae53bdd3:/#

在容器中持续任务并管理容器生命周期
^^^^^^^^^^^^^^^^^^^^^^^^

使用命令行运行以下命令，此命令将启动一个ubuntu容器并在其中持续运行echo hello world，启动后容器会持续输出hello world文本。

.. code-block:: shell

    $ docker run -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"
    1e5535038e285177d5214659a068137486f96ee5c2e85a4ac52dc83f2ebe4147

注意当你运行以上命令后，命令行直接退出并没有持续输出hello world文本，这是因为我们使用了-d参数，这时容器在后台运行，你可以通过docker logs命令获取容器内的日志输出，注意替换c3a2为你的容器ID的前四位，如下：

.. code-block:: shell

    $ docker logs -f c3a2
    hello world
    hello world
    hello world
    hello world
    hello world
    hello world
    ... 

为了查看当前正在运行状态的容器，你可以使用docker ps命令，如下：

.. code-block:: shell

    $ docker ps
    CONTAINER ID        IMAGE               COMMAND                  CREATED                  STATUS              PORTS               NAMES
    c3a251374851        ubuntu              "/bin/sh -c 'while..."   Less than a second ago   Up 2 minutes                            evil_ride

你也可以查看到那些没有在运行状态的容器，使用docker ps -a命令，如下：

.. code-block:: shell

    $ docker ps -a
    CONTAINER ID        IMAGE               COMMAND                  CREATED                  STATUS                     PORTS               NAMES
    c3a251374851        ubuntu              "/bin/sh -c 'while..."   Less than a second ago   Up 6 minutes                                   evil_ride
    b6d4324edfbc        ubuntu              "/bin/bash"              Less than a second ago   Exited (0) 6 minutes ago                       small_beaver
    3363b0a14324        ubuntu              "/bin/echo 'Hello ..."   Less than a second ago   Exited (0) 7 minutes ago                       hungry_stonebraker

注意以上出了第一个容器正在运行意外，另外2个ubuntu容器都已经停止，但是容器仍然存在。你可以理解为他们是没有被运行中的应用，而应用的文件存在于你的docker环境中。

现在，你可以通过docker stop {id}命令来停止正在运行的容器，如下：

.. code-block:: shell

    λ docker stop c3a2
    c3a2

然后，通过docker rm {id}命令来删除所有未运行的容器，(注意将id替换成你自己的容器ID的前四位)如下：

.. code-block:: shell

    λ docker rm c3a2 b6d4 3363
    c3a2
    b6d4
    3363

也可以通过这个命令自动枚举所有容器并停止，删除：

.. code-block:: shell

    FOR /f "tokens=*" %i IN ('docker ps -a -q') DO docker stop %i
    FOR /f "tokens=*" %i IN ('docker ps -a -q') DO docker rm %i


运行web应用并通过浏览器访问
^^^^^^^^^^^^^^^^^^^^^^^^

使用命令行运行以下命令

.. code-block:: shell

    $ docker run -itd -p 8080:80 training/php-sample:5
    fbf9012502229877066ad5e63a1be5727055243857927a1d36ede432d7c3cc20

完成后打开浏览器并导航到 http://localhost:8080，你应该可以看到类似以下页面

.. figure:: images/docker-command-01-php-sample.png



