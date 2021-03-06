---
title: 红黑树
date: 2016-08-14 15:34:04
updated: 2016-08-19 10:54:27
categories: [Learning, Tree]
tags: [红黑树]
---

# [红黑树](http://www.jianshu.com/p/210c2f4ca130)

> [在线演示](http://sandbox.runjs.cn/show/2nngvn8w)

红黑树是一棵自平衡的排序二叉树，满足二叉树的基本性质：树中任何节点的值大于左子节点，小于右子节点。

<!-- more -->

## 五个规则

红黑树通过颜色的约束来维持二叉树的平衡，一棵有效的红黑树应满足一下五个规则：

1. 节点的颜色是红色或黑色的；
2. 根节点是黑色的；
3. 红色节点的两个子节点必须是黑色的；
4. 所有叶子节点（树尾端NIL指针）必须是黑色的；
5. 任意节点到叶子节点的所有路径包含相同数量的黑色节点。

添加或删除节点维持红黑树平衡的三个动作：**左旋**，**右旋**，**着色**。

**左旋：**

![](http://7xt0u5.com1.z0.glb.clouddn.com/691c800b-c73e-4308-b920-a2814e63b51a.png)

**右旋：**

![](http://7xt0u5.com1.z0.glb.clouddn.com/29a6e54e-c51a-4ea9-9879-f3ea8e5e8898.png)


## 添加节点

添加节点时，共有如下四种情况：

**1. 父节点为左子节点，叔叔节点为红色**

![](http://7xt0u5.com1.z0.glb.clouddn.com/0276095b-e89c-4231-925a-8ce95f2edf7b.png)

**2. 父节点为左子节点，叔叔节点为黑色**

![](http://7xt0u5.com1.z0.glb.clouddn.com/63046423-8883-4c1d-ace1-5f8d41fcac4d.png)

**3. 父节点为右子节点，叔叔节点为红色**

![](http://7xt0u5.com1.z0.glb.clouddn.com/673ac674-f761-453b-9210-8fc2df1296f5.png)

**4. 父节点为右子节点，叔叔节点为黑色**

![](http://7xt0u5.com1.z0.glb.clouddn.com/5ddaaf03-5577-48b8-a5dc-ea3a46f2e124.png)


## 删除节点

### 删除节点X

删除节点X时，又分为如下3个步骤：

1. 若节点X无子节点，直接删除即可，且红黑树不需要进行平衡操作
2. 若节点X有一个子节点，子节点替换X后，再进行平衡操作
3. 若节点X左右子节点均不为null，找到替代X删除的节点M，将M的值赋给X（注意，X的颜色不变），而M就变成了真正待删除的节点，删除M之后再判断M是否有子节点，有则进行平衡操作。找M的规则如下：

```java
// 右子树[的最左子节点]
M = X.right;
while(M.left != null)
    M = M.left;
```

![](http://7xt0u5.com1.z0.glb.clouddn.com/d8b76800-d009-425d-a32f-c29fdcff3ab6.png)

删除节点X（或M）之后，就可以平衡红黑树了，设删除的节点是M，N是M的唯一子节点：

1. 若M是红色，删除之后不会破坏红黑树的规则
2. 若M是黑色，删除之后子树N将少了一个黑色节点
    1. 若N为红色，将N染黑即可
    2. 若N为黑色，则需要进行如下5种情况的调整

### 平衡红黑树

#### 5种情况

总的调整思路是增加子树N上的黑色节点，5种情况主要依据**父节点P**、**兄弟节点B**以及**B的子节点**的颜色而划分。（下图中x是新替换的节点，sib是x的兄弟节点）

**1. 兄弟节点B为红色（父节点P和子节点L、R必为黑色）**

进行如下转化后，红黑树仍然未平衡，子树N上缺少一个黑色节点，依据x的新兄弟节点L的左右子节点，**左右全黑**进入第3种，**左红右黑**进入第4种，**右红**进入第5种。

![](http://7xt0u5.com1.z0.glb.clouddn.com/2c72ca28-0b09-48c2-8f5c-7c2d46b9be9b.png)

**2. 兄弟节点B为黑色，父节点P为黑色，B的子节点均为黑色**

如下的转化是将P的右子树也减少了一个黑色节点，红黑色P总体上就少了一个节点，所以需要将P看做N继续进行调整。

![](http://7xt0u5.com1.z0.glb.clouddn.com/1fa5714c-c891-4f68-b8e6-967db587dd3d.png)

**3. 兄弟节点B为黑色，父节点P为红色，B的子节点均为黑色**

替换父节点和兄弟节点的颜色即可达到平衡。

![](http://7xt0u5.com1.z0.glb.clouddn.com/cdf05ad2-9902-4f15-ba47-6dcce49b0c44.png)

**4. 兄弟节点B为黑色（父节点P随意），B的左子节点L为红色，B的右子节点为黑色**

调整之后，红黑树未平衡，但转为了**第5种**情况。

![](http://7xt0u5.com1.z0.glb.clouddn.com/7a58d38e-ad5c-4838-9fcd-53286eebc2a4.png)

**5. 兄弟节点B为黑色（父节点P随意），B的左子节点L随意，B的右子节点为红色**

![](http://7xt0u5.com1.z0.glb.clouddn.com/9f3f99fa-a0d2-4e39-99cf-983a53b5b352.png)

#### 平衡流程

![](http://7xt0u5.com1.z0.glb.clouddn.com/52bb97be-0144-4593-8d23-cf0fbfab7cb6.png)


