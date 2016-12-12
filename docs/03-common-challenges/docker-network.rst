练习三：熟悉docker网络配置
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

01. 使用docker network 命令查看默认网络
^^^^^^^^^^^^^^^^^^^^^^^^

docker安装后会创建3个默认网络，可以通过docker network ls获取

.. code-block:: shell

    azureuser@docker-training-demo:~$ sudo docker network ls
    NETWORK ID          NAME                DRIVER              SCOPE
    ef1aabf2e9a8        bridge              bridge              local
    5c8fb60aae54        host                host                local
    0e4b98d9229b        none                null                local
    azureuser@docker-training-demo:~$

使用docker运行容器的时候回默认使用bridge(桥接)网络，我们可以通过以下命令看到当前连接到bridge网络的容器列表

.. code-block:: shell

    azureuser@docker-training-demo:~$ sudo docker network inspect bridge
    [
        {
            "Name": "bridge",
            "Id": "ef1aabf2e9a8978092882473b767cd317ae5eff13f4883ea6b74397e4c916521",
            "Scope": "local",
            "Driver": "bridge",
            "EnableIPv6": false,
            "IPAM": {
                "Driver": "default",
                "Options": null,
                "Config": [
                    {
                        "Subnet": "172.17.0.0/16",
                        "Gateway": "172.17.0.1"
                    }
                ]
            },
            "Internal": false,
            "Containers": {
                "2d6d22b5aa9313382b9d63cbc3ab884b33acbb96cf21353169bfbcc1b892665b": {
                    "Name": "cadvisor",
                    "EndpointID": "85c63cc892b64ab4cb347179e47ac4c0df8cf659f78129f15f4e46499c932a84",
                    "MacAddress": "02:42:ac:11:00:05",
                    "IPv4Address": "172.17.0.5/16",
                    "IPv6Address": ""
                },
                "34f7b90869cb3a70d25145ed5a8d183f04993baa65d2d90ea54b94ef4ae96a08": {
                    "Name": "sleepy_heyrovsky",
                    "EndpointID": "bf333aef7d10ab8383afce6baf1d09ab87a0350b0962fe80d4d71363c8971db6",
                    "MacAddress": "02:42:ac:11:00:04",
                    "IPv4Address": "172.17.0.4/16",
                    "IPv6Address": ""
                },
                "95488ebcbeba3b2cce47d946aa1fe87499fa82c51a02b26e13db92b8fcf7125b": {
                    "Name": "agitated_galileo",
                    "EndpointID": "9edd61d6d6252b92c4bb0fdb8fb94ff7f7babee0634fcc78c84255eb80f5a8ef",
                    "MacAddress": "02:42:ac:11:00:03",
                    "IPv4Address": "172.17.0.3/16",
                    "IPv6Address": ""
                },
                "c369822797c6da3b315587805702ff1157495f0e67c778592565f4f4c3837b10": {
                    "Name": "elated_pasteur",
                    "EndpointID": "5df25f5c9e0046ae898c567481b785514233b584c9934cace1eb3cffc813c73c",
                    "MacAddress": "02:42:ac:11:00:02",
                    "IPv4Address": "172.17.0.2/16",
                    "IPv6Address": ""
                }
            },
            "Options": {
                "com.docker.network.bridge.default_bridge": "true",
                "com.docker.network.bridge.enable_icc": "true",
                "com.docker.network.bridge.enable_ip_masquerade": "true",
                "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
                "com.docker.network.bridge.name": "docker0",
                "com.docker.network.driver.mtu": "1500"
            },
            "Labels": {}
        }
    ]
    azureuser@docker-training-demo:~$

02. docker0桥接网络工作机制
^^^^^^^^^^^^^^^^^^^^^^^^

通过运行 brctl show 和 bridge li 命令，我们可以看到默认的docker0网桥当前连接了4个不同的虚拟网卡，这4个虚拟网卡分别来自以上4个容器。

.. code-block:: shell

    azureuser@docker-training-demo:~$ brctl show
    bridge name     bridge id               STP enabled     interfaces
    docker0         8000.0242a0e02083       no              veth1e723b2
                                                            veth3ab9b8f
                                                            veth798aefc
                                                            vethcc9a5d0
    azureuser@docker-training-demo:~$ bridge li
    6: veth798aefc state UP @(null): <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 master docker0 state forwarding priority 32 cost 2
    8: veth3ab9b8f state UP @(null): <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 master docker0 state forwarding priority 32 cost 2
    10: veth1e723b2 state UP @(null): <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 master docker0 state forwarding priority 32 cost 2
    12: vethcc9a5d0 state UP @(null): <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 master docker0 state forwarding priority 32 cost 2

在运行 iptables --list -t nat 命令，你可以清晰的看到每个容器所映射的端口

.. code-block:: shell

    azureuser@docker-training-demo:~$ sudo iptables --list -t nat
    Chain PREROUTING (policy ACCEPT)
    target     prot opt source               destination
    DOCKER     all  --  anywhere             anywhere             ADDRTYPE match dst-type LOCAL

    Chain INPUT (policy ACCEPT)
    target     prot opt source               destination

    Chain OUTPUT (policy ACCEPT)
    target     prot opt source               destination
    DOCKER     all  --  anywhere            !127.0.0.0/8          ADDRTYPE match dst-type LOCAL

    Chain POSTROUTING (policy ACCEPT)
    target     prot opt source               destination
    MASQUERADE  all  --  172.17.0.0/16        anywhere
    MASQUERADE  tcp  --  172.17.0.2           172.17.0.2           tcp dpt:http
    MASQUERADE  tcp  --  172.17.0.3           172.17.0.3           tcp dpt:5000
    MASQUERADE  tcp  --  172.17.0.4           172.17.0.4           tcp dpt:http
    MASQUERADE  tcp  --  172.17.0.5           172.17.0.5           tcp dpt:http-alt

    Chain DOCKER (2 references)
    target     prot opt source               destination
    RETURN     all  --  anywhere             anywhere
    DNAT       tcp  --  anywhere             anywhere             tcp dpt:http to:172.17.0.2:80
    DNAT       tcp  --  anywhere             anywhere             tcp dpt:5000 to:172.17.0.3:5000
    DNAT       tcp  --  anywhere             anywhere             tcp dpt:81 to:172.17.0.4:80
    DNAT       tcp  --  anywhere             anywhere             tcp dpt:http-alt to:172.17.0.5:8080
    azureuser@docker-training-demo:~$

至此，你就可以理解docker的默认网络配置是如何完成容器的端口映射了。


03. 使用host网络
^^^^^^^^^^^^^^^^^^^^^^^^

host网络与bridge网络不同，它直接将容器暴露于主机之上，与在主机上直接运行一个进程一样，这种方式下我们无法从新映射容器的端口，如果容器本身使用80端口，那么运行这个容器就意味着主机的80端口被占用。

可以使用以下命令使用host模式运行容器。

.. code-block:: shell

    D:\docker-training
    λ docker run --network=host -itd php-webapp:1
    48b6ff6e82352a2231636b9c48ffc37ebd895f21ccb67d0e667054726d002d3f

    D:\docker-training
    λ docker ps
    CONTAINER ID        IMAGE               COMMAND                CREATED                  STATUS              PORTS                  NAMES
    48b6ff6e8235        php-webapp:1        "apache2-foreground"   Less than a second ago   Up 2 seconds                               quizzical_kirch

    D:\docker-training
    λ docker network inspect host
    [
        {
            "Name": "host",
            "Id": "dfb66a87b625dbc9c728a52c663714adabb9583265d491fb07abce405d498a34",
            "Created": "2016-12-11T23:50:47.1002637Z",
            "Scope": "local",
            "Driver": "host",
            "EnableIPv6": false,
            "IPAM": {
                "Driver": "default",
                "Options": null,
                "Config": []
            },
            "Internal": false,
            "Attachable": false,
            "Containers": {
                "48b6ff6e82352a2231636b9c48ffc37ebd895f21ccb67d0e667054726d002d3f": {
                    "Name": "quizzical_kirch",
                    "EndpointID": "ec983950d261fb9ccefcf16438f77988a961f6103151e29ada74b1afff967b45",
                    "MacAddress": "",
                    "IPv4Address": "",
                    "IPv6Address": ""
                }
            },
            "Options": {},
            "Labels": {}
        }
    ]


小结
^^^^^^^^^^^^^^^^^^^^^^^^

以上我们对docker的默认网络host和bridge进行了说明，并详细分析了docker0的bridge网络是如何提供端口转发能力的。使用docker0网桥让我们可以灵活的对已有容器的端口配置进行映射，这也让我们实现灵活部署中提供了很多方便，比如同时运行一个容器的多个实例并在他们中间实现负载均衡。

