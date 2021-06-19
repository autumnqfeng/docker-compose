## Docker搭建kafka集群

1. 镜像信息：

   ```shell
   username@host docker_compose % docker images
   REPOSITORY                  TAG       IMAGE ID       CREATED       SIZE
   wurstmeister/kafka          latest    c3b059ede60e   11 days ago   507MB
   zookeeper                   3.4       4b03fe5b3f64   5 weeks ago   260MB
   sheepkiller/kafka-manager   latest    4e4a8c5dabab   3 years ago   463MB
   ```

2. 节点信息：

   ```shell
   username@host docker_compose % docker ps
   CONTAINER ID   IMAGE                       COMMAND                  CREATED          STATUS          PORTS                                                                                                      NAMES
   fce0cc61914c   sheepkiller/kafka-manager   "./start-kafka-manag…"   46 minutes ago   Up 46 minutes   0.0.0.0:9000->9000/tcp, :::9000->9000/tcp                                                                  kafa-manager
   76209c684286   wurstmeister/kafka          "start-kafka.sh"         46 minutes ago   Up 46 minutes   0.0.0.0:9093->9092/tcp, :::9093->9092/tcp, 0.0.0.0:9997->9999/tcp, :::9997->9999/tcp                       broker3
   7a1d9700529f   wurstmeister/kafka          "start-kafka.sh"         46 minutes ago   Up 46 minutes   0.0.0.0:9092->9092/tcp, :::9092->9092/tcp, 0.0.0.0:9998->9999/tcp, :::9998->9999/tcp                       broker2
   1d040c58d748   wurstmeister/kafka          "start-kafka.sh"         46 minutes ago   Up 46 minutes   0.0.0.0:9999->9999/tcp, :::9999->9999/tcp, 0.0.0.0:9091->9092/tcp, :::9091->9092/tcp                       broker1
   ee94838f37ef   zookeeper:3.4               "/docker-entrypoint.…"   46 minutes ago   Up 46 minutes   2888/tcp, 3888/tcp, 0.0.0.0:2183->2181/tcp, :::2183->2181/tcp, 0.0.0.0:8083->8080/tcp, :::8083->8080/tcp   zoo3
   21bead0aecfb   zookeeper:3.4               "/docker-entrypoint.…"   46 minutes ago   Up 46 minutes   2888/tcp, 0.0.0.0:2181->2181/tcp, :::2181->2181/tcp, 3888/tcp, 0.0.0.0:8081->8080/tcp, :::8081->8080/tcp   zoo1
   ead2103b5198   zookeeper:3.4               "/docker-entrypoint.…"   46 minutes ago   Up 46 minutes   2888/tcp, 3888/tcp, 0.0.0.0:2182->2181/tcp, :::2182->2181/tcp, 0.0.0.0:8082->8080/tcp, :::8082->8080/tcp   zoo2
   ```

   <img src="./README.assets/截屏2021-06-19 下午1.31.01.png" style="zoom:50%;" />

   3. 部署过程

      * 首先保证安装好docker、docker-compose

      * 下载当前 `docker-compose.yml` 

      * 创建集群所需的网络

        ```shell
        docker network create --driver bridge --subnet 172.18.0.0/25 --gateway 172.18.0.1 zoo_kafka
        ```

      * 查看docker网络信息

        ```shell
        username@host docker_compose % docker network ls
        NETWORK ID     NAME        DRIVER    SCOPE
        8946b76c2bf4   bridge      bridge    local
        e2eaa1eb843a   host        host      local
        b164fabef806   none        null      local
        b121d976130a   zoo_kafka   bridge    local
        ```

      * 执行启动命令

        ```shell
        docker-compose up -d
        ```

   4. 检查是否启动正常

      * 查看 `zoo-kafka` 网络信息

        ```shell
        username@host docker_compose % docker network inspect zoo_kafka
        [
            {
                "Name": "zoo_kafka",
                "Id": "b121d976130aa7ee3010a2f89f054941905af4267299a2b68d0b326613c67802",
                "Created": "2021-06-18T11:18:44.3916725Z",
                "Scope": "local",
                "Driver": "bridge",
                "EnableIPv6": false,
                "IPAM": {
                    "Driver": "default",
                    "Options": {},
                    "Config": [
                        {
                            "Subnet": "172.18.0.0/25",
                            "Gateway": "172.18.0.1"
                        }
                    ]
                },
                "Internal": false,
                "Attachable": false,
                "Ingress": false,
                "ConfigFrom": {
                    "Network": ""
                },
                "ConfigOnly": false,
                "Containers": {
                    "1d040c58d748c7bce3268eac2ecd07c23daec04e0f4706a26518f386edbdbe56": {
                        "Name": "broker1",
                        "EndpointID": "5a630ee3789a12f4f93596d24a34efdc97e6315f82e6d4973678ec29093c2519",
                        "MacAddress": "02:42:ac:12:00:0e",
                        "IPv4Address": "172.18.0.14/25",
                        "IPv6Address": ""
                    },
                    "21bead0aecfbd7c0b79f377481dff11116340ac5043987d1245a8eb425a38e84": {
                        "Name": "zoo1",
                        "EndpointID": "970996e6862aef001abea280844f6c686d1ba3cd810712e724205f4ddfaa2019",
                        "MacAddress": "02:42:ac:12:00:0b",
                        "IPv4Address": "172.18.0.11/25",
                        "IPv6Address": ""
                    },
                    "76209c6842862f08f6f400fd54ecd7965ea5c6103c07e2a256e903b1cacfd561": {
                        "Name": "broker3",
                        "EndpointID": "9f635719115847f4b4a4a7de16b87c2bba08ba9260a2fdcfa21ff29afa2c9bd0",
                        "MacAddress": "02:42:ac:12:00:10",
                        "IPv4Address": "172.18.0.16/25",
                        "IPv6Address": ""
                    },
                    "7a1d9700529fb91de06ec162bb8d8a0958b58331ea797808d39dc752671e48dc": {
                        "Name": "broker2",
                        "EndpointID": "bb2c0bb6c637b21186bbc3168c0858a4515992ebd4b641c79fd15047041db21e",
                        "MacAddress": "02:42:ac:12:00:0f",
                        "IPv4Address": "172.18.0.15/25",
                        "IPv6Address": ""
                    },
                    "ead2103b5198cbf5dcec8197590e6bd8e0d85bbb9e02dad56a8f6e2d2a23e2d4": {
                        "Name": "zoo2",
                        "EndpointID": "1a8fb1e02b227862a4eb135abe9d4a7e2bf156e82ee448d9b63899c5d0defa14",
                        "MacAddress": "02:42:ac:12:00:0c",
                        "IPv4Address": "172.18.0.12/25",
                        "IPv6Address": ""
                    },
                    "ee94838f37efcc9443a9ee991be282690ecc35763e06b269f991c97f6a19a033": {
                        "Name": "zoo3",
                        "EndpointID": "edcf43786bf46149803c2e6b1cb9ce1c42475b575ca35780b7b58b6941d8c6fa",
                        "MacAddress": "02:42:ac:12:00:0d",
                        "IPv4Address": "172.18.0.13/25",
                        "IPv6Address": ""
                    },
                    "fce0cc61914ca9b6dcd13ce05b7712eb03cc95596e2c079bcfeb500835beb340": {
                        "Name": "kafa-manager",
                        "EndpointID": "6c84a7ee2f1aa2121a94bb24b962990a95fff3296bde8bc5a622fe72558932c0",
                        "MacAddress": "02:42:ac:12:00:0a",
                        "IPv4Address": "172.18.0.10/25",
                        "IPv6Address": ""
                    }
                },
                "Options": {},
                "Labels": {}
            }
        ]
        ```

      * zookeeper

        ```shell
        .username@host docker_compose % echo stat | nc 127.0.0.1 2181
        Zookeeper version: 3.4.14-4c25d480e66aadd371de8bd2fd8da255ac140bcf, built on 03/06/2019 16:18 GMT
        Clients:
         /172.18.0.10:58630[1](queued=0,recved=252,sent=252)
         /172.18.0.1:58978[0](queued=0,recved=1,sent=0)
         /172.18.0.1:57824[1](queued=0,recved=1986,sent=1986)
        
        Latency min/avg/max: 0/0/8
        Received: 2299
        Sent: 2238
        Connections: 3
        Outstanding: 0
        Zxid: 0x1000000b9
        Mode: follower			# 节点状态
        Node count: 141
        
        # ==========================================================================================
        
        username@host docker_compose % echo stat | nc 127.0.0.1 2182
        Zookeeper version: 3.4.14-4c25d480e66aadd371de8bd2fd8da255ac140bcf, built on 03/06/2019 16:18 GMT
        Clients:
         /172.18.0.15:46292[1](queued=0,recved=766,sent=766)
         /172.18.0.1:55222[0](queued=0,recved=1,sent=0)
         /172.18.0.16:40744[1](queued=0,recved=586,sent=587)
         /172.18.0.10:42472[1](queued=0,recved=290,sent=292)
        
        Latency min/avg/max: 0/0/20
        Received: 1704
        Sent: 1645
        Connections: 4
        Outstanding: 0
        Zxid: 0x1000000b9
        Mode: follower			# 节点状态
        Node count: 141
        
        # ==========================================================================================
        
        username@host docker_compose % echo stat | nc 127.0.0.1 2183
        Zookeeper version: 3.4.14-4c25d480e66aadd371de8bd2fd8da255ac140bcf, built on 03/06/2019 16:18 GMT
        Clients:
         /172.18.0.14:43250[1](queued=0,recved=727,sent=732)
         /172.18.0.1:63738[0](queued=0,recved=1,sent=0)
         /172.18.0.10:51482[1](queued=0,recved=540,sent=598)
        
        Latency min/avg/max: 0/5/71
        Received: 1328
        Sent: 1330
        Connections: 3
        Outstanding: 0
        Zxid: 0x1000000b9
        Mode: leader			# 节点状态
        Node count: 141
        Proposal sizes last/min/max: 36/36/1013
        ```

      * kafka-manager中将zookeeper、kafka节点管理起来

        <img src="./README.assets/截屏2021-06-19 下午1.53.41.png" alt="截屏2021-06-19 下午1.53.41" style="zoom:70%;" />

      * kafka节点信息

        <img src="./README.assets/截屏2021-06-19 下午1.58.05.png" style="zoom:70%;" />

