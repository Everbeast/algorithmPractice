# 二叉树

## 知识点

### 二叉树遍历

**前序遍历**：**先访问根节点**，再前序遍历左子树，再前序遍历右子树
**中序遍历**：先中序遍历左子树，**再访问根节点**，再中序遍历右子树
**后序遍历**：先后序遍历左子树，再后序遍历右子树，**再访问根节点**

注意点

- 以根访问顺序决定是什么遍历
- 左子树都是优先右子树

#### 递归遍历模版

```cpp
void preorder(TreeNode* root) {
    if(root==nullptr)
        return;
    std::cout<<root->val<<std::endl;
    preorder(root->left);
    preorder(root->right);
}

void inorder(TreeNode* root){
    if(root==nullptr)
      return;
    inorder(root->left);
    std::cout<<root->val<<std::endl;
    inorder(root->right);
}

void postorder(TreeNode* root){
    if(root==nullptr)
      return;
    postorder(root->left);
    postorder(root->right);
    std::cout<<root->val<<std::endl;
}
```

#### 前序非递归

```c++
#遍历结果存在一个vector中
vector<int> preorder(TreeNode* root){
	if(root==nullptr)
		return;
    stack<TreeNode*> st;
    vector<int> result;
    while(root!=nullptr || !st.empty()){
      while(root!=nullptr){
        result.push_back(root->val);
        st.push(root);
        root=root->left;
      }
      TreeNode node = st.top();
      st.pop();
      root=node->right;
    }
    return result;
}
```

#### 中序非递归

```c++
vector<int> inorder(TreeNode* root){
    if(root==nullptr)
        return;
    stack<TreeNode*> st;
    vector<int> result;
    while(!st.empty() || root!=nullptr){
        while(root!=nullptr){
            st.push(root);
            root=root->left;
        }
        TreeNode* node = st.top();
        st.pop();
        result.push_back(node->val);
        root=root->right;
    }
    return result;
}
```

#### 后序非递归

```c++
vector<int> postorder(TreeNode* root){
    if(root==nullptr)
        return;
    vector<int>result;
    stack<TreeNode*> st;
    TreeNode *lastVisit;
    while(root!=nullptr || !st.empty()){
        while(root!=nullptr){
            st.push(root);
            root=root->left;
        }
        TreeNode* node = st.top();//no pop 
        if(node->right==nullptr || node->right==lastVisit){
            st.pop();
            result.push_back(node->val);
            lastVisit=node; // marked last visit
        }
        else{
            node = node->right;
        }
    }
    return result;
}
```

注意点

- 核心就是：根节点必须在右节点弹出之后，再弹出

#### DFS 深度搜索-从上到下

```c++
//definition for a binary tree node
struct TreeNode{
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) :val(x), left(nullptr), right(nullptr){}
};

vector<int> preorder(TreeNode* root){
    vector<int> result;
    dfs(root, &result);
    return result;
}

void dfs(TreeNode *root, vector<int>& result){
    if(root==nullptr)
        return;
    result.push_back(root->val);
    dfs(root->left, result);
    dfs(root->right, result);
}
```



#### DFS 深度搜索-从下向上（分治法）

```c++
vector<int> preorder(TreeNode* root){
    return dfs(root);
}

vector<int> dfs(TreeNode* root){
    vector<int> result;
    if(root==nullptr)
        return result;
    vector<int> left = dfs(root->left);
    vector<int> right = dfs(root->right);
    result.push_back(root->val);
    result.insert(result.end(), left.begin(), left.end());
    result.insert(result.end(), right.begin(), right.end());
    return result;
}
```

注意点：

> DFS 深度搜索（从上到下） 和分治法区别：前者一般将最终结果通过指针参数传入，后者一般递归返回结果最后合并

#### BFS 层次遍历

```c++
vector<int> bfs(TreeNode* root){
    vector<int> result;
    if(root==nullptr)
        return result;
    queue<TreeNode*> que;
    que.push(root);
    while(!que.empty()){
        int cnt=que.size();
        for(int i=0;i<cnt;i++){
            TreeNode* node = que.top();
            que.pop();
            result.push_back(node->val);
            if(node->left) que.push(node->left);
            if(node-right) que.push(node->right);
        }
    }
    return result;
}
```

### 分治法应用

先分别处理局部，再合并结果

适用场景

- 快速排序
- 归并排序
- 二叉树相关问题

分治法模板

- 递归返回条件
- 分段处理
- 合并结果

```c++
ResultType traversal(TreeNode* root){
    if(root==nullptr)
        return;
    ResultType left = traversal(root->left);
    ResultType right = traversal(root->right);
    
    ResultType result = merge left and right;
    return result;
}
```


#### 典型示例

```go
vector<int> preorder(TreeNode* root){
    return divideAndConquer(root);
}

vector<int> divideAndConquer(TreeNode* root){
    vector<int> result;
    if(root==nullptr)
        return result;
    vector<int> left = dfs(root->left);
    vector<int> right = dfs(root->right);
    result.push_back(root->val);
    result.insert(result.end(), left.begin(), left.end());
    result.insert(result.end(), right.begin(), right.end());
    return result;
}
```

#### 归并排序  

```c++
vector<int> MergeSort(vector<int> nums){
    return mergeSort(nums, 0, nums.size())
}
vector<int> mergeSort(vector<int> nums, int start, int end){
   	vector<int> result;
    if(end-start<=1)
        return result;
    int mid = (end-start)/2;
    vector<int> left = MergeSort(nums, start, mid);
    vector<int> right = MergeSort(nums, mid+1, end);
    result = merge(left, right);
    return result;
}

vector<int> merge(vector<int>left, vector<int>right){
    vector<int> result;
    int l=0;
    int r=0;
    while(l<left.size() && r<right.size()){
        if(left[l]<right[r]){
            result.push_back(left[l]);
            l++;
        }
        else{
            result.push_back(right[r]);
            r++;
        }
    }
    if(l<left.size())
        result.insert(result.end(), left.begin()+l, left.end());
    if(r<right.size())
        result.insert(result.end(), right.begin()+l, right.end());
    return result;
}
```

注意点

> 递归需要返回结果用于合并

#### 快速排序  

```c++
vector<int> QuickSort(vector<int>& nums){
    quickSort(nums, 0, nums.size()-1);
    return nums;
}

void quickSort(vector<int>& nums, int start, int end){
    if(start<end){
        p = patition(nums, start, end);
        quickSort(nums, start, p-1);
        quickSort(nums, p+1, end);
    }
}

//way1:
int partition(vector<int>& nums, int start, int end){
    int p = nums[end];
    int i = start;
    int j = end;
    while(i<j){
        while(nums[i]<p && i<j) i++;
        if(i<j)
            nums[j] = nums[i];
        while(nums[j]>p && i<j) j--;
        if(i<j)
            nums[i] = nums[i];
    }
    return i;
}
//way2:
int partition(vector<int>& nums, int start, int end){
    int p = nums[end];
    int i = start;
    for(int j = start; j<end; j++){
        if(nums[j]<p){
            swap(nums, i, j);
            i++;
        }
    }
    //找到pivot的位置
    swap(nums, i, end);
    return i;
}

void swap(vector<int>& nums, int i, int j){
    int t = nums[i];
    nums[i] = nums[j];
    nums[j] = t;
}
```

注意点：

> 快排由于是原地交换所以没有合并过程
> 传入的索引是存在的索引（如：0、length-1 等），越界可能导致崩溃

常见题目示例

#### maximum-depth-of-binary-tree

[maximum-depth-of-binary-tree](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

> 给定一个二叉树，找出其最大深度。

思路：分治法

```c++
int maxDepth(TreeNode* root){
    if(root == nullptr)
        return 0;
    int left = maxDepth(root->left);
    int right = maxDepth(root->right);
    
    return left>right ? left+1 : right+1;
}
```



#### balanced-binary-tree

[balanced-binary-tree](https://leetcode-cn.com/problems/balanced-binary-tree/)

> 给定一个二叉树，判断它是否是高度平衡的二叉树。

思路：分治法，左边平衡 && 右边平衡 && 左右两边高度 <= 1，
因为需要返回是否平衡及高度，要么返回两个数据，要么合并两个数据，
所以用-1 表示不平衡，>0 表示树高度（二义性：一个变量有两种含义）。

```c++
bool isBalanced(TreeNode* root){
    if(maxDepth(root) == -1)
        return false;
    return true;
}

int maxDepth(TreeNode* root){
    if(root == nullptr)
        return 0;
    
    int left = maxDepth(root->left);
    int right = maxDepth(root->right);
    
    if(left == -1 || right == -1 || left-right > 1 || right-left > 1){
        return -1;
    }
    
    return left>right ? left+1 : right+1; 
}
```

注意

> 一般工程中，结果通过两个变量来返回，不建议用一个变量表示两种含义

#### binary-tree-maximum-path-sum

[binary-tree-maximum-path-sum](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)

> 给定一个**非空**二叉树，返回其最大路径和。

思路：分治法. 三种情况

1.  a + b + c
2.  b + a + 往上
3.  c + a + 往上

```c++
//	  /
//	 a
//  / \
// b   c

int maxDepthSum(TreeNode* root){
    int maxPath=INT_MIN;
    helper(root, maxPath);
    return maxPath;
}
int helper(TreeNode* root, int& maxPath){
    if(root==nullptr)
        return 0;
    
    //divide
    int left = max(helper(root->left, maxPath), 0);
    int right = max(helper(root->right, maxPath), 0);
    
    //conquer
    int newPathSum = root->val + left + right;
    maxPath = max(maxPath, newPathSum);
    //这里return的值卡得比较久  选择左右子树较大的 加上当前节点的值才能往上传，若左右都选不联通root的父亲节点 上传该值就是错的   
    return root->val + max(left, right);
}
```



#### lowest-common-ancestor-of-a-binary-tree

[lowest-common-ancestor-of-a-binary-tree](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

> 给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

思路：分治法，有左子树的公共祖先或者有右子树的公共祖先，就返回子树的祖先，否则返回根节点

```c++
TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q){
    if(root == nullptr)
        return nullptr;
    
    if(root == p || root == q){
        return root;
    }
    
    //divide
    TreeNode* left = lowestCommonAncestor(root->left, p, q);
    TreeNode* right = lowestCommonAncestor(root->right, p, q);
    
    //conquer
   	if(left != nullptr && right != nullptr)
        return root;
    if(left != nullptr)
        return left;
    if(right != nullptr)
        return right;
    return nullptr;
}
```



### BFS 层次应用

#### binary-tree-level-order-traversal

[binary-tree-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

> 给你一个二叉树，请你返回其按  **层序遍历**  得到的节点值。 （即逐层地，从左到右访问所有节点）

思路：用一个队列记录一层的元素，然后扫描这一层元素添加下一层元素到队列（一个数进去出来一次，所以复杂度 O(logN)）

```c++
vector<vector<int>> levelOrder(TreeNode* root){
   	vector<vector<int>> result;
    if(root == nullptr)
        return result;
    queue<TreeNode*> que;
    que.push(root);
    while(!que.empty()){
        int cnt = que.size();
        vector<int> tmp;
        for(int i = 0; i < cnt; i++){
            //出列
            TreeNode* node = que.front();que.pop();
            tmp.push_back(node->val);
            if(node->left) que.push(node->left);
            if(node->right) que.push(node->right);
        }
        result.push_back(tmp);
    }
    return result;
}
```



#### binary-tree-level-order-traversal-ii

[binary-tree-level-order-traversal-ii](https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/)

> 给定一个二叉树，返回其节点值自底向上的层次遍历。 （即按从叶子节点所在层到根节点所在的层，逐层从左向右遍历）

思路：在层级遍历的基础上，翻转一下结果即可

```c++
vector<vector<int>> levelOrder(TreeNode* root){
   	vector<vector<int>> result;
    if(root == nullptr)
        return result;
    queue<TreeNode*> que;
    que.push(root);
    while(!que.empty()){
        int cnt = que.size();
        vector<int> tmp;
        for(int i = 0; i < cnt; i++){
            //出列
            TreeNode* node = que.front();que.pop();
            tmp.push_back(node->val);
            if(node->left) que.push(node->left);
            if(node->right) que.push(node->right);
        }
        result.push_back(tmp);
    }
    reverse(result.begin(), result.end());
    return result;
}
```

#### binary-tree-zigzag-level-order-traversal

[binary-tree-zigzag-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)

> 给定一个二叉树，返回其节点值的锯齿形层次遍历。Z 字形遍历

```c++
vector<vector<int>> levelOrder(TreeNode* root){
   	vector<vector<int>> result;
    if(root == nullptr)
        return result;
    queue<TreeNode*> que;
    que.push(root);
    bool reverseFlag = false;
    while(!que.empty()){
        int cnt = que.size();
        vector<int> tmp;
        for(int i = 0; i < cnt; i++){
            //出列
            TreeNode* node = que.front();que.pop();
            tmp.push_back(node->val);
            if(node->left) que.push(node->left);
            if(node->right) que.push(node->right);
        }
        if(reverseFlag)
            reverse(tmp.begin(), tmp.end());
        reverseFlag = !reverseFlag;
        result.push_back(tmp);
    }
    return result;
}
```

### 二叉搜索树应用

#### validate-binary-search-tree

[validate-binary-search-tree](https://leetcode-cn.com/problems/validate-binary-search-tree/)

> 给定一个二叉树，判断其是否是一个有效的二叉搜索树。

思路 1：中序遍历，检查结果列表是否已经有序

思路 2：分治法，判断左 MAX < 根 < 右 MIN

```c++
//way1
bool isValidBST(TreeNode* root){
    vector<int> result;
    inOrder(root, result);
    for(int i = 0; i < result.size()-1; i++){
        if(result[i] >= result[i+1])
            return false;
    }
    return true;
}

void inOrder(TreeNode* root, vector<int>& result){
    if(root == nullptr)
        return;
    inOrder(root->left);
    result.push_back(root->val);
    inOrder(root->right);
}
```

```c++
//way2
struct ResultType{
    bool IsValid;
    TreeNode* MaxNode = nullptr;
    TreeNode* MinNode = nullptr;
};

bool isValidBST(TreeNode* root){
   	ResultType result = helper(root);
    return result.IsValid;
}

ResultType helper(TreeNode* root){
    ResultType result;
    if(root == nullptr){
        result.IsValid = true;
        return result;
    }
    
    //divide
    ResultType left = helper(root->left);
    ResultType right = helper(root->right);
   	
    //conquer
    if(!left.IsValid || !right.IsValid){
        result.IsValid = false;
        return result;
    }
    if(left.MaxNode != nullptr && left.MaxNode->val >= root->val){
        result.IsValid = false;
        return result;
    }
    if(right.MinNode != nullptr && right.MinNode->val <= root->val){
        result.IsValid = false;
        return result;
    }
    
    //通过考验
    result.IsValid = true;
    
    //更新最大最小node
    result.MinNode = root;
    if(left.MinNode != nullptr)
        result.MinNode = left.MinNode;
    
    result.MaxNode = root;
    if(right.MaxNode != nullptr)
        result.MaxNode = right.MaxNode;
    
    return result;
}
```

#### insert-into-a-binary-search-tree

[insert-into-a-binary-search-tree](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)

> 给定二叉搜索树（BST）的根节点和要插入树中的值，将值插入二叉搜索树。 返回插入后二叉搜索树的根节点。

思路：找到最后一个叶子节点满足插入条件即可

```c++
//DFS查找插入位置
TreeNode* insertIntoBST(TreeNode* root, int val){
    if(root == nullptr){
        root = new TreeNode(val);
        return root;
    }
    if(root->val > val)
        root->left = insertIntoBST(root->left, val);
    else
        root->right = insertIntoBST(root->right, val);
    return root;
}
```



## 总结

- 掌握二叉树递归与非递归遍历
- 理解 DFS 前序遍历与分治法
- 理解 BFS 层次遍历

## 练习

- [ ] [maximum-depth-of-binary-tree](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)
- [ ] [balanced-binary-tree](https://leetcode-cn.com/problems/balanced-binary-tree/)
- [ ] [binary-tree-maximum-path-sum](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)
- [ ] [lowest-common-ancestor-of-a-binary-tree](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)
- [ ] [binary-tree-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)
- [ ] [binary-tree-level-order-traversal-ii](https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/)
- [ ] [binary-tree-zigzag-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)
- [ ] [validate-binary-search-tree](https://leetcode-cn.com/problems/validate-binary-search-tree/)
- [ ] [insert-into-a-binary-search-tree](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)
