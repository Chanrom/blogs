---
date: 2018-04-28
title: "Leetcode总结"
tags:
    - leetcode
author:     "Chanrom"
catalog:    true
comment: true
---

# Leetcode

一直知道自己的算法能力偏弱，但是却没有时间针对性地练习过相关的能力。趁着自己有想找一份暑期实习的计划下开始刷Leetcode，刚开始时，即使是easy的题目也解的比较费力，在刷到100题的时候，解easy题的速度明显快了很多。但是在面对medium难度的题依然要花很长的时间才可以AC。本着融会贯通、不停进步的思想，我将在这个博客记录、总结一下遇到的题目，以便自己以及有兴趣一起学习的同学参考。

目前网络上的Leetcode解题思路不计其数，我们可以随手搜到。但是我想只有在自己脑中形成一个知识体系，才可能在所遇到的新问题得到突破。因此这篇博客会尽量地分门别类地总结类似的题目。


# 二叉树的遍历

二叉树的遍历需要熟悉：前序遍历、中序遍历、后序遍历以及层次遍历，包括递归的实现和循环的实现。以下分别给出各个算法的Python实现。

前序遍历-递归
-----
递归的实现很简单：
```python
    def preorderTraversal(self, root):
        res = []
        def pre(root):
            if not root:
                return 
            res.append(root.val)
            pre(root.left)
            pre(root.right)
        pre(root)
        return res
```

前序遍历-循环
-----
相比递归实现，循环实现具有更高的效率，其实现需要借助栈来实现：
```python
    def preorderTraversal(self, root):
        stack = []
        res = []
        while (root or len(stack) != 0): # 注意这个终止条件
            if root:
                res.append(root.val)
                stack.append(root)
                root = root.left
            else:
                root = stack[len(stack) - 1]
                stack.pop()
                root = root.right
        return res
```


中序遍历-递归
-----
基本同上：
```python
    def inorderTraversal(self, root):
        res = []
        def pre(root):
            if not root:
                return 
            pre(root.left)
            res.append(root.val)
            pre(root.right)
        pre(root)
        return res
```

中序遍历-循环
-----
中序遍历的循环实现和前序遍历很类似，无非就是（出栈）打印的时机不一样。在代码里的体现就是```res.append(root.val)```：
```python
    def inorderTraversal(self, root):
        stack = []
        res = []
        while (root or len(stack) != 0): # 注意这个终止条件
            if root:
                stack.append(root)
                root = root.left
            else:
                root = stack[len(stack) - 1]
                res.append(root.val)
                stack.pop()
                root = root.right
        return res
```


后序遍历-递归
-----
递归很简单，直接给出代码：
```python
    def postorderTraversal(self, root):
        res = []
        def pre(root):
            if not root:
                return 
            pre(root.left)
            pre(root.right)
            res.append(root.val)
        pre(root)
        return res
```

后序遍历-循环
-----
相比前两个而言，后序遍历会比较复杂一点，因为在后序遍历中，必须最后打印根节点的值，也就是说必须先打印左子树和右子树的值。在具体的实现当中，我们可以使用一个```last```指针，表示在上一时刻访问多的节点。当某个节点没有左右子节点或者右子节点已经访问过了（此时左子节点肯定也访问过了），这个节点的值才可以被打印出来。以下代码：
```python
    def postorderTraversal(self, root):
        stack = []
        res = []
        last = None
        while (root or len(stack) != 0): # 注意这个终止条件
            while root: # 一直往左下搜索，使节点入栈
                stack.append(root)
                root = root.left
            root = stack[len(stack) - 1] # 开始分析栈内节点
            if (root.right and root.right != last): # 如果当前节点有右子节点并且没被访问过，又要开始对右子树的访问
                root = root.right
            else: # 不是以上情况的时候就可以输出当前根节点了
                res.append(root.val)
                stack.pop()
                last = root # 记录
                root = None # 保证下一次处理栈顶元素
        return res
```


层序遍历-循环
-----
第一种层序遍历要求的打印顺序是：根节点先打印叶子节点最后打印的。思路是每遍历完当前层所有的节点的时候，将当前层内节点拥有的子节点装入列表，以备下一步再开始之前的步骤：
```python
    def levelOrder(self, root):
        if not root:
            return []
        ans, level = [], [root]
        while level:
            ans.append([node.val for node in level])
            temp = []
            for node in level:
                temp.extend([node.left, node.right])
            level = [leaf for leaf in temp if leaf]
        return ans
```

层序遍历2-循环
-----
第一种层序遍历要求的打印顺序是和之前相反，但这个在Python中不是大问题，直接一条命令就可以```res[::-1]```



# 字符串翻转

字符串的翻转涉及到比较多的题目，总结一下其实有不少题目有相似之处或者说是承前或者启后的，