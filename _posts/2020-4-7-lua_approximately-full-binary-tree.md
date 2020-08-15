---
layout:       post
title:        "Lua实现近似满二叉树"
subtitle:     "unity shader"
created:      '2020-06-03T06:33:32.035Z'
modified:     '2020-07-28T02:40:20.170Z'
author:       "CharlesZhong"
header-style: text
catalog:      true
...published:    false
tags:
    - Unity Shader
    - Shader
---

# Lua实现近似满二叉树

## 环境

## 原理
因为Lua有个table的存储数据的数据结构，所有的内容都是以这个为基准来设计高级的存储结构，是按照key-value的形式存储，而key可以是任意值（除了nil），也就是说可以设计出数组，也可以设计出字典的结构。

设计的数据结构在lua中为下面的表示

``` Lua
self.guess_recursion_list = 
{
  {{ }},
  {{ }, { }},
  {{ }, { }, { }, { }},
  {{ }, { }, { }, { }, { }, { }, { }, { }},
  {{ }, { }, { }, { }, { }, { }, { }, { }, { }, { }, { }, { }, { }, { }, { }, { }},
}
--[[
也就是相当于
self.guess_recursion_list = 
{
  [1] = {[1] = { }},
  [2] = {[1] = { }, [2] = { }},
  [3] = {[1] = { }, [2] = { }, [3] = { }, [4] = { }},
  [4] = {[1] = { }, [2] = { }, [3] = { }, [4] = { }, [5] = { }, [6] = { }, [7] = { }, [8] = { }},
  [5] = {[1] = { }, [2] = { }, [3] = { }, [4] = { }, [5] = { }, [6] = { }, [7] = { }, [8] = { }, [9] = { }, [10] = { }, [11] = { }, [12] = { }, [13] = { }, [14] = { }, [15] = { }, [16] = { }},
}
--]]
```

物理存储结构为：

``` mermaid
graph BT
31("[3][1]") & 32("[3][2]") --> 21("[2][1]")
33("[3][3]") & 34("[3][3]") --> 22("[2][2]")

21("[2][1]") & 22("[2][2]") --> 11("[1][1]")
```

最外层的[]内的为树的度degree，最里面的[]为树的顺序号，则在lua实际的实现中就是：

``` Lua
function CreateRecursionList(degree)
  if degree > 0 then
		local cur_degree = 1
		while(cur_degree <= degree) do
			local total_node_num = 1
			if cur_degree - 1 > 0 then
				for i = 1, (cur_degree - 1) do
					total_node_num = total_node_num * 2
				end
			end
			self.guess_recursion_list[cur_degree] = self.guess_recursion_list[cur_degree] or { }
			for i = 1, total_node_num do
				self.guess_recursion_list[cur_degree][i] = { }
			end
			cur_degree = cur_degree + 1
		end
	end
end

CreateRecursionList(5)
```

明显的看出里面的一些性质：
- 第i层节点数：&2^(i-1)&
- 第i层第j个节点的左孩子位置在（i+1）层中，位置为2*(j-1)+1
  第i层第j个节点的右孩子位置在（i+1）层中，位置为2*(j-1)+2
- i>=2时，第i层第j个节点的父节点判断的方法，判断当前节点是左孩子还是右孩子（判断奇偶，也就是判断j/2）
  - 当前是左孩子（奇数），则父节点为：(j-1)/2+1，在第（i-1）层
  - 当前是右孩子（偶数），则父节点为：(j-2)/2+1，在第（i-1）层

如果是用链表的形式去实现的话，也可以，但是下层的节点就不知道具体的位置

链表实现：

``` Lua
local BinaryTreeNode = { }
function BinaryTreeNode:SetData(data)
	self.data = data
end
BinaryTreeNode.__index = BinaryTreeNode
function BinaryTreeNode.New(node_parent, data)
	local temp = {
		lchild = { },
		rchild = { },
		data = data or { },
	}
	setmetatable(temp, BinaryTreeNode)
	return temp
end

-- 按照度来建立满二叉树
function CreateBinaryTree(degree)
	if degree and degree > 0 then
		self.guess_binary_tree_list = BinaryTreeNode.New()
		self:CreateBinaryTreeNode(self.guess_binary_tree_list, 1, degree)
	end
end

-- 创建满二叉树的左右子节点
function CreateBinaryTreeNode(binary_tree, cur_degree, max_degree)
	if binary_tree then
		if cur_degree > 0 and cur_degree < max_degree then
			binary_tree.lchild = BinaryTreeNode.New()
			binary_tree.rchild = BinaryTreeNode.New()
			self:CreateBinaryTreeNode(binary_tree.lchild, cur_degree + 1, max_degree)
			self:CreateBinaryTreeNode(binary_tree.rchild, cur_degree + 1, max_degree)
		end
	end
end

CreateBinaryTree(5)
```

最后实现的数据结构为：

``` Lua
-- 这里数量太多了  只写了两层
self.guess_binary_tree_list =
{
  lchild = 
  {
      lchild = 
      {
        lchild = {lchild = { }, data = { }, rchild = { }},
        data = { },
        rchild = {lchild = { }, data = { }, rchild = { }},
      },
      data = { },
      rchild = 
      {
        lchild = {lchild = { }, data = { }, rchild = { }},
        data = { },
        rchild = {lchild = { }, data = { }, rchild = { }},
      },
  },
  data = { },
  rchild = 
  {
      lchild = 
      {
        lchild = {lchild = { }, data = { }, rchild = { }},
        data = { },
        rchild = {lchild = { }, data = { }, rchild = { }},
      },
      data = { },
      rchild = 
      {
        lchild = {lchild = { }, data = { }, rchild = { }},
        data = { },
        rchild = {lchild = { }, data = { }, rchild = { }},
      },
  }
}
```

物理结构为：

``` mermaid
graph BT
31(lchild.lchild) & 32(lchild.rchild) --> 21(lchild)
33(rchild.lchild) & 34(rchild.rchild) --> 22(rchild)

21(lchild) & 22(rchild) --> 11(data)
```

<kbd>顺序存储</kbd>结构相比较于<kbd>链式存储</kbd>结构的特点：1可以知道每层所有节点的顺序，每一层的所有节点都是按照顺序放数据的；2可以更快求除了第一层外的某个节点的父节点。

## 问题

``` mermaid
graph BT
501 & 502 --> 41
503 & 504 --> 42
505 & 506 --> 43
507 & 508 --> 44
509 & 510 --> 45
511 & 512 --> 46
513 & 514 --> 47
515 & 516 --> 48

41 & 42 --> 31
43 & 44 --> 32
45 & 46 --> 33
47 & 48 --> 34

31 & 32 --> 21
33 & 34 --> 22

21 & 22 --> 11
```


现在有一个近似于上面的竞猜面板，会从多只队伍用一种规则筛选出16强，然后再筛选出8强，然后再筛选出4强，再筛选出2强，最后得到最后的冠军队伍，很明显，我们的数据是从下面开始记录起来的，而且每上一层的数据是下一层数据的两个中的其中一个，也就是下面的队伍501和队伍502，最后获胜队伍是501的话，那41的位置就是501，依次类推其他的节点获胜情况。

1.现在的问题就是我们需要像下面的图一样显示出数据来，但是服务器传来的数据为对阵信息，假设第一次的16强信息只有四个队伍进来，也就是：{ { A，B }，{ C，D } }

``` mermaid
graph BT
A & B --> 41(fa:fa-spinner)
C & D --> 42(fa:fa-spinner)
505(fa:fa-spinner) & 506(fa:fa-spinner) --> 43(fa:fa-spinner)
507(fa:fa-spinner) & 508(fa:fa-spinner) --> 44(fa:fa-spinner)
509(fa:fa-spinner) & 510(fa:fa-spinner) --> 45(fa:fa-spinner)
511(fa:fa-spinner) & 512(fa:fa-spinner) --> 46(fa:fa-spinner)
513(fa:fa-spinner) & 514(fa:fa-spinner) --> 47(fa:fa-spinner)
515(fa:fa-spinner) & 516(fa:fa-spinner) --> 48(fa:fa-spinner)

41(fa:fa-spinner) & 42(fa:fa-spinner) --> 31(fa:fa-spinner)
43(fa:fa-spinner) & 44(fa:fa-spinner) --> 32(fa:fa-spinner)
45(fa:fa-spinner) & 46(fa:fa-spinner) --> 33(fa:fa-spinner)
47(fa:fa-spinner) & 48(fa:fa-spinner) --> 34(fa:fa-spinner)

31(fa:fa-spinner) & 32(fa:fa-spinner) --> 21(fa:fa-spinner)
33(fa:fa-spinner) & 34(fa:fa-spinner) --> 22(fa:fa-spinner)

21(fa:fa-spinner) & 22(fa:fa-spinner) --> 11(fa:fa-spinner)
```

2.然后第二轮，传来8强数据：{ { A，B }，{ C，D }，{ A，C } }

``` mermaid
graph BT
A & B --> 41(A)
C & D --> 42(C)
505(fa:fa-spinner) & 506(fa:fa-spinner) --> 43(fa:fa-spinner)
507(fa:fa-spinner) & 508(fa:fa-spinner) --> 44(fa:fa-spinner)
509(fa:fa-spinner) & 510(fa:fa-spinner) --> 45(fa:fa-spinner)
511(fa:fa-spinner) & 512(fa:fa-spinner) --> 46(fa:fa-spinner)
513(fa:fa-spinner) & 514(fa:fa-spinner) --> 47(fa:fa-spinner)
515(fa:fa-spinner) & 516(fa:fa-spinner) --> 48(fa:fa-spinner)

41(A) & 42(C) --> 31(fa:fa-spinner)
43(fa:fa-spinner) & 44(fa:fa-spinner) --> 32(fa:fa-spinner)
45(fa:fa-spinner) & 46(fa:fa-spinner) --> 33(fa:fa-spinner)
47(fa:fa-spinner) & 48(fa:fa-spinner) --> 34(fa:fa-spinner)

31(fa:fa-spinner) & 32(fa:fa-spinner) --> 21(fa:fa-spinner)
33(fa:fa-spinner) & 34(fa:fa-spinner) --> 22(fa:fa-spinner)

21(fa:fa-spinner) & 22(fa:fa-spinner) --> 11(fa:fa-spinner)
```

3.最后假设A获胜，传来第三轮数据，就是4强数据：{ { A，B }，{ C，D }，{ A，C }，{ A，空 } }

``` mermaid
graph BT
A & B --> 41(A)
C & D --> 42(C)
505(fa:fa-spinner) & 506(fa:fa-spinner) --> 43(fa:fa-spinner)
507(fa:fa-spinner) & 508(fa:fa-spinner) --> 44(fa:fa-spinner)
509(fa:fa-spinner) & 510(fa:fa-spinner) --> 45(fa:fa-spinner)
511(fa:fa-spinner) & 512(fa:fa-spinner) --> 46(fa:fa-spinner)
513(fa:fa-spinner) & 514(fa:fa-spinner) --> 47(fa:fa-spinner)
515(fa:fa-spinner) & 516(fa:fa-spinner) --> 48(fa:fa-spinner)

41(A) & 42(C) --> 31(A)
43(fa:fa-spinner) & 44(fa:fa-spinner) --> 32(fa:fa-spinner)
45(fa:fa-spinner) & 46(fa:fa-spinner) --> 33(fa:fa-spinner)
47(fa:fa-spinner) & 48(fa:fa-spinner) --> 34(fa:fa-spinner)

31(A) & 32(fa:fa-spinner) --> 21(fa:fa-spinner)
33(fa:fa-spinner) & 34(fa:fa-spinner) --> 22(fa:fa-spinner)

21(fa:fa-spinner) & 22(fa:fa-spinner) --> 11(fa:fa-spinner)
```

4.最后因为队伍只剩一个队伍了，所以直接层层到最后的数据

``` mermaid
graph BT
A & B --> 41(A)
C & D --> 42(C)
505(fa:fa-spinner) & 506(fa:fa-spinner) --> 43(fa:fa-spinner)
507(fa:fa-spinner) & 508(fa:fa-spinner) --> 44(fa:fa-spinner)
509(fa:fa-spinner) & 510(fa:fa-spinner) --> 45(fa:fa-spinner)
511(fa:fa-spinner) & 512(fa:fa-spinner) --> 46(fa:fa-spinner)
513(fa:fa-spinner) & 514(fa:fa-spinner) --> 47(fa:fa-spinner)
515(fa:fa-spinner) & 516(fa:fa-spinner) --> 48(fa:fa-spinner)

41(A) & 42(C) --> 31(A)
43(fa:fa-spinner) & 44(fa:fa-spinner) --> 32(fa:fa-spinner)
45(fa:fa-spinner) & 46(fa:fa-spinner) --> 33(fa:fa-spinner)
47(fa:fa-spinner) & 48(fa:fa-spinner) --> 34(fa:fa-spinner)

31(A) & 32(fa:fa-spinner) --> 21(A)
33(fa:fa-spinner) & 34(fa:fa-spinner) --> 22(fa:fa-spinner)

21(A) & 22(fa:fa-spinner) --> 11(A)
```