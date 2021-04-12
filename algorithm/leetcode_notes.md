# leetcode 刷题笔记
## 3. 无重复的最长子串
### 双指着
vector存储对应ASCII码字符是否出现，双指针，无重复时右指针右移，更新最大值 left - right.一旦hash[right] != 0，左指针左移并将对应字符hash位置次数--，直到hash[right] 为0.


## 25. K 个一组翻转链表
### 链表指针操作
组内进行反转操作，之后把头结点连到前面一组的末尾，把尾结点连到后面一组的首部。
指针：pre tail 
增加伪头指针phead
判断走k步期间tail是否提前结束（即不够k个结点），如果是直接返回phead->next.
![](.img/25-1.png)
![](.img/25-2.png)

## 215. 数组中第K个最大元素
### 快排判断下标
基本思想：第K大那么排序后它的下标应该为size - k。
加速排序：随机选择一个下标与最右元素交换。
> random() % ( R - L  + 1) + L 返回的是[L,R]之间的数

以最右元素为标杆，将比它小的都放到左侧，最后再把最右元素与最终下标处交换，返回下标位置。
若下标位置在左侧，小了，在右区间搜索，否则在左区间搜索。

## 103. 二叉树的锯齿形层序遍历
层序遍历：队列
锯齿状：每层用一个双端队列，若正序则push_back，若逆序则push_front
每层结束后至标志位为反向

## 160. 相交链表
基本思想： A和B两个链表长度可能不同，但是A+B和B+A的长度是相同的，所以遍历A+B和遍历B+A一定是同时结束。 如果A,B相交的话A和B有一段尾巴是相同的，所以两个遍历的指针一定会同时到达交点 如果A,B不相交的话两个指针就会同时到达A+B（B+A）的尾节点。

p、q指针分别指向A、B链表头，当走到尾部时指向另一个链表的首部，直到p=q即为交点

## 146. LRU 缓存机制
哈希表 + 双端链表
哈希表存储链表的引用，get时从哈希表取，并且将结点moveToHead，put时检查是否存在，存在则moveToHead并更新值，不存在时若有空闲则addToHead并加入哈希表，无空闲则删除尾结点并删除哈希表引用，再addToHead并加入哈希表。

## 15. 三数之和
排序，固定一个数，左右指针移动。
去重：当前一个数=当前数时继续移动指针

## 121. 买卖股票的最佳时机
保存最低价格，计算最大差值

## 53. 最大子序和
dp[i] = max(dp[i - 1] + nums[i],nums[i]);

## 236. 二叉树的最近公共祖先
p、q结点在root异侧则为root，p=root且q在左或右，则为p，同理，则为q。
递归，当碰到p或q结点时返回。
```c++
TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(root == NULL || root == p || root == q)return root;
        TreeNode * L = lowestCommonAncestor(root->left,p,q);
        TreeNode * R = lowestCommonAncestor(root->right,p,q);
        if(L == NULL && R == NULL)return NULL;
        if(L == NULL)return R;
        if(R == NULL)return L;
        return root;
    }
```

## 42. 接雨水
双指针，左右各记录最高柱子，当左柱低于右柱时，左指针右移，期间若小于左最高，累加差值，大于则更新左最高，反之右边对称操作。

## 54. 螺旋矩阵
用top right left bottom四个变量维护，当超出边界的时候结束

## 33. 搜索旋转排列数组
二分搜索，当左边有序（**num[0]** <= nums[mid] ）: 若target 在 nums[0]和nums[mid]的值之间，r = mid - 1，否则在右边搜索 l = mid + 1。 右边有序（nums[mid] <= **nums[n - 1]**）同理。

## 200. 岛屿数量
深度优先搜索，从有岛屿（1）的**每一个地方**开始，把与之相连的置为0. 
若题目不允许修改原数组，要增加标记数组。

## 46. 全排列
有标记数组：标记访问过的元素为true，从0开始遍历
```c++
 for(int i = 0; i < n; ++i){
            if(mark[i])continue;
            cur.push_back(nums[i]);
            mark[i] = true;
            dfs(nums,cur,mark,k+1,n);
            cur.pop_back();
            mark[i] = false;
        }
```
无标记数组：从当前深度k开始，每次交换nums[k] 和 nums[i]，重置状态再交换回来。
```c++
for(int i = k; i < n; ++i){
            
            swap(nums[k],nums[i]);
            dfs(nums,k+1,n);
            swap(nums[k],nums[i]);
        }
```

