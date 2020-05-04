---
title: "Offer"
date: 2020-05-03T16:27:59+08:00
draft: false
---
@[TOC]
测试数据来源于GITHUB[剑指offer2](https://github.com/zhedahht/CodingInterviewChinese2)

##### 16 数值的整次方
**分治和位远算**

采用**分治**的方法，思想等同斐波那契数列，使用递归的思想写出循环的代码。
学到的小技巧，由于python的底层语言是c，使用**位运算**来提升代码的效率。

```python
def power(base,exp):
    if exp == 0:
        return 1
    if exp == 1:
        return base
    result = power(base,exp>>1)
    result *= result
    return result*base if exp&1==1 else result
```
使用位的**右移**来代替除2的操作，并使用**与操作**来判断`exp`是否为偶数。
在这个函数中默认`exp`为0或正数，我们在调用函数之前先对`exp`取绝对值，再得到结果都对其进行求倒操作。


##### 17 打印从1到最大的n位数
**全排列**
从1打印n位的最大数，尝试使用递归的方法对大数进行**全排列**。让代码简洁，思路上的创新就是观察到每位数的选择范围为0-9。
python3的最大数`maxsize`为：**9223372036854775807**
```python
def print1toMax(n):
    if n<=0:
        return
    number = [0 for _ in range(n)]

    for i in range(10):
        number[0] = i
        recursively(number,n,0)

def recursively(number,n,index):
    if index == n-1:
        #printstr(number)
        return

    for i in range(10):
        number[index+1] = i
        recursively(number,n,index+1)

def printstr(number):
    i = 0
    while i<len(number):
        if number[i]!=0:
            break
        i += 1
    a = number[i:]
    if a != []:
        print(int(''.join([str(t) for t in a])))

if __name__ == "__main__":
    start = time.time()
    print1toMax(7)
    end = time.time()
    print((end-start)*1000)
```
在书中说到，对于C++，如果题目给定n位整数但没有限定n的范围，则可看作大数问题。可以使用**字符串**来表示大数。
但在python中，应该是不用担心的，但字符串也可以尝试。
有意思的地方：[大数相乘](https://www.zhihu.com/question/28886029)

##### 18 删除链表的节点
**删除链表中的指定节点**
创新点：不删除该节点，而是**删除该节点的后一个节点**。
由于题目限制复杂度为O(1)，所以我们假定该节点一定在链表内。若没有该假定，则只能采取遍历的方法。

首先我们**使用python来创建一个链表**的结构。
```python
class ListNode():
    def __init__(self,value):
        self.value = value
        self.next = None
```
接着来实现我们的主函数：
```python
def drop(head,delnode):
    """
    1.该节点位与头结点，则直接将该链表置空
    2.该节点的下一个节点不为空，则删除后一个节点
    3.该节点位于尾节点，只能遍历
    """
    if delnode.next:
        nextnode = delnode.next
        delnode.value = nextnode.value
        delnode.next = nextnode.next

        nextnode.value = None
        nextnode.next = None

    elif head.next is None and head==delnode:
        head = None
        delnode = None

    else:
        phead = head
        while phead.next and phead.next!=delnode:
            phead = phead.next
        phead.next = None
        delnode = None
    return head
```

**删除链表中的重复节点**
在这道题中，默认将链表数值已排序，则重复值必相邻，考虑到重复值不止一个，加入内循环。核心依旧是链表的删除操作。
```python
def dropduplicate(head):
    if not head or not head.next:
        return head
    behead = ListNode(-1)
    behead.next = head
    cur = head
    be = behead

    while cur and cur.next:
        if cur.value == cur.next.value:
            val = cur.value
            while cur and cur.value == val:
                cur = cur.next
            be.next =cur
        else:
            be = cur
            cur = cur.next
    return behead.next

if __name__ == "__main__":
    h = ListNode(0)
    m = h
    for i in [1,2,2,3,3,3,3,3,3,6]:
        m.next = ListNode(i)
        m = m.next

    h = dropduplicate(h)
    while h:
        print(h.value)
        h = h.next
```

##### 19 正则表达式匹配
**面试题19**

考查对正则表达式的正确理解，边界条件的考虑以及代码的书写。
在书中，作者使用递归的方法实现该算法。复杂度为`O(n)`。这里我使用循环来实现。

需要考虑最外层的两个条件，即当前位的下一位是否为`*`：
1. 如果不为`*`：
	a. 如果当前位置与模式位置值相同，或者模式位置为`.`，且此时两个字符串都没有到达末尾，则都前进一步。
	b. 否则，直接返回`False`。
2. 如果为`*`：
	a. 如果当前位与模式位置值不同，则说明`*`没有显示，即直接对模式前进二步。
	b. 如果当前位置与模型位置值相同，则进入while循环，每次前进一步，判断当前位置直到与模式位置值不同。
3. 退出循环时，正确的结果则是模式和字符串都到达了末尾。
```python
def match(strs,pat):
    s,p = 0,0
    while s<len(strs) and p<len(pat):
        if p+1<len(pat) and (pat[p+1] == '*'):
            if strs[s]==pat[p] or (pat[p]=='.'):
                if pat[p]=='.':
                    return True
                else:
                    while s<len(strs):
                        if strs[s]==pat[p]:
                            s += 1
                        else:
                            break
            p += 2
        else:
            if (strs[s]==pat[p]) or (pat[p]=='.'):
                s += 1
                p += 1
            else:
                return False
    if s==len(strs) and p==len(pat):
        return True
    else:
        return False
```
递归版：（可以学习其递归实现的思想）
```python

def match1(string,pattern):
    if len(string)==0 and len(pattern)==0:
        return True
    if len(string)>0 and len(pattern)>0:
        if len(pattern)>1 and pattern[1]=='*':
            if string[0]==pattern[0] or pattern[0]=='.':
                return match1(string[1:],pattern[2:])
                	or match1(string[1:],pattern)
                	or match1(string,pattern[2:])

            else:
                return match1(string,pattern[2:])
        if string[0]==pattern[0] or pattern[0]=='.':
            return match1(string[1:],pattern[1:])
        return False
    return False
```

##### 20 表示数值的字符串
**使用条件判断快速得出结果**

做到目前为止，有一个很深的体会。作者会首先判断所有例子出现的可能性，按照场景划分，总结区分条件。
使用条件的逻辑判断**来全面覆盖测试集**和 **得出结果**。（目前总结的还不是很好，但这种思想真的要学会！）

在python中使用float函数可以直接进行类型检查和转换。这里就是要实现这么一个函数，所以不谈效率。

表示数值的字符串遵循模式$A[.[B]][e|EC]$ 或者 $.B[e|EC]$，其中：
$A$,$C$表示前面可能带有正负号的数字串，$A$表示整数部分，$C$表示指数部分。
$B$为小数点后的数字串，不带正负号。

1. 在小数里可能没有数值的整数部分，即`.32=0.32`。=>也就是说如果$A$为空，$B$必须要有值。或者 $A$有值，$B$也有值。
2. 在整数中没有数值的小数部分，即`32.=32`。=>也就是如果$A$不空，$B$都可以。

通过以上两个分析，我们可以知道$A$和$B$这两部分的存在是一个`或`的关系，只要有一个存在就可以。

3. 当`e|E`前面没有数字时，整个字符串都不能表示数值，即`.e1，e2`。
4. 当`e|E`后面没有数字，或者后面为小数时，也不能表示数值，即`12e，e1.2`。

通过以上两个分析，我们可以知道$C$和$e$前面的字符串的关系是一个`与`关系，必须同时存在。

那么符合以上模式的字符串，首先需要判断$A$是否存在符合要求的数值字符串，如果遇到小数点`.`，则判断是否存在符合要求的$B$；如果遇到`e|E`，则判断是否存在符合要求的$C$。
每次尽可能多的扫描0-9的数值字符串，由于我们对于小数点和指数符号都只允许存在一次，如果我们退出判断后，字符串没有到达最后一个字符，则说明该字符串不符合规则，直接退出。

```python
def isB(s,index):
    i = index
    while index<len(s) and s[index].isdigit():
        index += 1
    return index>i,index

def isAORC(s,index):
    if index<len(s) and (s[index]=='+' or s[index]=='-'):
        index += 1
    return isB(s,index)

def isNumeric(s):
    if len(s)<1:
        return False
    numeric,index = isAORC(s,0)

    if index<len(s) and s[index] == '.':
        index += 1
        tmp,index = isB(s,index)
        numeric = tmp or numeric

    if (index<len(s)) and (s[index] == 'e' or s[index] == 'E'):
        index += 1
        tmp,index = isAORC(s,index)
        numeric = tmp and numeric

    return numeric and len(s)==index


def Test(name,s,exp):
    if isNumeric(list(s)) == exp:
        print("PASS")
    else:
        print("Fail")

if __name__ == "__main__":
    Test("Test1", "100", True)
    Test("Test2", "123.45e+6", True)
    Test("Test3", "+500", True)
    Test("Test4", "5e2", True)
    Test("Test5", "3.1416", True)
    Test("Test6", "600.", True)
    Test("Test7", "-.123", True)
    Test("Test8", "-1E-16", True)
    Test("Test9", "1.79769313486232E+308", True)

    print("\n\n")

    Test("Test10", "12e", False)
    Test("Test11", "1a3.14", False)
    Test("Test12", "1+23", False)
    Test("Test13", "1.2.3", False)
    Test("Test14", "+-5", False)
    Test("Test15", "12e+5.4", False)
    Test("Test16", ".", False)
    Test("Test17", ".e1", False)
    Test("Test18", "e1", False)
    Test("Test19", "+.", False)
    Test("Test20", "", False)
```
注意每次都要判断当前索引是否到达了末尾，避免访问越界。

##### 21 调整数组顺序使奇数位于偶数前面
这道题很容易的想到快速排序，我们使用两个指针，`i`指针从前往后直到遇到偶，`j`指针从后往前直到遇到奇，然后奇偶交换，直到`j<i`。
但在实现的过程中，书中讲到了一点就是程序的可扩展性，即程序的耦合。
当我们的题目不是奇偶数，而是能被3整除和不能被3整除的呢，是负数与非负呢？

**将判断函数与主逻辑函数分开，逻辑不变，判断移出，这样就很方便的根据我们的要求改变函数**
```python
def isEven(a):
    return True if a&1==0 else False

def swapstr(arr):
    if len(arr) <1:
        return []
    i,j = 0,len(arr)-1
    while j>i:
        #查找第一个偶数
        while i<len(arr) and not isEven(arr[i]):
            i += 1
        #查找第一个奇数
        while j>0 and isEven(arr[j]):
            j -= 1

        arr[i], arr[j] = arr[j], arr[i]

    arr[i], arr[j] = arr[j], arr[i]
    return arr

def Test(arrs):
    for arr in arrs:
        print(swapstr(arr))

if __name__ == "__main__":

    test1 = [1,2,3,4,5,6,7]
    test2 = [2,4,6,1,3,5,7]
    test3 = [1,3,5,7,2,4,6]
    test4 = [1]
    test5 = [2]
    test6 = []

    arrs = [test1,test2,test3,test4,test5,test6]
    Test(arrs)
```

##### 22 链表中倒数第K个节点
**鲁棒性**
在接下来的题中，书中介绍了何谓“鲁棒性”。
所谓的鲁棒性是指程序能够判断输入是否合乎规范要求，并对不符合要求的输入予以合理的处理。

在该题中，我在百度的一面中也被面试官问到了这个问题，但自己还是眼高手低了，要勤写代码！
在这里我们首先需要考虑的是，**这倒数第K个节点在不在链表中！**

```python
class ListNode():
    def __init__(self,value):
        self.value = value
        self.next = None

def findK(head,k):
    if k==0 or head==None:
        return None

    i, j, h, b =0, 0, head, head
    while h.next!=None and i<k-1:
        h = h.next
        i += 1

    while h.next!=None:
        h = h.next
        b = b.next

    return b if i+1==k else None

def printk(listnode,listnode2):
    if listnode != None:
        print("result:%d"%(listnode.value))
    else:
        print("result:%s"%('None'))
    if listnode2 != None:
        print("expected:%d"%(listnode2.value))
    else:
        print("expected:%s"%('None'))
def Test():
    p1 = ListNode(1)
    p2 = ListNode(2)
    p3 = ListNode(3)
    p4 = ListNode(4)
    p5 = ListNode(5)

    p1.next = p2
    p2.next = p3
    p3.next = p4
    p4.next = p5

    printk(findK(None,100),None)
    printk(findK(p1,10),None)
    printk(findK(p1,0),None)
    printk(findK(p1,3),p3)
    printk(findK(p1,1),p5)
    printk(findK(p1,5),p1)

if __name__ == "__main__":
    Test()
```
这里给出了一个扩展题。
##### 22 链表的中间节点
**一个链表解决不了的问题，那就两个链表**
如果链表节点个数为奇数个，则返回中间节点，如果节点总数是偶数，则返回任意一个节点。
通过以上的启发，我们不想遍历两次链表，同样使用两个指针，那么怎么操作呢，保证一个指针到达最终节点的时候，另一个指针恰好到达我们的中间节点。

```python
    def findmid(head):
        if head==None:
            return head
        i,j,p,q = 0,0,head,head
        while p.next!=None:
            p = p.next.next
            q = q.next

        return q
```

