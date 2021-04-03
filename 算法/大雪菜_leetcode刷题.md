# 目录

[toc]

# 参考文献

[b站大雪菜教程](https://www.bilibili.com/video/BV1Ft41157zW)

# 二分法

## 思想

明确答案的在某个区间（L-R）当中，每次枚举中点，我们可以通过中点的情况判断出答案属于左右两边的一边，我们就可以把另外一边删掉。重复此操作，不断缩小答案区间，当区间只有一个值，L=R时，得到答案。 时间复杂度：logn

70%二分问题多数与单调性有关，有单调性的题目基本都可以二分，可以二分的题目不一定有单调性

95% 存在两段性的性质 有某种性质，使其左边成立，右边不成立

![image-20200830095602721](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200830095602.png)

两个模版

模板1

![image-20200830103619419](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200830103619.png)

![image-20200830102440589](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200830102440.png)

模版2

![image-20200830103625468](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200830103625.png)

![image-20200830102453341](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200830102453.png)

加一防止死循环

![image-20200830194745922](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200830194746.png)

## 例题

### 69. Sqrt(x)

![image-20200830105405545](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200830105405.png)

代码；

```java
class Solution {
    public int mySqrt(int x) {
        if(x==0)return 0;
        int l=1, r=x;
        while(l<r){
            int mid=(int)(l+(long)r+1)/2;
            if(mid<=x/mid)l=mid;
            else r=mid-1;
        }
        return l;
    }
}
```

![image-20200830121954251](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200830121954.png)

设计条件的要求:

1. 目标结果要在边界上
2. 能将区间分成两份

例如 t^2>=x就不满足条件1,因为向下取整的根号x 和根号x-1都满足条件

![image-20200830122031445](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200830122031.png)

### 35. Search Insert Position

![image-20200830150638107](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200830150638.png)

注意:

类似于向上取整,注意数组为空和比数组所有值都大的情况,这两种二分无法解决

```java
class Solution {
    public int searchInsert(int[] nums, int target) {
        if(nums.length==0)return 0;
        if(nums[nums.length-1]<target)return nums.length;
        int l=0,r=nums.length-1;
        while(l<r){
            int mid=(l+r)/2;
            if(nums[mid]>=target)r=mid;
            else l=mid+1;
        }
        return l;
    }
}
```

### 34. Find First and Last Position of Element in Sorted Array

![image-20200830213952678](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200830213953.png)

同时应用红绿两种模板,可以加深对两段的理解

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int l=0,r=nums.length-1;
        int[] res ={-1,-1};
        if(nums.length==0)return res;
        while(l<r){
            int mid=(l+r+1)/2;
            if(nums[mid]<=target) l=mid;
            else r=mid-1;
        }
        if(nums[l]!=target)return res;
        else res[1]=l;
        l=0;
        while(l<r){
            int mid =(l+r)/2;
            if(nums[mid]>=target)r=mid;
            else l=mid+1;
        }
        res[0]=l;
        return res;
    }
}
```

### 74. 搜索二维矩阵

![image-20200831095056344](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200831095103.png)

注意: 重点是二维转一维

```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        if(matrix.length==0)return false;
        if(matrix[0].length==0)return false;
        int m=matrix.length,n=matrix[0].length;
        int l=0,r=m*n-1;
        while(l<r){
            int mid=(l+r)/2;
            int x=mid/n;
            int y=mid-n*x;
            if(matrix[x][y]>=target)r=mid;
            else l=mid+1;
        }
        int x=l/n;
        int y=l-n*x;
        if(matrix[x][y]==target)return true;
        else return false;
    }
}
```

### 153. 寻找旋转排序数组中的最小值

![image-20200831103321131](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200831103321.png)

重点:寻找切分条件

```java
class Solution {
    public int findMin(int[] nums) {
        if(nums.length==1||nums[0]<nums[nums.length-1])return nums[0];
        int l=0,r=nums.length-1;
        int div=nums[0];
        while(l<r){
            int mid=(r+l)/2;
            if(nums[mid]<div)r=mid;
            else l=mid+1;
        }
        return nums[l];
    }
}
//建议题解
class Solution {
    public int findMin(int[] nums) {
        if(nums.length==1)return nums[0];
        int l=0,r=nums.length-1;
        int div=nums[r];
        while(l<r){
            int mid=(r+l)/2;
            if(nums[mid]<=div)r=mid;
            else l=mid+1;
        }
        return nums[l];
    }
}
```

### 33. 搜索旋转排序数组

![image-20200831212653996](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200831212654.png)

```java
class Solution {
    public int search(int[] nums, int target) {
        if(nums.length==0)return -1;
        int len=nums.length;
        if(target<=nums[len-1]){
            int l=0,r=len-1;
            while(l<r){
                int mid=(l+r)/2;
                if(nums[mid]>=target&&nums[mid]<=nums[len-1])r=mid;
                else l=mid+1;
            }
            if(nums[l]==target)return l;
            else return -1;
        }else{
            int l=0,r=len-1;
            while(l<r){
                int mid=(l+r+1)/2;
                if(nums[mid]<=target&&nums[mid]>nums[len-1])l=mid;
                else r=mid-1;
            }
            if(nums[l]==target)return l;
            else return -1;
        }
    }
}
```

### 278. 第一个错误的版本

![image-20200901084816859](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200901084824.png)

注意:

防止溢出

```java
public class Solution extends VersionControl {
    public int firstBadVersion(int n) {
        int l=1,r=n;
        while(l<r){
            int mid=l+(r-l)/2;//防止溢出
            if(isBadVersion(mid)==true)r=mid;
            else l=mid+1;
        }
        return l;
    }
}
```

### 162. Find Peak Element

![image-20200901092804041](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200901092804.png)

注意:算法执行中可能会跳过一些符合条件的峰值点,但最终会在一个上停下来

```java
class Solution {
    public int findPeakElement(int[] nums) {
        int len=nums.length;
        int l=0,r=len-1;
        while(l<r){
            int mid=l+(r-l)/2;
            if(nums[mid]>nums[mid+1])r=mid;
            else l=mid+1;
        }
        return l;
    }
}
```

### 287. Find the Duplicate Number

![image-20200901095749161](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200901095749.png)

注意:特殊情况,使用抽屉原理 即4个抽屉装5个,一定有抽屉重复

```java
class Solution {
    public int findDuplicate(int[] nums) {
        int n=nums.length-1;
        int l=1,r=n;
        while(l<r){
            int mid =(l+r)/2;
            int cnt=0;
            for(int x:nums){
                if(x<=mid&&x>=l)
                    cnt++;
            }
            if(cnt>mid-l+1)r=mid;
            else l=mid+1;
        }
        return l;
 
    }
}
```

### 275. H-Index II

![image-20200901122200796](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200901122200.png)

```java
class Solution {
    public int hIndex(int[] citations) {
        int n=citations.length;
        int l=0,r=n;
        while(l<r){
            int mid=(l+r+1)/2;
            int cnt=0;
            for(int x:citations){
                if(x>=mid)
                    cnt++;
            }
            if(cnt>=mid)l=mid;
            else r=mid-1;
        }
        return l;
    }
}
```

# 链表

因为要用到指针,本系列使用c++做

## 思想

多画图

![image-20200901144945309](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200901144945.png)

## 例题

### 19. 删除链表的倒数第N个节点

![image-20200901145223595](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200901145223.png)

双指针:要求一次扫描

![image-20200901152605512](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200901152605.png)

 

```java
/**
* Definition for singly-linked list.
* struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
* };
*/
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        auto dummy=new ListNode(-1);
        dummy->next=head;
        auto first=dummy,second=dummy;
        while(n--){
            first=first->next;
        }
        while(first->next){
            first=first->next;
            second=second->next;
        }
        second->next=second->next->next;
        return dummy->next;//注意这里不能返回head,因为head可能已经被删掉了
    }
};
```

### 237. 删除链表中的节点

![image-20200902090348397](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200902090355.png)

重点:无法进行传统意义的删除,而是采用替换

![image-20200902090850746](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200902090850.png)

```java
class Solution {
public:
    void deleteNode(ListNode* node) {
        node->val=node->next->val;
        node->next=node->next->next;
    }
};
```

### 83. 删除排序链表中的重复元素

![image-20200902091137973](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200902091138.png)

```java
/**
* Definition for singly-linked list.
* struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
* };
*/
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        ListNode* ln=head;
        while(ln){
            while(ln->next!=NULL&&ln->next->val==ln->val){
                ln->next=ln->next->next;
            }
            ln=ln->next;
        }
        return head;
    }
};
```

### 61. Rotate List

![image-20200902102452449](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200902102452.png)

重点:

* 理解反转,其实将后面一段移到前边,纯指针操作就能很快完成
* 双指针
* 没有修改首结点,不需要虚拟头节点

```java
/**
* Definition for singly-linked list.
* struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
* };
*/
class Solution {
public:
    ListNode* rotateRight(ListNode* head, int k) {
        if(!head)return NULL;
        //终点,旋转点,新终点
        auto first=head,second=head;
        int cnt=0;
        ListNode* ln=head;
        while(ln){
            cnt++;
            ln=ln->next;
        }
        k=k%cnt;
        while(k--){
            first=first->next;
        }
        while(first->next){
            first=first->next;
            second=second->next;
        }
        first->next=head;
        head=second->next;
        second->next=NULL;
        return head;
    }
};
```

### 24. Swap Nodes in Pairs

![image-20200903152316845](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200903152324.png)

重点:固定距离,不需要使用双指针,只需要一个指针,后面位置都能推出来

![image-20200903152334217](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200903152334.png)

```java
/**
* Definition for singly-linked list.
* struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
* };
*/
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        if(!head)return NULL;
        ListNode* dummy=new ListNode(-1);
        dummy->next=head;
        auto p=dummy;
        while(p->next&&p->next->next){
            auto a=p->next,b=p->next->next;
            p->next=b;
            a->next=b->next;
            b->next=a;
            p=a;
        }
        return dummy->next;
    }
};
```

### 206. 反转链表

![image-20200903213804022](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200903213804.png)

重点:

* 如何迭代的对每个反转
* 使用a,b两个指针进行反转,以及一个c指针进行移动

```java
/**
* Definition for singly-linked list.
* struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
* };
*/
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* a=NULL;
        ListNode* b=head;
        while(b){
            ListNode* c=b->next;
            b->next=a;
            a=b;
            b=c;
        }
        return a;
    }
};
```

### 92. 反转链表 II

![image-20200904152929753](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200904152930.png)

```java
/**
* Definition for singly-linked list.
* struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
* };
*/
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int m, int n) {
        if(!head)return NULL;
        ListNode* dummy=new ListNode(-1);
        dummy->next=head;
        auto a=dummy,b=dummy->next;
        ListNode* s1=dummy;
        ListNode* s2=NULL;
        int cnt=n-m;
        cout<<m;
        while(m--){
            a=a->next;
            b=b->next;
            //进入的是运算完的m
            if(m==1)s1=a;
        }
 
        s2=a;
        while(cnt--){
            ListNode* c=b->next;
            b->next=a;
            a=b;
            b=c;
        }
        s1->next=a;
        s2->next=b;
        return dummy->next;
    }
};
```

### 160. 相交链表

![image-20200904204532960](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200904204532960.png)

重点：特别的实现思路，如下所示

![image-20200905111613397](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200905111620.png)

```java
/**
* Definition for singly-linked list.
* struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
* };
*/
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        auto p=headA,q=headB;
        while(p!=q){
            if(p)p=p->next;
            else p=headB;
            if(q)q=q->next;
            else q=headA;
        }
        return p;
    }
};
```

### 142. 环形链表 II

![image-20200905111732873](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200905111732873.png)

要点：

* 快慢指针，1，2
* 相遇后，回开头，两个都是每次走一个
* 数学计算，可知第二次让指针从起点到分界点，等于相遇点到分界点

![image-20200905113523859](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200905113523.png)

```java
/**
* Definition for singly-linked list.
* struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
* };
*/
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        auto fast=head,slow=head;
        while(fast){
            fast=fast->next;
            slow=slow->next;
            if(fast)fast=fast->next;
            else break;
            if(fast==slow){
                fast=head;
                while(fast!=slow){
                    fast=fast->next;
                    slow=slow->next;
                }
                return slow;
            }
        }
 
        return NULL;
    }
};
```

### 148. Sort List

![image-20200905172921181](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200905172922.png)

采用归并排序

```java
/**
* Definition for singly-linked list.
* struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
* };
*/
class Solution {
public:
    ListNode* sortList(ListNode* head) {
        //快慢指针分割
        if(!head||!head->next)return head;
        ListNode* slow=head,*fast=head->next;
        while(fast&&fast->next){
            slow=slow->next;
            fast=fast->next->next;
        }
        ListNode* tmp=slow->next;
        slow->next=NULL;
        return merge(sortList(head),sortList(tmp));
    }
    ListNode* merge(ListNode* l1,ListNode* l2){
 
        ListNode* h=new ListNode(0);
        ListNode* res=h;
        while(l1&&l2){
            if(l1->val<l2->val){
                res->next=l1;
 
                l1=l1->next;
            }else{
                res->next=l2;
                l2=l2->next;
 
            }
            //别忘了
            res=res->next;
        }
        if(l1){
            res->next=l1;
        }else{
            res->next=l2;
        }
 
        return h->next;
    }
};
```

# 树

## 参考文献

[LeetCode暑期刷题打卡2019——Week3 树专题](https://www.bilibili.com/video/BV19t411w7Ep)

 

## 98.验证二叉搜索树

![image-20200906110642305](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200906110649.png)

重点:

* 中序遍历结果应该是升序

```java
/**
* Definition for a binary tree node.
* struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
* };
*/
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        //中序遍历
        stack<TreeNode*> st;
        long long  min_order=(long long)INT_MIN-1;
        while(!st.empty()||root!=NULL){
            while(root!=NULL){
                st.push(root);
                root=root->left;
            }
            TreeNode* tmp=st.top();
            st.pop();
            if(tmp->val<=min_order)return false;
            else{
                min_order=tmp->val;
                root=tmp->right;
            }
        }
        return true;
    }
};
```

自顶向下

```java
/**
* Definition for a binary tree node.
* struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
* };
*/
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        return dfs(root,INT_MIN,INT_MAX);
    }
    bool dfs(TreeNode* root,long long minv,long long maxv){
        if(!root)return true;
        if(root->val<minv||root->val>maxv)return false;
        //1ll代表long long
        return dfs(root->left,minv,root->val-1ll)&&dfs(root->right,root->val+1ll,maxv);
    }
};
```

## 94. 二叉树的中序遍历

![image-20200907105918869](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200907105926.png)

 

递归

```java
/**
* Definition for a binary tree node.
* struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
* };
*/
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> res;
        inorder(root,res);
        return res;
    }
    void inorder(TreeNode* root,vector<int>& v){//传引用
        if(!root)return;
        if(root->left)inorder(root->left,v);
        v.push_back(root->val);
        if(root->right)inorder(root->right,v);
    }
};
```

迭代

```java
/**
* Definition for a binary tree node.
* struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
* };
*/
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> v;
        stack<TreeNode*> st;
        while(!st.empty()||root!=NULL){
 
            while(root){
                st.push(root);
                root=root->left;
            }
            TreeNode* tmp=st.top();
            st.pop();
            v.push_back(tmp->val);
            root=tmp->right;
        }
        return v;
    }
 
};
```

## 101. 对称二叉树

![image-20200907181615899](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200907181616.png)

题解

![image-20200907182104394](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200907182104.png)

递归

```java
/**
* Definition for a binary tree node.
* struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
* };
*/
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        if(!root)return true;
        return dfs(root->left,root->right);
    }
    bool dfs(TreeNode* left,TreeNode* right){
        //下两段可改为 if(!left||!right)return !left&&!right;
        if(left==NULL&&right==NULL)return true;
        if((left==NULL&&right!=NULL)||(left!=NULL&&right==NULL))return false;
 
        if(left->val!=right->val)return false;
        return dfs(left->left,right->right)&&dfs(left->right,right->left);
    }
};
```

迭代

* 左边:左中右
* 右边:右中左

 

```java
/**
* Definition for a binary tree node.
* struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
* };
*/
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        if(!root)return true;
        stack<TreeNode*> st1,st2;
        auto l=root->left,r=root->right;
        while(l||r||st1.size()||st2.size()){
            while(l&&r){
                st1.push(l);
                l=l->left;
                st2.push(r);
                r=r->right;
            }
            if(l||r)return false;
            l=st1.top();
            r=st2.top();
            if(l->val!=r->val)return false;
            st1.pop();
            st2.pop();
            l=l->right;
            r=r->left;
        }
        return true;
    }
 
};
```

## 105. 从前序与中序遍历序列构造二叉树

![image-20200908101842261](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200908101849.png)

 

递归

```java
/**
* Definition for a binary tree node.
* struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
* };
*/
class Solution {
public:
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        return pi(preorder,inorder,0,0,preorder.size()-1);
 
    }
    TreeNode* pi(vector<int>& preorder, vector<int>& inorder,int st_p,int st_i,int en_i){
        if(st_i>en_i)return NULL;
        TreeNode* root=new TreeNode(preorder[st_p]);
        int i=st_i;
        for(;i<=en_i;i++){
            if(inorder[i]==preorder[st_p])break;
        }
        root->left=pi(preorder,inorder,st_p+1,st_i,i-1);
        root->right=pi(preorder,inorder,st_p+i-st_i+1,i+1,en_i);
        return root;
 
    }
};
```

## 102. 二叉树的层序遍历

![image-20200908110531107](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200908110531.png)

```c++
/**
* Definition for a binary tree node.
* struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
* };
*/
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> res;
        if(!root)return res;
        queue<TreeNode*> q;
        queue<int> q2;
        q.push(root);
        q2.push(0);
        while(q.size()){
            TreeNode* tmp=q.front();
            int ind=q2.front();
            //注意,size返回无符号整形,不能计算到负值,所以不能是size()-1
            if(ind+1>res.size()){
                vector<int> v1;
                res.push_back(v1);
            }
            res[ind].push_back(tmp->val);
            q.pop();
            q2.pop();
            if(tmp->left){
                q.push(tmp->left);
                q2.push(ind+1);
            }
            if(tmp->right){
                q.push(tmp->right);
                q2.push(ind+1);
            }
        }
        return res;
    }
};
```

## 236. 二叉树的最近公共祖先

![image-20200908192141138](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200908192141.png)

![image-20200908192148904](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200908192148.png)

题解

![image-20200908225340811](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200908225340.png)

左边为空,我们发现返回和右边相同

![image-20200908225410824](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200908225410.png)

 

```c++
/**
* Definition for a binary tree node.
* struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
* };
*/
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(!root||root==p||root==q)return root;
        auto left=lowestCommonAncestor(root->left,p,q);
        auto right=lowestCommonAncestor(root->right,p,q);
        if(!left)return right;
        if(!right)return left;
        //最后只有左右都不空
        return root;
    }
};
```

##  543. 二叉树的直径

![image-20200909095717345](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200909095724.png)

重点:

* 一开始我想用左右两边深度相加,但这道题需要注意的一点是：二叉树的直径（即最长路径），不一定经过根结点。

![image-20200909103720525](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200909103720.png)

* 本题思路是枚举所有顶点的直径,取最大,因此需要一个全局变量

 

```c++
 
/**
* Definition for a binary tree node.
* struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
* };
*/
class Solution {
public:
    int ans=0;
    int diameterOfBinaryTree(TreeNode* root) {
        getLen(root);
        return ans;
    }
    int getLen(TreeNode* root){
        if(!root)return 0;
        int left=getLen(root->left);
        int right=getLen(root->right);
        ans=ans>left+right?ans:left+right;
        return left>right?left+1:right+1;
    }
};
```

 

## 124. 二叉树中的最大路径和

![image-20200909104127133](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200909104127.png)

![image-20200909104136876](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200909104136.png)

```c++
/**
* Definition for a binary tree node.
* struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
* };
*/
class Solution {
public:
    int ans=INT_MIN;
    int maxPathSum(TreeNode* root) {
        getmax(root);
        return ans;
    }
    int getmax(TreeNode* root){
        //求包含根节点的情况下的最大值
        if(!root)return 0;
        int left=getmax(root->left);
        int right=getmax(root->right);
        //求v,v1,v2,v3中最大值
        ans=max(ans,left+root->val+right);
        //返回v,v1,v2的最大值
        return max(0,root->val+max(left,right));
    }
};
```

## 173. 二叉搜索树迭代器

![image-20200909214130538](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200909214130.png)

![image-20200909214139002](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200909214139.png)

 

```c++
/**
* Definition for a binary tree node.
* struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
* };
*/
class BSTIterator {
public:
    stack<TreeNode*> st;
    BSTIterator(TreeNode* root) {
        while(root){
            st.push(root);
            root=root->left;
        }
    }
 
    /** @return the next smallest number */
    int next() {
        TreeNode* tmp=st.top();
        st.pop();
        int res=tmp->val;
        tmp=tmp->right;
        while(tmp){
            st.push(tmp);
            tmp=tmp->left;
        }
        return res;
    }
 
    /** @return whether we have a next smallest number */
    bool hasNext() {
        return !st.empty();
    }
};
 
/**
* Your BSTIterator object will be instantiated and called as such:
* BSTIterator* obj = new BSTIterator(root);
* int param_1 = obj->next();
* bool param_2 = obj->hasNext();
*/
```

## 297. 二叉树的序列化与反序列化

![image-20200909225525034](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200909225525.png)

注意：

* 前序遍历并不能确定一棵二叉树，但我们加上空节点即可唯一确定
* 字符转文字的通用处理是遍历每一个然后-'0' ,但需要判断是否为负值,我这里取巧用stringstream
* 通用处理方式
* ![image-20200910193523633](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20200910193531.png)

```c++
/**
* Definition for a binary tree node.
* struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
* };
*/
class Codec {
public:
 
    // Encodes a tree to a single string.
    string serialize(TreeNode* root) {
        string res;
        dfs(root,res);
        return res;
    }
    void dfs(TreeNode* root,string& s){
        if(!root){
            s+="#,";
            return;
        }
        s+=to_string(root->val)+',';
        dfs(root->left,s);
        dfs(root->right,s);
    }
 
    // Decodes your encoded data to tree.
    TreeNode* deserialize(string data) {
        //不需要切分,使用指针
        int u=0;
        return dfs2(data,u);
    }
    TreeNode* dfs2(string &data,int&u){
        if(data[u]=='#'){
            u+=2;
            return NULL;
        }
        int st=u;
        while(data[u]!=','){
            u++;
        }
        string str_num=data.substr(st,u-st);
        u++;
        int num;
        stringstream ss;
        ss<<str_num;
        ss>>num;
        TreeNode* root=new TreeNode(num);
        root->left=dfs2(data,u);
        root->right=dfs2(data,u);
        return root;
    }
};
 
// Your Codec object will be instantiated and called as such:
// Codec codec;
// codec.deserialize(codec.serialize(root));
```

# 字符串

## 38.外观数列

[38. 外观数列](https://leetcode-cn.com/problems/count-and-say/)

## 49. 字母异位词分组

[49. 字母异位词分组](https://leetcode-cn.com/problems/group-anagrams/)