练习一：使用docker基本命令
~~~~~~~~~~~~~~~~~~~~~~

docker run - 使用容器运行Hello World
^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: shell

    $ docker run ubuntu /bin/echo 'Hello world'
    Hello world

docker run - 与容器进行交互
^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: shell

    $ docker run -t -i ubuntu /bin/bash
    root@af8bae53bdd3:/#

docker run - 在容器中运行服务
^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: shell
    $ docker run -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"
    1e5535038e285177d5214659a068137486f96ee5c2e85a4ac52dc83f2ebe4147

