# Graph 简介

GraphQL 是一种新的 API 标准，它为 REST 提供了更高效、更强大和更灵活的替代方案。

## 出现的背景

在过去的十年中，REST 基本已经成为设计 Web API 的标准，它提供了一些很棒的想法，例如无状态服务器和对资源的结构化访问， 但是他却也有一些显而易见的问题。下面将以一篇博客的展位为例， 来说明这些点。

### 数据获取对比

已知需要实现一个博客应用， 此应用中，程序需要显示特定用户，以及其的帖子标题。此外还需显示该用户的最后 3 个关注者的姓名。

#### REST 接口实现

使用RESTAPI，您通常会通过访问多个端点来收集数据。在本例中，可以使用/users/<id>端点来获取初始用户数据。 其次，可能存在一个/users/<id>/posts端点，它为用户返回所有帖子。第三个端点将是/users/<id>
/followers， 它返回每个用户的跟随者列表, 大致过程如下图
![image](../images/restful.png)

##### 目前遇到的问题举例，比如(数据遇到的，星光遇到的)

问题汇总:

1. 挂载点过多， 维护成本增加，且加大网络传输
2. 针对不同的需求需要重新实现功能接口， 比如在增加字段的情形下， 可能需要修改接口
3. 对于特点接口， 返回的数据可能包含不必要的数据

#### GraphQL 接口实现

在GraphQL中，只需向GraphQL服务器发送一个包含具体数据需求的查询。然后，服务器用满足这些要求的JSON对象进行响应。
![image](../images/graphql.png)

问题：

1. 需要前端用户对后端数据模型比较了解。 好在GraphQL通常会暴露已有能力， 供指定用户查看支持的数据模型

## 应用示例

## 核心概念

1. [Query and resolver](https://graphql.org/learn/queries/)  
   代表有哪些资源可以查询， 以及如何查询

2. [Schemas and Types](https://graphql.org/learn/schema/)  
   代表可以从资源中获取的对象，以及它有哪些字段。  
   e.g.
   ```text
    type Character {
    name: String!
    appearsIn: [Episode!]!
    }
   ```

## 组织架构

![image](../images/graphql-location.png)


