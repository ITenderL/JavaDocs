# 基于Docker学习ElasticSearch

## 1. 环境搭建

### 1.1. 安装ElasticSearch

```bash
# 拉取镜像
docker pull elasticsearch:7.6.0
# 运行es容器
docker run -d --name itender_es -p 9200:9200 -p 9300:9300 -e ES_JAVA_OPTS="-Xms512m -Xmx512m" -e "discovery.type=single-node" elasticsearch:7.6.0
# 查看
curl http://localhost:9200
http://172.18.169.102:9200/
```

### 1.2.安装kibana

```bash
# 拉取镜像
docker pull kibana:7.6.0
# 运行容器
docker run -d --name=itender_kibana -p 5601:5601 kibana:7.6.0
```

### 1.3.Kibana连接ES

#### 1.3.1. 普通方式

```bash
# 进入kibana容器
docker exec -it 82d41cee7e64 bash
# 进入config目录
cd config
# 修改kibana.yml文件
vi kibana.yml

server.name: kibana
server.host: "0"
elasticsearch.hosts: [ "http://172.18.169.102:9200" ] # ip为es的宿主机ip
xpack.monitoring.ui.container.elasticsearch.enabled: true

# 退出容器，重启容器
docker restart 'kibana容器'

```

#### 1.3.2. 目录挂载方式

```bash
 # 复制容器中kibana.yml 到宿主机的/elk/kibana/文件夹下
 docker cp '82d41cee7e64容器名':'/usr/share/kibana/config/kibana.yml容器的文件目录' /elk/kibana/
 # 重新运行kibana容器，目录挂载的方式
 docker run -d --name=kibana -p 5601:5601 -v /elk/kibana/kibana.yml:/usr/share/kibana/config/kibana.yml kibana:7.6.0
 
```

### 1.4. Docker-compose安装

#### 4.1. 安装docker-compose

```bash
# 第一种方式：安装docker-compose
sudo curl -L https://github.com/docker/compose/releases/download/1.16.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
# 第二种方式：安装docker-compose
sudo curl -L https://get.daocloud.io/docker/compose/releases/download/1.25.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
# 添加可执行权限
sudo chmod +x /usr/local/bin/docker-compose
# 查看是否安装成功
```

#### 4.2. 创建docker-compose文件

```yml
version: "2.2"
volumes:
  data:
  config:
  plugin:
networks:
  es:
services:
  elasticsearch:
    image: elasticsearch:7.6.0
    ports:
      - "9200:9200"
      - "9300:9300"
    networks:
      - "es"
    environment:
      - "discovery.type=single-node"
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    volumes:
      - data:/usr/share/elasticsearch/data
      - config:/ust/share/elasticsearch/config
      - plugin:/ust/share/elasticsearch/plugins
  kibana:
    image: kibana:7.6.0
    ports:
      - "5601:5601"
    networks:
      - "es"
    volumes:
      - /elk/es-kibana-compose/kibana.yml:/usr/share/kibana/config/kibana.yml
```

```yml
server.name: kibana
server.host: "0"
elasticsearch.hosts: [ "http://elasticsearch:9200" ]
xpack.monitoring.ui.container.elasticsearch.enabled: true
```

#### 4.3. 启动es和kibana

```bash
# 启动es和kibana
docker-compose up -d
# 查看日志
docker logs -f "es容器"
docker logs -f "kibana容器"
```

### 1.5 ik分词器安装

修改docker-compose.yml文件，进行文件映射

```bash
# 现在ik分词器，这里要注意的是ik分词器的版本要与es版本保持一致
wget https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.6.0/elasticsearch-analysis-ik-7.6.0.zip
```

```bash
# 关闭容器
docker-compose down
```

修改docker-compose.yml

```bash
version: "2.2"
volumes:
  data:
  config:
networks:
  es:
services:
  elasticsearch:
    image: elasticsearch:7.6.0
    ports:
      - "9200:9200"
      - "9300:9300"
    networks:
      - "es"
    environment:
      - "discovery.type=single-node"
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    volumes:
      - data:/usr/share/elasticsearch/data
      - config:/usr/share/elasticsearch/config
      - /elk/es-kibana-compose/ik-7.6.0:/usr/share/elasticsearch/plugins/ik-7.6.0
  kibana:
    image: kibana:7.6.0
    ports:
      - "5601:5601"
    networks:
      - "es"
    volumes:
      - /elk/es-kibana-compose/kibana.yml:/usr/share/kibana/config/kibana.yml
```

## 2. 几个核心概念

### 索引 index

一个索引就是一个拥有几分相似特征的文档的集合。比如说，你可以有一个客户数据的索引，另一个产品目录的索引，还有一个订单数据的索引。一个索引由一个名字来标识（必须全部是小写字母的），并且当我们要对对应于这 个索引中的文档进行索引、搜索、更新和删除的时候，都要使用到这个名字。在一个集群中，可以定义任意多的索引。

### 映射 mapping

mapping是处理数据的方式和规则方面做一些限制，如某个字段的数据类型、默认值、分析器、是否被索引等等， 这些都是映射里面可以设置的，其它就是处理es里面数据的一些使用规则设置也叫做映射，按着最优规则处理数据对性能提高很大，因此才需要建立映射，并且需要思考如何建立映射才能对性能更好。 

### 文档  document

一个文档是一个可被索引的基础信息单元。比如，你可以拥有某一个客户的文档，某一个产品的一个文档，当然， 也可以拥有某个订单的一个文档。文档以JSON（Javascript Object Notation）格式来表示，而JSON是一个到处存在的互联网数据交互格式。在一个index/type里面，你可以存储任意多的文档。注意，尽管一个文档，物理上存在于一个索引之中，文档必须 被索引/赋予一个索引的type。 

### 字段 Field

相当于是数据表的字段，对文档数据根据不同属性进行的分类标识 

