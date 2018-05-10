---
date: 2018-05-10
title: "Batch Normalization"
tags:
    - dnn
author:     "Chanrom"
catalog:    true
comment: true
mathjax : ture
fontsize: 5pt
---

# 介绍

```Batch Normalization```是很多深层网络会使用的一种训练```trick```，这篇博客主要想从谷歌的原始论文和一些大牛的博客出发，重新阐述一下这个训练深层网络的“大杀器”。

# The Insights

我们知道深度学习在图像、语言和文本等领域都取得了很大的进步。在这当中，随机梯度下降（```SGD```）作为一种有效的训练深层网络的方法而被很多人所熟知。在```SGD```的基础之上的一些改进优化算法，如```Adam```、```Adagrad```和```NAdam```等通常都能将深层网络训练到最佳的性能。


举个例子，假设我们要使用```SGD```来优化一个含有参数 \\( \Theta \\) 的网络：
$$
\Theta = argmin\_{\Theta} \frac{1}{N} \sum\_{i=1}^N l(x\_i, \Theta),
$$
每个```step```使用梯度下降来更新一次 \\( \Theta \\)，最后得到最优的值。每个```step```可以使用整个数据集来计算一次梯度，也可以使用一个样本来计算一次梯度，但这两种方法都有各自的缺点，所以一般使用的是批处理（```mini-batch```）随机梯度下降。**其优点在于**：1) \\( m \\) 个样本的梯度近似于整个数据集的梯度，相对来说梯度比较稳定，并且随着\\( m \\) 的增大，这种稳定性更好；2) 相比于利用单个样本去计算梯度，批处理的方式更高效。数学上看，```mini-batch```的方式每次使用 \\( m \\) 个样本计算一次梯度，其梯度为：
$$
\frac{1}{m} \frac{\partial l(x\_i, \Theta)}{\partial \Theta}.
$$


虽然随机梯度下降的方法简单高效，使得训练很深的网络也不在话下，但是想要使得深层网络获得更佳的性能却仍然不是一件容易的事情。实际上，想要深层网络训练的好，模型超参数、学习速率和模型参数的初始化方法的选择都至关重要。这些因素在网络变得更深的时候显得格外重要，因为底层网络参数一丁点的改变都将影响高层网络的输入。


在领域适应（```domain adaptation```）的研究当中，一个本质的问题就是某个学习系统的输入分布不是固定不变的（如训练样本的分布和测试样本的分布不一致会导致分类器的性能降低），这种情况称之为```covariate shift```。```Covariate shift```导致学习系统需要有额外的能力去适应输入分布的变化。结合之前提到的深层网络的训练问题，如果我们把一个深层网络的每一层全部分开来看，```covariate shift```的问题同样存在。假设某个网络有这样一个计算过程：
$$
l = F\_2(F\_1(u, \Theta\_1), \Theta\_2),
$$
\\( F \\) 指任意的函数变换。当我们学习参数\\( \Theta\_2\\) 的时候，可以将 \\( F\_2 \\) 的输入看做是 \\( v = F\_1(u, \Theta\_1) \\)：
$$
l = F\_2(v, \Theta).
$$
这样，一次梯度下降可以用如下计算方式：
$$
\Theta\_2 \leftarrow \Theta\_2 - \frac{\alpha}{m} \sum\_{i = 1}^m \frac{\partial F\_2(v\_i, \Theta\_2)}{\partial \Theta\_2}
$$
所以从上面的角度看待深层网络的训练时我们发现，如果输入 \\( v \\) 的分布是固定不变的，那么训练含有参数 \\(\Theta\_2 \\)的高层网络就会变得更加容易，也能训练的更好：在训练的过程中，\\(\Theta\_2 \\)不需要调整自己去“补偿”因为输入分布变化所带来的不利影响。


前面我们了解到深度网络某一层的输入保持固定不变的分布形式对该层的训练是非常有利的，实际上输入保持固定不变的分布还能为除该层之外的子网络带来益处。考虑深层网络的某一层使用的是```sigmoid```激活函数：
$$
z = g(W v + b),
$$
其中 \\( v \\) 是该层的输入，其他为参数。\\(g \\) 是```sigmoid```函数。随着 \\(|x|\\) 的增大，\\( g^\prime(x) \\) 都是趋近于0的。这就意味着如果\\( x = W v + b \\) 中某一维度的绝对值如果不能比较小的话，能够传给输入 \\( v \\) 的梯度就会```vanish```，进而训练就会变得很慢。但是呢，因为深层网络层数很多，这往往是不可避免的：在不断训练的过程中，梯度要么很大要么很小，所以到头来 \\(x \\) 还是慢慢进入到非线性函数的```saturated regime```，使得几乎没有梯度能够往底层的网络传播（```ReLU```）的设计就是为了缓解非线性激活函数梯度消失的这个问题）。那如果我们使得输入始终稳定为一个较稳定的分布，那么是不是有可能在训练的过程中降低激活函数陷入```saturated regime```的可能性，使得训练加快呢？


# Internal Covariate Shift
















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

字符串的翻转涉及到比较多的题目，比如，从最简单的字符串翻转的概念题到涉及回文串的题。总结一下这类题目对“启后”——在将来的面试中解决新的题目——还是很有帮助的。这里介绍几个这样的题目。


翻转字符串
-----
把一个字符串最左侧的若干字符截取下来，保持其顺序，放到原字符串截断后留下的子串前面，这个称之为翻转字符串。举个例子，如果```A="abcde"```，那么```B="deabc"```是```A```的一个翻转。如果判断一个字符串是不是另外一字符串的翻转呢？解题的思路就是，如果```B```是```A```的一个翻转字符串的话，```A```必须要在```(B + B)[1:-1]```中能找到，去掉最后和最前的字符是为了保证```A="aaaa"```，```B="aa"```的情况是不符合要求的。代码如下：
```python
    def rotateString(self, A, B):
        if len(A) == 0 and len(B) == 0:
            return True
        if len(A) == 0:
            return False
        s = (B + B)[1:-1]
        if A not in s:
            return False
        else:
            return True
```

# 排列组合

我这里的排列组合其实指的很宽泛，只要是涉及到计算若干个字符的排列组合、有效排列组合等等都可以归为这一类。把这些题目放到一起，以便总结。

括号生成
-----

给出 n 代表生成括号的对数，请你写出一个函数，使其能够生成所有可能的并且有效的括号组合。例如，给出 n = 3，生成结果为：
```
[
  "((()))",
  "(()())",
  "(())()",
  "()(())",
  "()()()"
]
```
我自己想到的方法是通过变换字符串中的```)(```这种组合。举例来说，串```()()()```的第一个```)(```变换成```()```则可以变成一个有效的括号串。
```python
    def generateParenthesis(self, N):
        valid_str = set(['()'*n])
        stack = ['()'*n]
        while len(stack) != 0:
            top = stack[len(stack) - 1]
            stack.pop()
            top = [c for c in top]
            for i in xrange(1, len(top) - 1):
                if top[i] == ')' and top[i + 1] == '(':
                    tmp = top[:]
                    tmp[i], tmp[i + 1] = tmp[i + 1], tmp[i]
                    if ''.join(tmp) not in valid_str:  
                        valid_str.add(''.join(tmp))
                        stack.append(''.join(tmp))
        return list(valid_str)
```
网上很多解法，通常是利用递归进行广度搜索：
```python
    def generateParenthesis(self, N):
        ans = []
        def backtrack(S = '', left = 0, right = 0):
            if len(S) == 2 * N:
                ans.append(S)
                return
            if left < N:
                backtrack(S+'(', left+1, right)
            if right < left:
                backtrack(S+')', left, right+1)
        backtrack()
        return ans
```


# 两个指针

使用两个指针可以解决不少的问题，特别是有关数组的题目上，这里从易到难介绍几道题目。
















