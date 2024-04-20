---
title:  Neo4j的学习笔记
category: Neo4j 
tags:学习笔记,Neo4j
---
[入门 欢迎来到 Neo4j](https://neo4j.com/docs/getting-started/ "Neo4j Cypher文档")

# Neo4j是什么

使用免费版云Neo4j Aura DB：[Start Free](https://neo4j.com/cloud/platform/aura-graph-database/?ref=nav-get-started-cta)

> Neo4j是一个可以存储节点（nodes）和它们之间的关系（relationships）的图数据库。
>
> 在关系数据库中，通常需要在查询中添加显式连接。在此过程中，需要在查询语句的运行时才会计算关系，这可能是一项昂贵而缓慢的操作。
> 在图（graph）中，关系直接被存储，许多强大的操作都更快更简单。

![1713593478513](image/2024-4-19-Neo4j-Cypher-Note/1713593478513.png)

## 节点、属性和关系(Node, properties, relationships)

**节点**和**关系**都可以有类型（type）和属性（properties）。

### 节点

> *节点*可以使用：labels标签 进行标记（例如Supplier，employee）也可以使用key-value对 进行标记（name : "Minato"）（我的理解是类似对象

![1713603754017](image/2024-4-19-Neo4j-Cypher-Note/1713603754017.png)

这里可以看出，节点拥有Label作为**节点标识**，同时拥有一系列properties。

### 关系

> *关系*对两个节点提供方向、类型化或归属性的连接。（e.g.Shipper SHIPS Order）
>
> 关系拥有确定的方向、类型、开始节点、结束节点和属性（可选）
>
> 节点可以有任意数量的关系，且性能不会受到影响。
>
> 开始节点和结束节点可以相同

![1713605098507](image/2024-4-19-Neo4j-Cypher-Note/1713605098507.png)

这里可以看出，关系拥有Label作为**关系标识**，拥有开始节点（from）和结束节点（to），拥有属性（可有可不有）

## 导入数据

构建完图结构后，可以将数据进行导入。

每个节点、关系都对应了一张csv表格。例如，Customer节点对应的 `customer.csv`的每一行就代表了一个顾客。

再比如，这里的product有多个关系，`SUPPLIES`和 `PART_OF`，分别连接 `Supplier`和 `Category`。

![1713605992279](image/2024-4-19-Neo4j-Cypher-Note/1713605992279.png)

但这两个关系都根据 `product.csv`进行构建

![1713606152589](image/2024-4-19-Neo4j-Cypher-Note/1713606152589.png)

在 `product.csv`中记录了Supplier和Category的id

![1713606126638](image/2024-4-19-Neo4j-Cypher-Note/1713606126638.png)


导入的Cypher语句示例（自动生成的，后面再专门对Cypher进行学习）

### Key statement

```
CREATECONSTRAINT`territoryID_Territory_uniq` IF NOTEXISTS
FOR(n:`Territory`)
REQUIRE(n.`territoryID`)ISUNIQUE;
```


### Load statement

```
UNWIND$nodeRecordsAS nodeRecord
WITH*
WHERENOT nodeRecord.`territoryID`IN$idsToSkipANDNOT nodeRecord.`territoryID`ISNULL
MERGE(n:`Territory`{`territoryID`: nodeRecord.`territoryID`})
SET n.`territoryDescription`= nodeRecord.`territoryDescription`;
```

## Explore the graph 探索图
