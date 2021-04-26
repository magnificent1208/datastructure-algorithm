## 栈 Stack
### 1 定义与基本要素
#### 1.1 定义 
官方定义： 栈是限定仅在表尾插入和删除操作的线性表。
人话：栈也是一种线性结构，相比数组，栈只能从一端添加元素，也只能从一端取出元素。
	 把允许删除和删除的一端成为栈顶(top),另一端成为栈底(bottom)
#### 1.2 基本性质
栈是一种，FILO Last In First Out 后进先出的数据结构

#### 1.3 栈的作用
去掉数组下标等性质，使得可以关注相应问题的本身，可变性小。
应用：撤销操作。ctrl+z

### 2 实践
#### 2.1 有效的括号
![image-20210422091255572](C:\Users\maggie\AppData\Roaming\Typora\typora-user-images\image-20210422091255572.png)
思路1： 先将左括号压入栈，然后当前历遍元素与栈顶元素匹配。

```python
class Solution:
    def isValid(self, s: str) -> bool:
        stack = []
        for ch in s:
            #先判断是否左括号
            if ch in ['(','[','{']:
                stack.append(ch)
            else:
                # 如果是右括号，可能已经匹配成功。栈顶元素先扔出来。
                if stack:
                    left = stack.pop()
                else:
                    return false
                if left == '[' and ch == ']' or left == '(' and ch == ')' or left == '{' and ch == '}':
                    continue
                else:
                    return false

        if stack == []:
            return True
        else:
            return false
```

应用 设计一个有getMin功能的栈
实现一个特殊的栈，在实现栈的基本功能的基础上，在实现返回栈中最小元素的操作。
【要求】
1. pop，push，getMin操作的时间复杂度都是O(1)
2. 设计的栈类型可以使用现成的栈结构
回顾 ”栈顶“元素=>最底下的元素，”栈底“元素=>最外层元素。
思路1：压栈的时候后判断插入是空栈or小于当前栈顶元素的，就可以压入。
	 

