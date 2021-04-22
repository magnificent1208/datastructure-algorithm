# 数组

## 1. 理论
定义：将元素顺序低放在一块连续的储存区里，元素间的顺序关系由他们的储存顺序自然表示。 
人话： 将元素码成一排进行存放。
特性：动态/静态设定&索引(索引可以有语义，也可以没有语义)。
### 1.1 基础
#### 1.1.1 索引没有语义，如何表示没有元素。
#### 1.1.2 如何添加元素，如何删除元素。
	保证数组不为空
	添加：插入的话，根据index从后往前移。
	删除：索引后往前覆盖，然后维护size。
### 1.2 动态数组
#### 1.2.1 动态数组时间复杂度分析
##### 1.2.1.1 添加操作 O(n)
		   Addlast() -- O(1) 与数据规模基本没有关系
		   Addfirst() --O(n) 与数据规模有关，因为要把所有的元素往后移一位
		   add(index,e) --O(n) 
##### 1.2.1.2 查询操作 O(n)
		   Get(index) --O(1) 挑到制定index返回值
		   Contains() --O(n)历遍所有数据
		   Find() --O(n) 历遍所有数据
#### 1.2.2 动态数组均摊复杂度
* 当最坏情况时间分析不适用时
* 最耗时的操作，并不是每次都触发，会间隔较长一段时间，那么这段耗时的操作
##### 1.2.3 复杂度争荡
	例如remove(), 增加一个判断，如果当前有效元素为总量的1/2且还存在有效元素，
	则将容量缩减为原来的一半。
### 1.3 其他基础结构（列表与字典的时间复杂度）

## 2. 实践-相向双指针
leetcode题型归纳：双指针

** 感觉每次都没读懂题目的意思，后续要加上自己对题目意思的理解&重点写出来。

### 2.1 两数之和

*只存在一个正确解

<img src="C:\Users\maggie\AppData\Roaming\Typora\typora-user-images\image-20210331102720782.png" alt="image-20210331102720782" style="float: left;"  />

#### 2.1.1 暴力解，双循环
```python
class Solution:
    def twoSum(self, nums, target):
        l = len(nums)
        for i in range(l-1):
            for j in range(i+1,l):
                if nums[i] + nums[j] == target:
                    return [i,j]
```
#### 2.1.2 哈希解法

```
class Solution:
    def twoSum(self, nums, target):
    	map = {} #空字典
         for i in range(len(nums)):
             if target - nums[i] not in map:
                 map[nums[i]] = i
             else:
                 return map[target- nums[i]], i
```
#### 2.1.3 双指针解法
```python
class Solution:
    def twoSum(self, nums, target):
        array =nums.copy()
        nums.sort()
        i, j = 0, len(nums) - 1
        while i < j:
            s = nums[i] + nums[j]
            if s < target: #如果数值不够大，则左指针往右移动，和增大 
                i += 1
            elif s > target:
                j -= 1
            else:
                break
        res = []
        for k in range(len(nums)):
            if array[k] == nums[i] or array[k] == nums[j]:
                res.append(k)
        return res
```

### 2.2 三数之和

重点1：不重复，无排序

![image-20210331103919041](C:\Users\maggie\AppData\Roaming\Typora\typora-user-images\image-20210331103919041.png)

#### 2.2.1  双指针法
思路1：遇到使用双指针的情况，先考虑是否需排序（本题目显然是需要的呀！）
思路2：在两数之和外再加一个循环 (黑话：单循环+双指针)
思路3：添加一个去重的操作
```
class Solution:
    def threeSum(self, nums: List[int]) -> List[List[int]]:
        nums.sort()
        res = []
        for i in range(len(nums)):
            #去重 避免i这个与相邻的数相等
            if i > 0 and nums[i] == nums[i-1]:
                continue
            # 双指针部分
            left = i + 1
            right = len(nums) - 1
            while left < right:
                if nums[i] + nums[left] +nums[right] < 0:
                    left += 1
                elif nums[i] + nums[left] +nums[right] > 0:
                    right -= 1
                # 遇到符合要求的数组，返回后
                elif nums[i] + nums[left] +nums[right] == 0:
                    res.append([nums[i], nums[left], nums[right]])
                    #去重 避免left或right指针的数跟旁边相同
                    while left < right and nums[left] == nums[left+1]:
                        left += 1 #出现这个情况直接右移
                    while left < right and nums[right] == nums[right-1]:
                         right -= 1 #出现这个情况直接左移
                    #继续循环
                    left += 1
                    right -= 1
        return res
```

### 2.3 最接近的三数之和
重点1： 找出最接近target的三个数
重点2：返回与target最接近的和(不是下标啦！)

![image-20210402083455950](C:\Users\maggie\AppData\Roaming\Typora\typora-user-images\image-20210402083455950.png)

#### 2.3.1 双只指针法
思路1： 记得数组排序！！
思路2：固定遍历nums[i]这个值
思路3：前后指针放在如3sum的位置，
	  设置一个变量a，储存nums和target之间的abs value，
	  然后根据sums = nums[i] + nums[left] + nums[right]
	  判断sums与target的距离，如果更接近则更新结果a。
思路4： sums > target 则 right-= 1， 如果 sums <  target 则 left+= 1

```python
class Solution:
    def threeSumClosest(self, nums: List[int], target: int) -> int:
        # sort
        nums.sort()
        # init
        a = abs(nums[0] + nums[1] +nums[2] -target)
        res = nums[0] + nums[1] +nums[2]

        for i in range(len(nums)):
            left = i +1 
            right = len(nums) -1

            while left < right:
                sums = nums[i] + nums[left] + nums[right]
                # 情况1 不停找最接近的和
                if abs(sums - target) < a:
                    a = abs(sums - target)
                    res = sums
                # 双指针常见操作了
                if sums > target:
                    right -= 1
                elif sums < left:
                    left += 1

                # 情况2 如果相等，直接返回
                elif sums == target:
                    return sums
        return res
```

### 2.4 四数之和
重点1： 四个值与target相等
重点2：不重复的四元组
重点3：可以是同样的元素，不同的顺序
![image-20210406160506224](C:\Users\maggie\AppData\Roaming\Typora\typora-user-images\image-20210406160506224.png)

#### 2.4.1 相向指针法
具体操作：
##### 1. 双重循环分别枚举前两个数，然后再用双指针枚举剩下的两个数。假设双重枚举到的前两个数分别位于下标i和j，其中i<j.
##### 2. 初始，左右指针分别为下标j+1&下标n-1，（有序数组，枚举剩下的两个数）
##### 3. 每次计算四个数的和，判断：
###### 3.1 如果sum=target，则将枚举到的四个数加到答案中，然后双指针做常见操作。
###### 3.2 如果sum<target, 则将左指针右移一位。
###### 3.3 如果sum>target, 则将右指针左移一位。


剪枝操作：历遍不同的组合
1. 确定第一个数之后，如果nums[i] + nums[i+1]+nums[i+2]+nums[i+3] > target，说明在这个组合之下，剩下的三个数无论取什么值，sum一定大于target，因此退出第一重循环。
2. 在确定第一个数之后，如果nums[i] + nums[n-3]+nums[n-2]+nums[n-1] < target, 说明在这个组合之下，无论剩下的三个数取什么值， sum一定小于target，因此第一重循环直接进入下一轮，枚举nums[i+1]
3. 在确定前两个数之后，如果nums[i] + nums[n-3]+nums[n-2]+nums[n-1] > target, 说明在这个组合之下，无论剩下两个数无论取什么值，sum一定大于target，因此退出第二重循环。
4. 在确定前两个数之后，如果nums[i] + nums[j]+nums[n-2]+nums[n-1] < target, 说明在这个组合之下，无论剩下的两个数取什么值， sum一定小于target，因此第二重循环直接进入下一轮，枚举nums[j+1]

```
#四数之和
class Solution:
    def fourSum(self, nums: List[int], target: int) -> List[List[int]]:
        res = list()
        if not nums or len(nums) < 4:
            return res
        # 记得涉及双指针需要sort
        nums.sort()
        length = len(nums)
        for i in range(length - 3):
            if i > 0 and nums[i] == nums[i - 1]:
                continue
            if nums[i] + nums[i + 1] + nums[i + 2] + nums[i + 3] > target:
                break
            if nums[i] + nums[length - 3] + nums[length - 2] + nums[length - 1] < target:
                continue
            for j in range(i + 1, length - 2):
                if j > i + 1 and nums[j] == nums[j - 1]:
                    continue
                if nums[i] + nums[j] + nums[j + 1] + nums[j + 2] > target:
                    break
                if nums[i] + nums[j] + nums[length - 2] + nums[length - 1] < target:
                    continue
                left, right = j + 1, length - 1
                while left < right:
                    total = nums[i] + nums[j] + nums[left] + nums[right]
                    if total == target:
                        res.append([nums[i], nums[j], nums[left], nums[right]])
                        while left < right and nums[left] == nums[left + 1]:
                            left += 1
                        left += 1
                        while left < right and nums[right] == nums[right - 1]:
                            right -= 1
                        right -= 1
                    elif total < target:
                        left += 1
                    else:
                        right -= 1
        
        return res
```

总结：三数之和，就是固定一个数+双指针；四数之和，就是固定两个数+双指针；如此类推，100数之和都一样。

具体来说，本小节用的指针是相向双指针。

-----------------------------------------------------

### 2.5 盛最多水的容器

![image-20210408184345395](C:\Users\maggie\AppData\Roaming\Typora\typora-user-images\image-20210408184345395.png)

重点1：木桶原理，关注短板/边。
重点2：短板向长板移动的触发条件（height[i] < height[j]）
重点3：判断是否max()

```python
class Solution:
	def maxArea(self, height:List[int]) -> int:
        i, j, res = 0, len(height) - 1, 0 # 三个变量初始化
        while i < j : #经典的前后指针
            # 注意，现在这个双指针没有sort这一步了，所以要加上这个判断
            if height[i] < height[j]:
                res = max(res, height[i]*(j-i))
                i += 1
            else:
                res = max(res, height[j]*(j-i))
			   j -= 1
        return res
            
```

### 2.6 有效三角形的个数

![image-20210412183646725](C:\Users\maggie\AppData\Roaming\Typora\typora-user-images\image-20210412183646725.png)

重点1： 无顺序要求，所以先sort。然后找到(可能的)最长边。
重点2： 用类似“三数之和”的解法，固定最长边c。然后找后续符合三角形定义的剩下两条边可能的组合。用双指针在这个固定c的左边！左边！左边！左边！寻找。
重点3：然后找后续符合三角形定义的剩下两条边可能的组合,不要傻傻遍历。
但出现nums[left] + nums[right] > nums[i]的时候，等于程序已经找到临界点了，后续所有组合都是可以组成三角形的，这个时候nums[right]可以直接开始下一轮，使 right -= 1
重点4：看清楚要输出的东西是什么！！本题是让你计数，不要返回所有可能性!!别瞎

```python
class Solution:
    def triangleNumber(self, nums: List[int]) -> int:
        nums.sort() #先排序
        count = 0 # 最终输出是要计数计数计数
        for i in range(2, len(nums)): #前面两个数用来放两个指针就不用动了
            left, right = 0, i-1
            while left < right:
                if nums[left] + nums[right] > nums[i]:
                    #当达到这个临界点，后续的left指针再怎么右移，都能满足三角形的性质，所以直接返回right-left种可能
                    count += right - left
                    right -= 1
                #　if nums[left] + nums[right] < nums[i]:
                else:
                    left += 1
        return count
```

## 3 实践-同向双指针-快慢指针
### 3.1 算出有序数组中的重复项

![image-20210412192248957](C:\Users\maggie\AppData\Roaming\Typora\typora-user-images\image-20210412192248957.png)
step1：先sort，先sort，先sort，先sort
step2：定义快慢指针，slow只装不重复的项。fast用来遍历值，如果fast当前值不与[0,slow]重复，就放到slow里面。
重点1： slow指针左边的数不用管，只需要跟[0，slow]区间最右边的数字比较（因为是sort的数组，所以一定要记得sort）

```python
class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        i = 0
        j = 1
        # j 不能超出数组
        while j < len(nums):
            if nums[i] == nums[j]:
                j += 1
            else: # 如果遇到不同的，就往slow里面装
                i += 1 # 则slow右移一位,腾出一个位置给新元素
                nums[i] = nums[j] # 把fast上面的这个数，填到这个slow的位置
                j += 1 # j在继续往后遍历
            return i + 1 # i开始下一轮 
```

### 3.2 移除元素

emm 其实很想直接nums.pop() ... # 记得pop(index)

![image-20210414080049651](C:\Users\maggie\AppData\Roaming\Typora\typora-user-images\image-20210414080049651.png)

#### 3.2.1 pop方法

```python
class Solution:
    def removeElement(self, nums: List[int], val: int) -> int:
        for i in range((len(nums)-1),-1,-1):
            if nums[i] == val:
                nums.pop(i)
        return len(nums)
```

* 低效，需要44ms左右
#### 3.2.2 双指针法
重点1：原地
重点2：不要的元素往后换

```python
class Solution:
    def removeElement(self, nums: List[int], val: int) -> int:
        i,j = 0,0
        while j < len(nums):#限制j不要跑到nums外面
            if nums[j] == val: #相等的情况直接跳过
                j += 1
            elif nums[j] != val:
                nums[i] = nums[j]
                # 更新索引
                j += 1
                i += 1
        return i #return i 的长度就行
```

#### 3.2.3 进阶 考虑需要删除的元素很少
重点1: 类似相向双指针，一头一尾
重点2: 互换，缩减长度
```python
class Solution:
    def removeElement(self, nums: List[int], val: int) -> int:
        i = 0
        j = len(nums) - 1
        while i <= j:
        	if nums[i] == val:
        		nums[i] = nums[j]
        		j -= 1
        	else:
        		i += 1

        return j+1
```

### 3.3 删除有序数组中的重复项 II

重点1： 最多出现两次
重点2：计数器+快指针+慢指针
重点3：计数器count从1开始
![捕获](C:\Users\maggie\Desktop\捕获.PNG)

```python
class Solution:
    def removeElement(self, nums: List[int], val: int) -> int:
        i,j,count = 0,1,1
        while j < len(nums):
        	# 两者元素相同的情况(重复元素)
        	if nums[i] == nums[j]:
        		count += 1
				# 这个元素正好重复了2次，往左移
                if count == 2:
                i += 1
                nums[i] = nums[j]
                # 这个元素重复多于2次
                else：
                	pass
                j += 1
            # 两者元素不同(新元素)，则左移
           	else:
           		i += 1
           		nums[i] = nums[j]
           		count = 1
           		j += 1
       return i+1

```

### 3.4 移动零

![image-20210416084033991](C:\Users\maggie\AppData\Roaming\Typora\typora-user-images\image-20210416084033991.png)

重点1： 原数组操作
重点2：保持非零元素的相对顺序
* 简直直接暗示了用双指针啊 ！！
重点3：用慢指针存放非零元素，用快指针遍历寻找非零元素
重点4: 遍历完非零元素之后，再把后续的空间置零。


```python
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        i,j = 0,0 #初始化都要是零
        # 双指针历遍寻找非零元素
        while j < len(nums) : #不要忘记快指针的限制
            if nums[j] == 0:
                j += 1
            else:
                nums[i] = nums[j]
                i += 1
                j += 1
        #把剩下的空位赋0
        for k in range(i,len(nums)):
            nums[k] = 0
```

### 3.5 数组中最长山脉

重点1：

![image-20210416090944958](C:\Users\maggie\AppData\Roaming\Typora\typora-user-images\image-20210416090944958.png)

思路1：先固定山峰，然后分别寻找左、右边山脉的长度
思路2：如果当前的山峰山脉长度比最长的山脉长，更新最长山脉。
注意，当确认当前点为山峰的情况，才在左右寻找最长山峰，减少搜索的次数。

```python
class Solution:
    def longestMountain(self, A: List[int]) -> int:
        if len(A) < 3:
            return 0
        
        res = 0

        #固定山峰
        for i in range (1,len(A)-1):
            #确认左右是山峰的情况，才在左右寻找最长山峰
            if A[i-1] < A[i] and A[i+1] < A[i]:
                left = i -1 
                right = i + 1

                #左边山脉长度 
                while left >= 0 and A[left] < A[left+1]:
                    left -= 1
                #右边山脉长度 
                while right <= len(A)  -1 and A[right] > A[right-1]:
                    right += 1
                #如果这个山脉比最长的山脉长，更新res
                #减1是因为要去掉i本身
                if right - left - 1 > res:
                    res = right -  left -1 
        return res
```


key：
快慢指针：1. 快指针的限制
快指针永远用来探索，慢指针用来定位。

## 4 实践-同向双指针-滑动窗口
需要获得数组或者字符串的连续子部分，这时滑动窗口就很适用。
nums[left,right]为滑动窗口，根据具体的要求，通过遍历的时候，来改变left和right的位置，从而完成任务。
滑动窗口有两种：
	1. 固定窗口大小； 
	2. 窗口大小不固定： 
	 	2.1 求解最大的满足条件的窗口
	 	2.2 求解最小的满足条件的窗口

### 4.1 滑动窗口最大值
![image-20210421083605478](C:\Users\maggie\AppData\Roaming\Typora\typora-user-images\image-20210421083605478.png)
常规步骤：
a.初始化左右指针

```python
class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        #双指针方法
        res = []
        L =0 
        R = L+K #左+窗口大小
        if len(nums) > 0:
            while R<len(nums)-1:
                maxValue = max(nums[L:R])
                #粘贴到res里面
                res.append(maxValue)
                #指针向前
                L += 1
                R += 1
            return res
        else：
        	return nums
```

*超出时间限制 这个只是个例题 其实应该用队列的方式

### 4.2 长度最小的子数组

【可变窗口大小】
对于可变窗口大小，我们同样固定初始化左右指针L和R，分别表示窗口的左右顶点，后面跟固定指针不同，需要保证：
a. l 和 r 初始化都是0
b. r指针移动一步
c. 判断窗口内的连续元素 是否满足题目限定的条件
	c(1)如果满足，判断是否需要更新最优解。
		如果需要，则通过移动l指针缩小窗口的大小。循环本步骤。
	c(2)如果不满足，则继续。

![image-20210421093749739](C:\Users\maggie\AppData\Roaming\Typora\typora-user-images\image-20210421093749739.png)

思路：
1. 开始r向右滑动，使得和变大。
2. 当恰好 >= s 时， 记录滑窗所包括的子数组长度res。若res已有数值，需判断新值是否小于旧的值，若是，更新res；left 向右滑动。
3. 判断是否仍 >= s，若是，重复步骤2,3。若否，转步骤1。直到右边框到达最右处。

```python
class Solution:
    def minSubArrayLen(self, target: int, nums: List[int]) -> int:
        #初始化 
        #res初始化 弄个最大值
        #float('inf')可以表示正负无穷
        left,sums,res = 0,0,float('inf')
        #右指针右移
        for right in range(len(nums)):
            sums += nums[right]
            #实现2.
            while sums >= target:
                #若新值小于旧值，更新res
                if right - left + 1 < res:
					res = right - left + 1
                #左指针向右滑动
                sums -= nums[left]
                left += 1
       	 return 0 if res == float('inf') else res
    
```

### 4.3 乘积小于K的子数组

![image-20210421183605173](C:\Users\maggie\AppData\Roaming\Typora\typora-user-images\image-20210421183605173.png)

重点1：连续子数组

当left <= right 且滑动窗口内乘积小于k时候，我们可以知道，这个窗口内所有子集都满足要求。


```python
class Solution:
    def numSubarrayProductLessThanK(self, nums: List[int], k: int) -> int:
        left = 0
        product = 1
        count = 0 
        for right in range(len(nums)):
            # 右边界右移
            product *= nums[right]
            # 如果乘积>=k, 左边界右移
            while left <= right and product >= k:
                product /= nums[left]
                left += 1
            # 当前右边界下，满足条件的数组
            count += right -left
         return count
```

## 5 分离双指针