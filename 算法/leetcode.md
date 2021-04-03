# 目录

[toc]

# review

一级标题:题目标签

二级标题:题目名 直接网站复制带连接

三级标题:解决方法(如果有多种)

```
###review
```

 

![2019-06-17T09_17_17](https://github.com/wuyuler/pictureBed/raw/master/2019-06-17T09_17_17.png)

![2019-06-17T09_17_53](https://github.com/wuyuler/pictureBed/raw/master/2019-06-17T09_17_53.png)

#参考文献

[力扣](https://leetcode-cn.com/)

[leetcode网址]( https://www.nowcoder.com/ta/leetcode?query=&asc=true&order=&page=5 )

# 链表

## 前置

### 头节点

![img](https://img-my.csdn.net/uploads/201302/22/1361516923_9264.jpg)

数据结构中，在单链表的开始结点之前附设一个类型相同的结点，称之为头结点。头结点的数据域可以不存储任何信息，头结点的指针域存储指向开始结点的指针（即第一个元素结点的存储位置）。

**作用**

1、**防止单链表**是**空**的而设的.当链表为空的时候,带头结点的头指针就指向头结点.如果当链表为空的时候,单链表没有带头结点,那么它的头指针就为NULL.

2、是为了方便单链表的特殊操作,插入在表头或者删除第一个结点.这样就保持了单链表操作的统一性!

3、单链表加上头结点之后，无论单链表是否为空，头指针始终指向头结点，因此空表和非空表的处理也统一了，方便了单链表的操作，也减少了程序的复杂性和出现bug的机会。

4、对单链表的多数操作应明确对哪个结点以及该结点的前驱。不带头结点的链表对首元结点、中间结点分别处理等；而带头结点的链表因为有头结点，首元结点、中间结点的操作相同 ,从而减少分支，使算法变得简单 ,流程清晰。对单链表进行插入、删除操作时，如果在首元结点之前插入或删除的是首元结点，不带头结点的单链表需改变头指针的值，在C 算法的函数形参表中头指针一般使用指针的指针(在C+ +中使用引用 &)；**而带头结点的单链表不需改变头指针的值，函数参数表中头结点使用指针变量即可。**

## sort-list

* 题目描述

  Sort a linked list in *O*(*n* log *n*) time using constant space complexity.

* code_std

  ```
  链接：https://www.nowcoder.com/questionTerminal/d75c232a0405427098a8d1627930bea6
  来源：牛客网
   
  /*
    考点：
    1. 快慢指针；2. 归并排序。
    此题经典，需要消化吸收。
    复杂度分析:
               T(n)            拆分 n/2, 归并 n/2 ，一共是n/2 + n/2 = n
              /    \           以下依此类推：
            T(n/2) T(n/2)      一共是 n/2*2 = n
           /    \  /     \
          T(n/4) ...........   一共是 n/4*4 = n
   
         一共有logn层，故复杂度是 O(nlogn)
   */
  class Solution {
  public:
      ListNode *sortList(ListNode *head) {
          if (!head || !head->next) return head;
   
          ListNode* p = head, *q = head->next;
          while(q && q->next) {
              p = p->next;
              q = q->next->next;
          }
   
          ListNode* left = sortList(p->next);
          p->next = NULL;
          ListNode* right = sortList(head);
   
          return merge(left, right);
      }
  ```


      ListNode *merge(ListNode *left, ListNode *right) {
          ListNode dummy(0);
          ListNode *p = &dummy;
          while(left && right) {
              if(left->val < right->val) {
                  p->next = left;
                  left = left->next;
              }
              else {
                  p->next = right;
                  right = right->next;
              }
              p = p->next;
          }
          if (left) p->next = left;
          if (right) p->next = right;
          return dummy.next;
      }

  };

## 两数相加

给出两个 **非空** 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 **逆序** 的方式存储的，并且它们的每个节点只能存储 **一位** 数字。

如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。

您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

**示例：**

```
输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807
```

**review**:
指针也可以传引用

```c
 
* Definition for singly-linked list.
* struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
* };
*/
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode* head=new ListNode(0);
        ListNode* pnode=head;
        int add=0;
        while(l1||l2||add==1){
            int a=moveAndGetValue(l1);
            int b=moveAndGetValue(l2);
            int res=a+b+add;
            if(res>=10){
                res=res-10;
                add=1;
            }else{
                add=0;
            }
            pnode->next=new ListNode(res);
            pnode=pnode->next;
        }
        //去掉头节点
        head=head->next;
        return head;
    }
    int moveAndGetValue(ListNode* &l){
        int w=0;
        if(l!=NULL){
            w=l->val;
            l=l->next;
        }
        return w;
    }
};
```

## [19. 删除链表的倒数第N个节点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

mycode

两趟扫描

```c
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
        int len=0;
        ListNode* temp=head;
        while(temp){
            len++;
            temp=temp->next;
        }
        if(len==1)return NULL;//添加头结点后可以删除,但效率会变低
        int a=len-n+1;//正数第a个
        ListNode* h=new ListNode(len);
        h->next=head;
        temp=h;
        for(int i=1;i<a;i++){
            temp=temp->next;//移动到被删除节点的前一个节点
        }
        temp->next=temp->next->next;
        h=h->next;
        return h;
    }
};
```

一次循环 空间换时间 指针数组的保存

```c
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
        ListNode* h=new ListNode(-1);
        h->next=head;
        vector<ListNode*> v;
        v.push_back(h);
        int count=0;
        while(head){
            count++;
            v.push_back(head);
            head=head->next;
        }
        ListNode * l=v[count-n];
        l->next=l->next->next;
        return h->next;
    }
};
```

 

## [24. 两两交换链表中的节点](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。

**你不能只是单纯的改变节点内部的值**，而是需要实际的进行节点交换。

 

**示例:**

```
给定 1->2->3->4, 你应该返回 2->1->4->3.
```

```c
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
        ListNode* h_res=new ListNode(-1);
        h_res->next=head;
        ListNode*h=h_res;
        while(h->next&&h->next->next){
            ListNode* temp_1=h->next;
            ListNode* temp_2=h->next->next;
            h->next=temp_2;
            temp_1->next=temp_2->next;
            h->next->next=temp_1;
            h=temp_1;
            cout<<h->val<<endl;
        }
        return h_res->next;
    }
};
```

 

# 树

## [94. 二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

给定一个二叉树，返回它的*中序* 遍历。

**示例:**

```
输入: [1,null,2,3]
   1
    \
     2
    /
   3
 
输出: [1,3,2]
```

**code:**

```
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
        vector<int>res;
        if(root==NULL)return res;
        if(root->left){
            vector<int> left=inorderTraversal(root->left);
            for(int i=0;i<left.size();i++)res.push_back(left[i]);
        }
        res.push_back(root->val);
        if(root->right){
            vector<int> right=inorderTraversal(root->right);
            for(int i=0;i<right.size();i++)res.push_back(right[i]);
        }
        return res;
    }
};
```

 

# 栈

## 棒球比赛

  你现在是棒球比赛记录员。
给定一个字符串列表，每个字符串可以是以下四种类型之一：
1.`整数`（一轮的得分）：直接表示您在本轮中获得的积分数。
\2. `"+"`（一轮的得分）：表示本轮获得的得分是前两轮`有效` 回合得分的总和。
\3. `"D"`（一轮的得分）：表示本轮获得的得分是前一轮`有效` 回合得分的两倍。
\4. `"C"`（一个操作，这不是一个回合的分数）：表示您获得的最后一个`有效` 回合的分数是无效的，应该被移除。

每一轮的操作都是永久性的，可能会对前一轮和后一轮产生影响。
你需要返回你在所有回合中得分的总和。

```c
class Solution {
public:
    int calPoints(vector<string>& ops) {
        vector<int> st;
        for(int i=0;i<ops.size();i++){
            string temp=ops[i];
            if(temp=="C"){
                st.pop_back();
            }else if(temp=="D"){
                if(st.size()>0){
                    int a=st.back()*2;
                    st.push_back(a);
                }
 
            }else if(temp=="+"){
                int a;
                if(st.size()>0){
                    a=st.back();
                }
                if(st.size()>1)
                 a+=st[st.size()-2];
                st.push_back(a);
            }else{
                stringstream ss;
                int num;
                ss<<temp;
                ss>>num;
                st.push_back(num);
            }
 
        }
        int res=0;
        for(int i=0;i<st.size();i++)
            res+=st[i];
        return res;
    }
};
```

 

# 堆

# 贪心算法

# 排序

# 位运算

## [29. 两数相除](https://leetcode-cn.com/problems/divide-two-integers/)

给定两个整数，被除数 `dividend` 和除数 `divisor`。将两数相除，要求不使用乘法、除法和 mod 运算符。

返回被除数 `dividend` 除以除数 `divisor` 得到的商。

**示例 1:**

```
输入: dividend = 10, divisor = 3
输出: 3
```

```c
class Solution {
public:
    int divide(int dividend, int divisor) {
        //移位
        long ans=0,up=fabs(dividend),down=fabs(divisor);
        while(up>=down){
            long count=1,base=down;
            while(up>(base<<1)){
                base<<=1;
                count<<=1;
            }
            ans+=count;
            up-=base;
        }
        ans=((dividend < 0)^(divisor < 0)) ? -ans : ans;
        return (ans > INT_MAX || ans < INT_MIN) ? INT_MAX : ans;
    }
};
```

 

# 动态规划

## [139. 单词拆分](https://leetcode-cn.com/problems/word-break/)

给定一个非空字符串 s 和一个包含非空单词列表的字典 wordDict，判定 s 是否可以被空格拆分为一个或多个在字典中出现的单词。

说明：

拆分时可以重复使用字典中的单词。
你可以假设字典中没有重复的单词。
示例 1：

输入: s = "leetcode", wordDict = ["leet", "code"]
输出: true
解释: 返回 true 因为 "leetcode" 可以被拆分成 "leet code"。

```c
class Solution {
public:
    template<typename T>
void output(T s) {
    //cout << s << endl;
}
template<typename T>
void output(vector<T> a) {
    /*for (int i = 0; i < a.size(); i++)
        cout << a<<" ";
    cout << endl;*/
}
void output(int * arr,int len) {
    /*for (int i = 0; i < len; i++)
        cout << arr[i] << " ";
    cout << endl;*/
}
bool hasThisStr(string s, vector<string>& wordDict) {
    for (int i = 0; i < wordDict.size(); i++)
        if (s == wordDict[i])return true;
    return false;
}
    bool wordBreak(string s, vector<string>& wordDict) {
    //让被切分的字符尽可能的少
    int* F = new int[s.size()]();//存储映射到字典的连续字符
    string temp = s.substr(0, 1);
    if (hasThisStr(temp, wordDict))F[0] = 1;
    else F[0] = 0;
    for (int i = 1; i < s.size(); i++) {
        int last = F[i - 1];
        int t = 0;
        for (int j = 0; j < wordDict.size(); j++) {
            int num = wordDict[j].size();//符合字符传的长度
            if (s[i] != wordDict[j][num - 1])continue;
            output<char>(F[i]);
            if (i - num + 1 >= 0) {
                if (i - num >= 0 && F[i - num] != i - num + 1)continue;//新的字符串前面必须是已经匹配的字符串
                string temp2 = s.substr(i - num + 1, num);
                output<string>(temp2);
                if (hasThisStr(temp2, wordDict)) {
                    t = i + 1;//
                    break;
                }
            }
        }
        F[i] = t > last ? t : last;
    }
    output(F, s.size());
    if (F[s.size()-1] ==s.size())return true;
    else return false;
}
 
};
int main() {
    string s = "leetcode";
    string s2[] = { "leet","code" };
    vector<string> v(s2,s2+2);
    if (wordBreak(s, v)) {
        cout << "Yes";
    }
    return 0;
}
```

优化:1. F数组存储的长度在本题根本没有意义,直接改为bool 2.多使用API尽量少自己造轮子

```c
bool wordBreak(string s, vector<string>& wordDict) {
    vector<bool> dp(s.size()+1, false);
    unordered_set<string> m(wordDict.begin(), wordDict.end());
    dp[0] = true;
    for (int i = 1; i <= s.size(); ++i){
        for (int j = 0; j < i; ++j){
            if (dp[j] && m.find(s.substr(j, i-j)) != m.end()){
                dp[i] = true;
                break;
            }
        }
    }
    return dp[s.size()];
```

 

##[152. 乘积最大子序列](https://leetcode-cn.com/problems/maximum-product-subarray/)

```c
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        if(nums.size()==0)return 0;
        int* dp_min=new int[nums.size()];//以i字符结尾的最小值
        int*dp_max=new int[nums.size()];//以i字符结尾的最大值
        dp_min[0]=nums[0];
        dp_max[0]=nums[0];
        for(int i=1;i<nums.size();i++){
            dp_min[i]=min(nums[i],min(dp_min[i-1]*nums[i],dp_max[i-1]*nums[i]));
            dp_max[i]=max(nums[i],max(dp_min[i-1]*nums[i],dp_max[i-1]*nums[i]));
        }
        int res=INT_MIN;
        for(int i=0;i<nums.size();i++){
            res=max(res,dp_max[i]);
        }
        return res;
    }
};
```

## [32. 最长有效括号](https://leetcode-cn.com/problems/longest-valid-parentheses/)

给定一个只包含 `'('` 和 `')'` 的字符串，找出最长的包含有效括号的子串的长度。

**示例 1:**

```
输入: "(()"
输出: 2
解释: 最长有效括号子串为 "()"
```

**示例 2:**

```
输入: ")()())"
输出: 4
解释: 最长有效括号子串为 "()()"
```

```c
class Solution {
public:
    int longestValidParentheses(string s) {
        if(s.size()<2)return 0;
        int *dp=new int[s.size()]();
        if(s[0]=='('&&s[1]==')')dp[1]=2;
        for(int i=2;i<s.size();i++){
            if(s[i]==')'){
                if(s[i-1]=='('){
                    dp[i]=dp[i-2]+2;
                }else{
                    if(i-dp[i-1]-1>=0&&s[i-dp[i-1]-1]=='('){
                        dp[i]=dp[i-1]+2;
                        if(i-dp[i-1]-2>=0){
                            dp[i]+=dp[i-dp[i-1]-2];
                        }
                    }
                }
            }
        }
        int max=0;
        for(int i=0;i<s.size();i++){
            if(dp[i]>max)max=dp[i];
        }
        return max;
    }
};
```

 

#图

 

# 字符串

## 无重复字符的最长子串

BUG:

* m.insert(pair<char,int>(s[end],end++));别忘了函数是从右往左
* begin=m[s[end]]+1 这样跳过的字符,很多没从map里删除

```c
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        int begin=0;
        int end=0;
        int maxlen=0;
        map<char,int> m;
        while(end<s.length()){
            if(m.find(s[end])==m.end()){
                m.insert(pair<char,int>(s[end],end));
                end++;
            }else{
                if(begin==end){
                    end++;
                }
                else{
                    int templen=end-begin;
                    if(templen>maxlen)maxlen=templen;
                    m.erase(s[begin]);
                    begin++;
 
                }
 
            }
        }
        if(end-begin>maxlen)maxlen=end-begin;
        return maxlen;
    }
};
```

滑窗

```c
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
      int chMap[256] = {0};//存储被扫描字符的下一个字符的位置
        int lower = 0, maxLen = 0;
        for (int upper = 0; upper < s.size(); ++upper) {
        if (chMap[s[upper]] == 0 || chMap[s[upper]] <= lower)//该字符没出现,或已被跳过
            maxLen = max(maxLen, upper - lower + 1);
        else lower = chMap[s[upper]];
        chMap[s[upper]] = upper + 1; //存放字符出现位置加1 加1为处理提一个位0的情况
    }
    return maxLen;
 
    }
};
```

##最长回文子串

给定一个字符串 `s`，找到 `s` 中最长的回文子串。你可以假设 `s` 的最大长度为 1000。

**示例 1：**

```
输入: "babad"
输出: "bab"
注意: "aba" 也是一个有效答案。
```

**示例 2：**

```
输入: "cbbd"
输出: "bb"
```

中心拓展

事实上，只需使用恒定的空间，我们就可以在 O(n^2)*O*(*n*2) 的时间内解决这个问题。

我们观察到回文中心的两侧互为镜像。因此，回文可以从它的中心展开，并且只有 2n - 12*n*−1 个这样的中心。

你可能会问，为什么会是 2n - 12*n*−1 个，而不是 n*n* 个中心？原因在于所含字母数为偶数的回文的中心可以处于两字母之间（例如“abba” 的中心在两个 b 之间）。

```java
public String longestPalindrome(String s) {
    if (s == null || s.length() < 1) return "";
    int start = 0, end = 0;
    for (int i = 0; i < s.length(); i++) {
        int len1 = expandAroundCenter(s, i, i);
        int len2 = expandAroundCenter(s, i, i + 1);
        int len = Math.max(len1, len2);
        if (len > end - start) {
            start = i - (len - 1) / 2;
            end = i + len / 2;
        }
    }
    return s.substring(start, end + 1);
}
 
private int expandAroundCenter(String s, int left, int right) {
    int L = left, R = right;
    while (L >= 0 && R < s.length() && s.charAt(L) == s.charAt(R)) {
        L--;
        R++;
    }
    return R - L - 1;
}
```

```c
class Solution {
public:
    string longestPalindrome(string s) {
        int begin=0,end=0;
        for(int i=0;i<s.size();i++){
            int a = expand(s,i,i);
            int b=expand(s,i,i+1);
            int temp=max(a,b);
            if(temp>end-begin){
                begin=i-(temp-1)/2;
                end=i+temp/2;
            }
        }
        string res=s.substr(begin,end-begin+1);
        return res ;
    }
    int expand(string s,int L,int R){
        while(L>=0&&R<s.size()&&s[L]==s[R]){
            L--;
            R++;
        }
        return R-L-1;
    }
};
```

## Z字形变化

将一个给定字符串根据给定的行数，以从上往下、从左到右进行 Z 字形排列。

比如输入字符串为 `"LEETCODEISHIRING"` 行数为 3 时，排列如下：

```
L   C   I   R
E T O E S I I G
E   D   H   N
```

之后，你的输出需要从左往右逐行读取，产生出一个新的字符串，比如：`"LCIRETOESIIGEDHN"`。

请你实现这个将字符串进行指定行数变换的函数：

```
string convert(string s, int numRows);
```

**示例 1:**

```
输入: s = "LEETCODEISHIRING", numRows = 3
输出: "LCIRETOESIIGEDHN"
```

**示例 2:**

```
输入: s = "LEETCODEISHIRING", numRows = 4
输出: "LDREOEIIECIHNTSG"
解释:
 
L     D     R
E   O E   I I
E C   I H   N
T     S     G
```

**BUG**:

判断空char不能用 c==' '或者c=='',要用c==0

注意向下和向上的结束位置

**my**

```c
class Solution {
public:
    string convert(string s, int numRows) {
        if(s=="")return "";//无法处理空
        if(numRows==1)return s;//无法处理numRows等于1的情况
        char** c=new char*[numRows];
        for(int i=0;i<numRows;i++){
            c[i]=new char[s.size()]();
        }
        int step=0;
        int x=0,y=0;
        while(step<s.size()){
            while(x<numRows-1&&step<s.size()){
                c[x][y]=s[step++];
                down(x,y);
            }
 
            while(x>0&&step<s.size()){
                c[x][y]=s[step++];
                Z_up(x,y);
            }
        }
        string res="";
        for(int i=0;i<numRows;i++){
            for(int j=0;j<=y;j++){
                if(c[i][j]!=0)res+=c[i][j];
            }
        }
        for(int i=0;i<numRows;i++)
            delete[] c[i];
        return res;
    }
    void down(int&x,int&y){
        x++;
    }
    void Z_up(int&x,int&y){
        x--;y++;
    }
};
```

去掉列

```c
class Solution {
public:
    string convert(string s, int numRows) {
        vector<string> res(numRows);
        int n=0;
        int x;
        while(1){
            x=0;
            while(x<numRows&&n<s.size()){
                res[x++]+=s[n++];
            }
            x--;//返回最后一行
            while(x>1&&n<s.size()){
                res[--x]+=s[n++];
            }
            if(n==s.size())break;
        }
        string res2;
        for(int i=0;i<numRows;i++)
            res2+=res[i];
        return res2;
    }
 
};
```

## 38. 报数

```
报数序列是一个整数序列，按照其中的整数的顺序进行报数，得到下一个数。其前五项如下：
 
1. 1
2. 11
3. 21
4. 1211
5. 111221
 
```

code

```c
class Solution {
public:
    string countAndSay(int n) {
        string res="1";
        for(int i=0;i<n-1;i++){
            string temp;
            int j=0;
            while(j<res.size()){
                char a = res[j];
                string temp1="";
                while(j<res.size()&&res[j]==a){
                    temp1+=a;
                    j++;
                }
                int num =temp1.size();
                char num1=num+'0';
                temp+=num1;
                temp+=a;
            }
            res=temp;
        }
        return res;
    }
};
```

 

# 深度优先

# 广度优先

# 并查集

## [128. 最长连续序列](https://leetcode-cn.com/problems/longest-consecutive-sequence/)

给定一个未排序的整数数组，找出最长连续序列的长度。

要求算法的时间复杂度为 *O(n)*。

**示例:**

```
输入: [100, 4, 200, 1, 3, 2]
输出: 4
解释: 最长连续序列是 [1, 2, 3, 4]。它的长度为 4。
```

```c
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        if(nums.size()==0)return 0;
        map<int,int> uf;
        map<int,int> count;
        for(int i=0;i<nums.size();i++){
            uf[nums[i]]=INT_MAX;//表示独立结点
            count[nums[i]]=1;
        }
        //cout<<"初始化"<<endl;
        for(int i=0;i<nums.size();i++){
            int num=nums[i];
            if(uf.find(num+1)!=uf.end()){//找一下是否有num+1
                join(num,num+1,uf,count,INT_MAX);
        }
            if(uf.find(num-1)!=uf.end()){//找一下是否有num-1
                join(num,num-1,uf,count,INT_MAX);
        }
        }
        //cout<<"并查集完毕"<<endl;
        map<int,int>::iterator iter;
        iter=count.begin();
        int max=0;
        while(iter!=count.end()){
            //cout<<iter->second<<endl;
            if(iter->second>max)max=iter->second;
            iter++;
        }
        return max;
    }
 
    int find(int x,map<int,int> &uf,int none){
        if(uf[x]==none)return x;
        return uf[x]=find(uf[x],uf,none);
    }
    void join(int x,int y,map<int,int> &uf,map<int,int> &count,int none){
        int root1=find(x,uf,none);
        int root2=find(y,uf,none);
        if(root1!=root2){//为了尽力减少树高的增长,将小的秩加在大的秩里
            if(count[root1]>count[root2]){
                uf[root2]=root1;
                count[root1]=count[root1]+count[root2];
            }else{
                uf[root1]=root2;
                count[root2]=count[root1]+count[root2];
            }
        }
    }
};
```

* map

```c
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        map<int,int> m;
        int max=0;
        for(int i=0;i<nums.size();i++){
            if(m.find(nums[i])==m.end()){
                int left=getNum(m,nums[i]-1,0);
                int right=getNum(m,nums[i]+1,0);
                int curr=left+right+1;
                if(curr>max)max=curr;
                m[nums[i]]=curr;
                //核心代码 更新端点
                m[nums[i]-left]=curr;
                m[nums[i]+right]=curr;
            }
        }
        return max;
    }
    template<typename T>
    int getNum(map<T,int>& m,T key,int num_def){
        if(m.find(key)==m.end()){
            return num_def;
        }else{
            return m[key];
        }
    }
};
```

 

## [130. 被围绕的区域](https://leetcode-cn.com/problems/surrounded-regions/)

并查集 不如直接广搜

```c
class Solution {
public:
    struct Point{
        int y;
        int x;
        Point(int y,int x):y(y),x(x){
        }
        bool operator<(const Point&p)const{
            if(y<p.y)return true;
            else if(y==p.y){
                if(x<p.x)return true;//必须重载的这一步
            }
            return false;
        }
 
    };
    void solve(vector<vector<char>>& board) {
        if(board.size()==0)return;
        map<Point,int> m;//将点映射为index
        vector<Point*> v;//存储所有O点
        vector<Point*> edge;//存储所有边缘点
        int len=board.size()*board[0].size();
        int* uf=new int[len];//并查集  欲哭无泪 BUG:int(len)
        //fill(uf,uf+board.size()*board[0].size(),-1);//-1表示根结点 -2表示根节点且存在边缘结点
        for(int i=0;i<len;i++)
            uf[i]=-1;
        int count=0;
        for(int y=0;y<board.size();y++){
            for(int x=0;x<board[0].size();x++){
                if(board[y][x]=='O'){
                    //将O点存储并映射为index
                    Point *p =new Point(y,x);
                    m[*p]=count;
                    v.push_back(p);
                    if(isEdge(board,p)){
                        edge.push_back(p);
                        uf[count]=-2;
                    }
                    count++;
 
                    merge(m,uf,p);//将点p相邻点都融合
                }
 
            }
        }
        //遍历所有结点,将非边缘集合的点置为'X'
        for(int i=0;i<count;i++){
            int root=find(uf,i);
            if(uf[root]==-1){
                Point*p=v[i];
                board[p->y][p->x]='X';
            }
        }
    }
    int find(int*uf,int a){
        if(uf[a]<0)return a;
        return uf[a]=find(uf,uf[a]);
    }
    void join(int* uf,int a,int b){
        int root1=find(uf,a);
        int root2=find(uf,b);
        if(root1==root2)return;
        if(uf[root1]==-2||uf[root2]==-2){
            uf[root1]=root2;
            uf[root2]=-2;
        }else{
            uf[root1]=root2;
        }
 
    }
    void merge(map<Point,int>& m,int*uf,Point* p){
        Point p1=*p;
        int index_p=m[p1];
        //上 顺时针 y x
        int a[][2]={
            -1,0,
            0,1,
            1,0,
            0,-1
        };
        for(int i=0;i<4;i++){
            Point temp =Point(p1.y+a[i][0],p1.x+a[i][1]);
 
            if(m.find(temp)!=m.end()){
                join(uf,m[temp],index_p);
            }
        }
    }
 
    bool isEdge(vector<vector<char> >& board,Point *p){
        int y_len=board.size();
        int x_len=board[0].size();
        int x=p->x;
        int y=p->y;
        return x==0||x==x_len-1||y==0||y==y_len-1;
    }
};
```

暴力广搜

```c
class Solution {
public:
    struct Point {
    int y;
    int x;
    Point(int y, int x) :y(y), x(x) {
    }
};
vector<Point> BFS(vector<vector<char>> board, Point p) {
    vector<Point> v;
    queue<Point> a;
    a.push(p);
    board[p.y][p.x] = 'Z';
    int move[][2] = {
            -1,0,
            0,1,
            1,0,
            0,-1
    };
    while (!a.empty()) {
        Point p = a.front();
        a.pop();
        v.push_back(p);
        for (int i = 0; i < 4; i++) {
            int y = p.y + move[i][0];
            int x = p.x + move[i][1];
            if (y >= 0 && y < board.size() && x >= 0 && x < board[0].size()) {
                if (board[y][x] == 'O')
                    a.push(Point(y, x));
                    board[y][x] = 'Z';
            }
        }
    }
    return v;
}
bool isEdge(vector<vector<char> >& board, Point &p) {
    int y_len = board.size();
    int x_len = board[0].size();
    int x = p.x;
    int y = p.y;
    return x == 0 || x == x_len - 1 || y == 0 || y == y_len - 1;
 
}
void solve(vector<vector<char>>& board) {
    if (board.size() == 0)return;
    for (int y = 0; y < board.size(); y++) {
        for (int x = 0; x < board[0].size(); x++) {
            if (board[y][x] == 'O') {
                vector<Point> v = BFS(board, Point(y, x));
                int flag = 0;//不存在边界
                for (int i = 0; i < v.size(); i++) {
                    if (isEdge(board, v[i])) {
                        flag = 1;//存在边界
                        break;
                    }
                }
                if (flag == 0) {
                    for (int i = 0; i < v.size(); i++)
                        board[v[i].y][v[i].x] = 'X';
                }
            }
        }
    }
 
}
 
};
```

只测边缘的深搜

```c
class Solution {
public:
    struct Point{
        int y;
        int x;
        Point(int y,int x):y(y),x(x){
        }
    };
    void solve(vector<vector<char>>& board) {
        if(board.size()==0)return;
        int y_len=board.size();
        int x_len=board[0].size();
        for(int i=0;i<y_len;i++){
            if(board[i][0]=='O'){
                DFS(board,Point(i,0));
            }
        }
        for(int i=0;i<y_len;i++){
            if(board[i][x_len-1]=='O'){
                DFS(board,Point(i,x_len-1));
            }
        }
        for(int i=0;i<x_len;i++){
            if(board[0][i]=='O'){
                DFS(board,Point(0,i));
            }
        }
        for(int i=0;i<x_len;i++){
            if(board[y_len-1][i]=='O'){
                DFS(board,Point(y_len-1,i));
            }
        }
        for(int i=0;i<y_len;i++)
            for(int j=0;j<x_len;j++){
                if(board[i][j]=='O')
                    board[i][j]='X';
                else if(board[i][j]=='Z'){
                    board[i][j]='O';
                }
            }
 
    }
    void DFS(vector<vector<char> >& board,Point p){
        stack<Point> st;
        st.push(p);
        board[p.y][p.x]='Z';
        int move[][2]={
            -1,0,
            0,1,
            1,0,
            0,-1
        };
        while(!st.empty()){
            Point p = st.top();
            st.pop();
            for (int i = 0; i < 4; i++) {
                int y = p.y + move[i][0];
                int x = p.x + move[i][1];
                if (y >= 0 && y < board.size() && x >= 0 && x < board[0].size()) {
                    if (board[y][x] == 'O'){
                        st.push(Point(y, x));
                        board[y][x] = 'Z';
                    }
 
            }
        }
 
        }
 
 
    }
 
};
```

 

# 拓扑排序

##[207. 课程表](https://leetcode-cn.com/problems/course-schedule/)

现在你总共有 *n* 门课需要选，记为 `0` 到 `n-1`。

在选修某些课程之前需要一些先修课程。 例如，想要学习课程 0 ，你需要先完成课程 1 ，我们用一个匹配来表示他们: `[0,1]`

给定课程总量以及它们的先决条件，判断是否可能完成所有课程的学习？

**示例 1:**

```
输入: 2, [[1,0]]
输出: true
解释: 总共有 2 门课程。学习课程 1 之前，你需要完成课程 0。所以这是可能的。
```

```c
class Solution {
public:
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        int *indegree=new int[numCourses]();
        vector<int> *graph =new vector<int>[numCourses];
        queue<int> q;//入度为0的结点队列
        for(int i=0;i<prerequisites.size();i++){
            int begin=prerequisites[i][0];
            int end=prerequisites[i][1];
            graph[begin].push_back(end);
            indegree[end]++;
        }
        //初始化入度为0的结点队列
        for(int i=0;i<numCourses;i++){
            if(indegree[i]==0){
                indegree[i]=-1;//表示已经入队
                q.push(i);
            }
        }
        while(!q.empty()){
            //出队一个0结点
            int node_0=q.front();
            q.pop();
            for(int i=0;i<graph[node_0].size();i++){
                int end=graph[node_0][i];
                indegree[end]--;
                if(indegree[end]==0){
                    q.push(end);
                    indegree[end]=-1;
                }
            }
        }
        for(int i=0;i<numCourses;i++)
            if(indegree[i]!=-1)return false;
        return true;
    }
};
```

# 字典树

# 递归

## [17. 电话号码的字母组合](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)

给定一个仅包含数字 `2-9` 的字符串，返回所有它能表示的字母组合。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

![img](http://upload.wikimedia.org/wikipedia/commons/thumb/7/73/Telephone-keypad2.svg/200px-Telephone-keypad2.svg.png)

**示例:**

```
输入："23"
输出：["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
```

- hash tbale 加递归

  ```c
  class Solution {
  public:
      vector<string> letterCombinations(string digits) {
          vector<string> res;
          if(digits=="")return res ;
          map<char,string> table{
              {'0', " "}, {'1',"*"}, {'2', "abc"},
              {'3',"def"}, {'4',"ghi"}, {'5',"jkl"},
              {'6',"mno"}, {'7',"pqrs"},{'8',"tuv"},
              {'9',"wxyz"}
          };
   
          func(res,"",digits,0,table);
          return res;
      }
      void func(vector<string> &res,string str,string& digits,int k,map<char,string>& table){
          if(k==digits.size()){
              res.push_back(str);
              return;
          }
          string tmp=table[digits[k]];
          for(char c:tmp){
              str+=c;
              func(res,str,digits,k+1,table);
              str.pop_back();
          }
          return ;
      }
  };
  ```

 

## [22. 括号生成](https://leetcode-cn.com/problems/generate-parentheses/)

给出 *n* 代表生成括号的对数，请你写出一个函数，使其能够生成所有可能的并且**有效的**括号组合。

例如，给出 *n* = 3，生成结果为：

```
[
  "((()))",
  "(()())",
  "(())()",
  "()(())",
  "()()()"
]
```

```c
class Solution {
public:
    vector<string> generateParenthesis(int n) {
        vector<string> v;
        getRes(n,"",v);
        // for(int i=0;i<v.size();i++)
        //     cout<<v[i]<<endl;
        return v;
    }
    void getRes(int n,string s,vector<string> &v){
        //递归返回条件
        if(s.size()==2*n){
            v.push_back(s);
            return;
        }
        int num_left=getNum(s,'(');
        int num_right=getNum(s,')');
        if(num_left<n)
            getRes(n,s+'(',v);
        if(num_right<num_left)
            getRes(n,s+')',v);
    }
    int getNum(string &s,char c){
        int count=0;
        for(int i=0;i<s.size();i++){
            if(s[i]==c)
                count++;
        }
        return count;
    }
 
};
```

 

# 数组

## 1.两数之和

给定一个整数数组 `nums` 和一个目标值 `target`，请你在该数组中找出和为目标值的那 **两个** 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

**示例:**

```
给定 nums = [2, 7, 11, 15], target = 9
 
因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```

Hashmap

```c
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        map<int,int> m;
        vector<int> res;
        if(nums.empty())return res;
        for(int i=0;i<nums.size();i++){
            if(m.find(target-nums[i])!=m.end()){
                res.push_back(m[target-nums[i]]);
                res.push_back(i);
                break;
            }else m.insert(pair<int,int>(nums[i],i));
        }
       return res;
    }
};
```

##11. 盛最多水的容器

给定 *n* 个非负整数 *a*1，*a*2，...，*a*n，每个数代表坐标中的一个点 (*i*, *ai*) 。在坐标内画 *n* 条垂直线，垂直线 *i* 的两个端点分别为 (*i*, *ai*) 和 (*i*, 0)。找出其中的两条线，使得它们与 *x* 轴共同构成的容器可以容纳最多的水。

**说明：**你不能倾斜容器，且 *n* 的值至少为 2。

![img](https://aliyun-lc-upload.oss-cn-hangzhou.aliyuncs.com/aliyun-lc-upload/uploads/2018/07/25/question_11.jpg)

图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。

### 双指针法

[题解](<https://leetcode-cn.com/problems/container-with-most-water/solution/sheng-zui-duo-shui-de-rong-qi-by-leetcode/>)

```c
class Solution {
public:
    int maxArea(vector<int>& height) {
        int begin=0;
        int end=height.size()-1;
        int max=cal(begin,end,height);
        while(begin<end){
            int left=height[begin];
            int right=height[end];
            if(left>right)end--;
            else begin++;
            int temp=cal(begin,end,height);
            if(temp>max)max=temp;
        }
        return max;
    }
    int cal(int begin,int end,vector<int>&height){
        return min(height[begin],height[end])*(end-begin);
    }
};
```

##[15. 三数之和](https://leetcode-cn.com/problems/3sum/)

给定一个包含 *n* 个整数的数组 `nums`，判断 `nums` 中是否存在三个元素 *a，b，c ，*使得 *a + b + c =* 0 ？找出所有满足条件且不重复的三元组。

**注意：**答案中不可以包含重复的三元组。

### review

* 必须确定你的思路没有无法解决的问题再去实现,否者就是浪费时间

* 考虑先对数据排序会不会有助于解题

### 排序加双指针

```c
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
 
        //  注意一：
        if (nums.size() < 3) return {};
        set<vector<int> > result;
        sort(nums.begin(), nums.end());
        int sum = 0;
 
        for (int j = 1, i, k; j < nums.size() - 1; ++j) {
            i = 0;
            k = nums.size() - 1;
            while (i < j && j < k) {
                sum = nums[i] + nums[j] + nums[k];
 
                if (sum == 0) {
                    result.insert({nums[i], nums[j], nums[k]});
                    i++;
                    //  注意二：
                    if (nums[i] == nums[k]) break;
                }
                else if (sum > 0) k--;
                else if (sum < 0) i++;
            }
        }
        return vector<vector<int> >(result.begin(), result.end());
    }
 
};
```

 

## [16. 最接近的三数之和](https://leetcode-cn.com/problems/3sum-closest/)

给定一个包括 *n* 个整数的数组 `nums` 和 一个目标值 `target`。找出 `nums` 中的三个整数，使得它们的和与 `target` 最接近。返回这三个数的和。假定每组输入只存在唯一答案。

```
例如，给定数组 nums = [-1，2，1，-4], 和 target = 1.
 
与 target 最接近的三个数的和为 2. (-1 + 2 + 1 = 2).
```

排序加双指针

```c
class Solution {
public:
    int threeSumClosest(vector<int>& nums, int target) {
        sort(nums.begin(),nums.end());
        int min_div =INT_MAX;
        int res;
        for(int j=1,i,k;j<nums.size()-1;j++){
            i=0;
            k=nums.size()-1;
            while(i<j&&k>j){
                int num=nums[i]+nums[j]+nums[k];
                if(abs(num-target)<min_div){
                    min_div=abs(num-target);
                    res=num;
                }
                if(num>target)k--;
                if(num<target)i++;
                if(num==target)return num;
 
            }
        }
        return res;
    }
};
```

## [26. 删除排序数组中的重复项](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/)

给定一个排序数组，你需要在**原地**删除重复出现的元素，使得每个元素只出现一次，返回移除后数组的新长度。

不要使用额外的数组空间，你必须在**原地修改输入数组**并在使用 O(1) 额外空间的条件下完成。

**示例 1:**

```
给定数组 nums = [1,1,2],
 
函数应该返回新的长度 2, 并且原数组 nums 的前两个元素被修改为 1, 2。
 
你不需要考虑数组中超出新长度后面的元素。
```

* 冒泡交换

```c
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        if(nums.size()<2)return nums.size();
        int right=nums.size()-1;
        int left=1;
        while(left<=right){
            if(nums[left]==nums[left-1]){
                change(nums,left,right);
                right--;
            }else{
                left++;
            }
        }
        return right+1;
    }
    void change(vector<int>& nums,int left,int right){
        //从left不断交换到right
        while(left<right){
            int temp= nums[left];
            nums[left]=nums[left+1];
            nums[left+1]=temp;
            left++;
        }
    }
};
```

 

* 双指针 赋值

  任何时候冒泡的思想都是极为浪费时间的

  双指针法不需要冒泡交换,而是采用赋值

  每次遇到不同的值才去更新i的下一个

  ```c
  class Solution {
  public:
      int removeDuplicates(vector<int>& nums) {
          if(nums.size()==0)return 0;
          int i=0;
          for(int j=1;j<nums.size();j++){
              if(nums[i]!=nums[j]){
                  i++;
                  nums[i]=nums[j];
              }
          }
          return i+1;
      }
   
  };
  ```

## [27. 移除元素](https://leetcode-cn.com/problems/remove-element/)

给定一个数组 *nums* 和一个值 *val*，你需要**原地**移除所有数值等于 *val* 的元素，返回移除后数组的新长度。

不要使用额外的数组空间，你必须在**原地修改输入数组**并在使用 O(1) 额外空间的条件下完成。

元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

**示例 1:**

```
给定 nums = [3,2,2,3], val = 3,
 
函数应该返回新的长度 2, 并且 nums 中的前两个元素均为 2。
 
你不需要考虑数组中超出新长度后面的元素。
```

* 使用t26学会的双指针法

  ```c
  class Solution {
  public:
      int removeElement(vector<int>& nums, int val) {
          int i=-1;
          for(int j=0;j<nums.size();j++){
              if(nums[j]!=val){
                  i++;
                  nums[i]=nums[j];
              }
          }
          return i+1;
      }
  };
  ```

## [31. 下一个排列](https://leetcode-cn.com/problems/next-permutation/)

实现获取下一个排列的函数，算法需要将给定数字序列重新排列成字典序中下一个更大的排列。

如果不存在下一个更大的排列，则将数字重新排列成最小的排列（即升序排列）。

必须**原地**修改，只允许使用额外常数空间。

以下是一些例子，输入位于左侧列，其相应输出位于右侧列。
`1,2,3` → `1,3,2`
`3,2,1` → `1,2,3`
`1,1,5` → `1,5,1`

```c
class Solution {
public:
    bool cmp(int a,int b){
        return a>b;
    }
    void nextPermutation(vector<int>& nums) {
        if(nums.size()<2)return;
        int i,j;
        for(i=nums.size()-2;i>=0;i--){
            if(nums[i]<nums[i+1]){
                break;
            }
        }
        if(i==-1){
            sort(nums.begin()+i+1,nums.end());
            return;
        }
        for(j=nums.size()-1;j>=1;j--){
            if(nums[j]>nums[i]){
                swap(nums[j],nums[i]);//有说要选i右边最小的大于num[i]的,但其实第一个一定就是最小的
                break;
            }
        }
        sort(nums.begin()+i+1,nums.end());
    }
};
```

## [33. 搜索旋转排序数组](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)

* 二分法 旋转结点也采用二分法

假设按照升序排序的数组在预先未知的某个点上进行了旋转。

( 例如，数组 `[0,1,2,4,5,6,7]` 可能变为 `[4,5,6,7,0,1,2]` )。

搜索一个给定的目标值，如果数组中存在这个目标值，则返回它的索引，否则返回 `-1` 。

你可以假设数组中不存在重复的元素。

你的算法时间复杂度必须是 *O*(log *n*) 级别。

**mycode**

```c
class Solution {
public:
    int searchRotatedPivot(vector<int>& nums,int left,int right){
        if(nums[left]<nums[right])return 0;
        while(left<=right){
            int mid=(left+right)/2;
            if(mid-1>=0&&(mid+1>=nums.size()||(nums[mid]<nums[mid-1]&&nums[mid]<nums[mid+1])))return mid;
            else if(nums[mid]<nums[right]){
                right=mid-1;
            }else{
               left=mid+1;
            }
        }
        return -1;
    }
 
    int search_bin(vector<int>& nums,int left,int right,int tar){
        while(left<=right){
            int mid=(left+right)/2;
            if(nums[mid]==tar)return mid;
            else if(nums[mid]<tar){
                left=mid+1;
            }else{
                right=mid-1;
            }
        }
        return -1;
    }
    int search(vector<int>& nums, int target) {
        if(nums.size()==0)return -1;
        int mid_index;
        if(nums.size()==1)mid_index=0;
        else if(nums.size()==2)mid_index=1;
        else mid_index=searchRotatedPivot(nums,0,nums.size()-1);
 
        int a=search_bin(nums,0,mid_index-1,target);
        int b=search_bin(nums,mid_index,nums.size()-1,target);
        if(a!=-1)return a;
        else if(b!=-1)return b;
        else return -1;
    }
};
```

## [35. 搜索插入位置](https://leetcode-cn.com/problems/search-insert-position/)

总结:KIS，以完成需求为核心，最大化不必要的工作

如果不影响效率，代码多点也可以

题目：

给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

你可以假设数组中无重复元素。

示例 1:

输入: [1,3,5,6], 5
输出: 2

code

```从\
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        if(nums.size()==0)return 0;
        int i=0;
        while(i<nums.size()&&nums[i]<target)
            i++;
        return i;
    }
};
```

##[39. 组合总和](https://leetcode-cn.com/problems/combination-sum/)

给定一个无重复元素的数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。

candidates 中的数字可以无限制重复被选取。

说明：

所有数字（包括 target）都是正整数。
解集不能包含重复的组合。

题解:https://leetcode-cn.com/problems/combination-sum/solution/hui-su-suan-fa-jian-zhi-python-dai-ma-java-dai-m-2

做搜索、回溯问题的套路是画图，代码其实就是根据画出的树形图写出来的。

**对数组的修改,尽量在它做参数时进行**

```python
#39组合排序
class Solution:
    def combinationSum(self, candidates, target):
        candidates_sort=sorted(candidates)
        res=[]
        n=len(candidates_sort)
        def fun1(i,target1,arr):#candidates排序
            if target1<0 or i==n:
                return
            elif target1==0:
                res.append(arr)
            else:
                fun1(i,target1-candidates_sort[i],arr+[candidates_sort[i]])
                fun1(i+1,target1,arr)
        fun1(0,target,[])
        return res
sol=Solution()
res=sol.combinationSum([2,3,6,7],7)
print(res)
```

 

# 分治

# 数学

##整数反转

给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。

**示例 1:**

```
输入: 123
输出: 321
```

**示例 2:**

```
输入: -123
输出: -321
```

**示例 3:**

```
输入: 120
输出: 21
```

**注意:**

假设我们的环境只能存储得下 32 位的有符号整数，则其数值范围为 [−2^31,  2^31 − 1]。请根据这个假设，如果反转后整数溢出那么就返回 0。

**BUG**:

32位int的取值是-214748364**8**到-214748364**7**正负绝对值不同

不能对-2147483648直接取负值

```c
class Solution {
public:
    int reverse(int x) {
        int flag=0;
        long long x_l=x;
        if(x<0){
            flag=1;
            x_l=-x_l;
        }
        stringstream ss;
        string s;
        ss<<x_l;;
        ss>>s;
        string res1="";
        int a=s.size()-1;
        for(int i=a;i>=0;i--)
            res1+=s[i];
        ss.clear();
        ss<<res1;
        long long res;
        ss>>res;
        if(res<INT_MIN||res>INT_MAX)return 0;
        if(flag==1)res=-1*res;
        return res;
    }
};
```

## [12. 整数转罗马数字](https://leetcode-cn.com/problems/integer-to-roman/)

罗马数字包含以下七种字符： `I`， `V`， `X`， `L`，`C`，`D` 和 `M`。

```
字符          数值
I             1
V             5
X             10
L             50
C             100
D             500
M             1000
```

例如， 罗马数字 2 写做 `II` ，即为两个并列的 1。12 写做 `XII` ，即为 `X` + `II` 。 27 写做  `XXVII`, 即为 `XX` + `V` + `II` 。

通常情况下，罗马数字中小的数字在大的数字的右边。但也存在特例，例如 4 不写做 `IIII`，而是 `IV`。数字 1 在数字 5 的左边，所表示的数等于大数 5 减小数 1 得到的数值 4 。同样地，数字 9 表示为 `IX`。这个特殊的规则只适用于以下六种情况：

- `I` 可以放在 `V` (5) 和 `X` (10) 的左边，来表示 4 和 9。
- `X` 可以放在 `L` (50) 和 `C` (100) 的左边，来表示 40 和 90。
- `C` 可以放在 `D` (500) 和 `M` (1000) 的左边，来表示 400 和 900。

给定一个整数，将其转为罗马数字。输入确保在 1 到 3999 的范围内

**code**

### review

永远不能忍受重复,查表是解决重复的好方法

### 暴力

```c
class Solution {
public:
    string intToRoman(int num) {
        int temp;
        string s="";
        addRoman(s,"M",num/1000);
        num=num%1000;
        addRoman(s,"CM",num/900);
        num=num%900;
        addRoman(s,"D",num/500);
        num=num%500;
        addRoman(s,"CD",num/400);
        num=num%400;
        addRoman(s,"C",num/100);
        num=num%100;
        addRoman(s,"XC",num/90);
        num=num%90;
        addRoman(s,"L",num/50);
        num=num%50;
        addRoman(s,"XL",num/40);
        num=num%40;
        addRoman(s,"X",num/10);
        num=num%10;
        addRoman(s,"IX",num/9);
        num=num%9;
        addRoman(s,"V",num/5);
        num=num%5;
        addRoman(s,"IV",num/4);
        num=num%4;
        addRoman(s,"I",num);
        return s;
 
    }
    void addRoman(string &s,string roman,int num){
        for(int i=0;i<num;i++)
            s+=roman;
    }
};
```

 

### 查表

```CQL
class Solution {
public:
    string intToRoman(int num) {
        int temp;
        string s="";
        int values[]={1000,900,500,400,100,90,50,40,10,9,5,4,1};
        string reps[]={"M","CM","D","CD","C","XC","L","XL","X","IX","V","IV","I"};
        for(int i=0;i<13;i++){
            if(num>=values[i]){
                addRoman(s,reps[i],num/values[i]);
                num=num%values[i];
            }
        }
        return s;
 
    }
    void addRoman(string &s,string roman,int num){
        for(int i=0;i<num;i++)
            s+=roman;
    }
};
```

 

# 题目

## 寻找两个有序数组的中位数()

标签:

#### [寻找两个有序数组的中位数](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/)

题目

```
给定两个大小为 m 和 n 的有序数组 nums1 和 nums2。
 
请你找出这两个有序数组的中位数，并且要求算法的时间复杂度为 O(log(m + n))。
 
你可以假设 nums1 和 nums2 不会同时为空。
```

##[字符串转换整数 (atoi)](https://leetcode-cn.com/problems/string-to-integer-atoi/)

纯字符处理模拟,考验细心程度

## [13. 罗马数字转整数](https://leetcode-cn.com/problems/roman-to-integer/)

map基本应用

 

# 笔记

**首先**想到的**简单**方法往往时最好的方法

采用二分策略时,下标一定有可能到边界,因此在此之上如果有+或-操作,一定要考虑边界

##经典技巧

* 生成全排列

  * 递归

    ```c
    #include<iostream>
    #include<algorithm>
     
    using namespace std;
     
    int arry[3] = { 1,2,3 };
     
    void Recursion(int s, int t)
    {
        if (s == t)
            for_each(arry, arry + 3, [](int i) {printf("%d", i); }), printf("\n");
        else
        {
            for (int i = s; i <= t; i++)
            {
                swap(arry[i], arry[s]);
                Recursion(s + 1, t);
                swap(arry[i], arry[s]);
            }
        }
    }
     
    int main()
    {
     
        Recursion(0, 2);
     
        return 0;
    }
    ```

 

  * 字典序

首先看什么叫字典序，顾名思义就是按照字典的顺序（a-z, 1-9）。以字典序为基础，我们可以得出任意两个数字串的大小。比如 "1" < "12"<"13"。 就是按每个数字位逐个比较的结果。对于一个数字串，“123456789”， 可以知道最小的串是 从小到大的有序串“123456789”，而最大的串是从大到小的有序串“*987654321”。这样对于“123456789”的所有排列，将他们排序，即可以得到按照字典序排序的所有排列的有序集合。

于是可以有下面计算下一个排列的算法：
设P是1～n的一个全排列:p=p1p2......pn=p1p2......pj-1pjpj+1......pk-1pkpk+1......pn
　　1）从排列的右端开始，找出第一个比右边数字小的数字的序号j（j从左端开始计算），即 j=max{i|pi<pi+1}
　　2）在pj的右边的数字中，找出所有比pj大的数中最小的数字pk，即 k=max{i|pi>pj}（右边的数从右至左是递增的，因此k是所有大于pj的数字中序号最大者）
　　3）对换pi，pk

        4）再将pj+1......pk-1pkpk+1......pn倒转得到排列p'=p1p2.....pj-1pjpn.....pk+1pkpk-1.....pj+1，这就是排列p的下一个排列。

 

 

 

 

## 链表

重点理解链表中head的意义,head作为指针其指向的结点携带的数据究竟是一个**普通**的结点的数据还是有特殊含义的**头节点**

不夸张的说,所有链表类的题目添加完一个不存储数据的head结点后都变得简单

如果特别强调速度,可以牺牲空间换时间,这里有个注意事项,指针也可以作为对象保存在数组里([19. 删除链表的倒数第N个节点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/))

## 并查集

* 理解一种概念最好的方法莫过于弄懂它是为了做什么而被创造出来的:
* 这种数据结构用来表示集合信息，用以实现如确定某个集合**含有哪些元素**、判断某两个元素是否存在**同一个**集合中、求集合中元素的**数量**等问题。
* 使用数组下标表示元素时一定要注意有没有**负值**
* 同时在一些离散数据,使用数组下标表示元素的方法时,使用**两种不存在**的下标表示不存在元素和根元素
* 直接用map进行映射的好处,不需要另外的标志标识不存在,因为map可以查出是否存在 map直接使用parent的值而不是index
* 使用map映射,别忘了find,union传引用
* 统计集合个数:计集合个数就比较简单了，直接扫描一遍所有的结点，如果某个结点的祖先结点不是它自己，说明该结点是某个集合的祖先元素，统计这种结点个数即可
* 维护一个数组，代表以某个结点为根的树的结点数目，初始化为全1。在合并两个集合时，将秩较小的集合的元素数目加到秩较大的集合上。
* 使用map的话,**key**和**value**类型相同
* union时如果root1==root2返回是**必要的**,否则可能会出现自己指向自己这种死循环的可能

## BFS

* 注意一定不要忘记每次将点入队时,将其标记为**已访问**,否则会死循环 在入队时标记,不要再出队时标记(130. 被围绕的区域)

## 动态规划

1. 理论
   1. 问题是由交叠的子问题构成,这样的子问题出现在对给定问题求解的递推关系里,这个递推关系包含了相同类型的更小子问题的解
   2. 动态规划建议与其对交叠的子问题一次又一次求解,不如将其记录在表里
      1. 最优化法则,最优化问题任意实例的最优解都是由其子实例的最优解构成
2. dp数组存储什么要细细思考,有时不一定非要存储数值型数据,bool型也可以(139.单词拆分)
3. F[0]的意义也要考虑方便性来确定,有两种理解即1.**到第一个数**,2.**第一个数之前** 前者答案是F[size-1] 后者是F[size] 一般使用前者会方便一点,因为一般可以直接确定结果
4. 递推是一切,如果因为一些连续的要求直接用**结果**当作F[]的值无法与以前产生递推,可以考虑换一个值(152.乘积最大子序列)

## 递归

* 在遇到求某种类似不同排列的题目时,考虑用递归(22.括号生成)

##数组

##语法

### 结构体排序

### string

```c
//查找
if(haystack.find(needle)!=string::npos){
            return haystack.find(needle);
}else
return -1;
```

 

###vector

* 去重

```c
unordered_set<string> m(wordDict.begin(), wordDict.end());
```

 

###map

```c
#include <map>//要使用map,必须包含此头文件.
map<string,int> M;//定义一个完成从string到int映射的map
M.clear(); //清空一个map
M.find(b);//确定map中是否保存string对象b的映射,若没有函数返回M.end()
M[b] = idx; //若map中不存在string对象b的映射,则定义其映射为b映射为idx
idxb = M[b]; //若map中存在string对象b的映射,则读出该映射
iterator erase（iterator it);//通过一个条目对象删除
 
iterator erase（iterator first，iterator last）//删除一个范围
 
size_type erase(const Key&key);//通过关键字删除
 
map<int, int>::iterator iter;
    iter = _map.begin();
    while(iter != _map.end()) {
        cout << iter->first << " : " << iter->second << endl;
        iter++;
    }
 
//自定义对象做key需要重载<运算符 进行排序 默认递增
struct  Test
{
    int x;
    int y;
 
    bool operator < (const Test &o) const
    {
        return x < o.x || y < o.y;
    }
};
 
```

###queue

```
push(x) 将x压入队列的末端
pop() 弹出队列的第一个元素(队顶元素)，注意此函数并不返回任何值
front() 返回第一个元素(队顶元素)
back() 返回最后被压入的元素(队尾元素)
empty() 当队列为空时，返回true
size() 返回队列的长度
```

 

## BUG

* int* a=new int[num] 而不是int(num)

## 感悟

* 先把结果所有可能的类型列出来,例如涉及数组,考虑空,开头,结尾等

#附录源码