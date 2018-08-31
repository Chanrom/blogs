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

使用两个指针可以解决不少的问题，特别是有关数组的题目上，这里从易到难介绍几道题目(从二分搜索到复杂一点的二分搜索33，34)。

# 链表问题
链表问题好像在面试中考察的不多，但是相关的题目却比较有技巧性，以下简单的做一些分析。

两个链表的第一个公共节点
-----

如果说可以借助其他数据结构的话，这道题目比较好解，比如借助栈来存储两个链表的元素，然后再从栈顶开始一一比对。如果不能借助其他数据结构呢（即空间复杂度为```O(1)```）？退一步考虑，如果两个链表有交点，那必要条件是从两个链表的末尾开始就相同，然后再往前的某个地方分开。因此，如果我们“对齐”两个链表的长度，从前往后扫描，如果在结束之前有相同节点，说明找到了第一个交点。
```python
  def getIntersectionNode(self, headA, headB):
        if not headA or not headB:
            return None
        
        def count(p):
            m = 0
            while p:
                m += 1
                p = p.next
            return m

        ma = count(headA)
        mb = count(headB)
        
        if ma < mb:
            ma, mb = mb, ma
            headA, headB = headB, headA

        n = ma - mb
        while n > 0:
            headA = headA.next
            n = n - 1
        while mb > 0:
            if headA == headB:
                return headA
            mb -= 1
            headA = headA.next
            headB = headB.next
        return None
```

反转链表
-----
有两种方法，非递归和递归的方法。
```python
    def reverseList(self, head):
        # 递归
        if not head:
            return 
        if not head.next:
            return head
        root = self.reverseList(head.next)
        head.next.next = head
        head.next = None
        return root

        # 以下为非递归
        # cur = head
        # prev = None
        # while cur:
        #     after = cur.next
        #     cur.next = prev
        #     prev = cur
        #     cur = after
        # return prev
```

链表中倒数第K个节点
-----
使用两个指针，第一个指针先走K个节点，然后第二个指针才开始和第一个指针一起走。当第一个指针到达链表尾端，第二个指针指向的节点就是倒数第K个节点。

判断一个单向链表是否形成了环形结构。
-----
也是定义两个指针，一个快指针一次走一步，一个慢指针一次走两步，如果快指针追赶到了慢指针说明有环，若快指针变成NULL则无环。



# 快速排序

快速排序是很基本的一种排序算法，在面试中经常被问道，而且很多题目也是基于快速排序的一个变形。这里给出一个基本的实现：
网上很多解法，通常是利用递归进行广度搜索：
```python
    def partition(a, l, r):
        flag = a[r]
        j = l - 1
        for i in range(l, r):
            if a[i] <= flag:
                j += 1
                a[i], a[j] = a[j], a[i]
        a[j + 1], a[r] = a[r], a[j + 1]
        return j + 1
    def Quicksort(a, l, r):
        if l < r:
            q = partition(a, l, r)
            Quicksort(a, l, q - 1)
            Quicksort(a, q + 1, r)
```















