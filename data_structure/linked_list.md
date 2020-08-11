# 链表

## 基本技能

链表相关的核心点

- null/nil 异常处理
- dummy node 哑巴节点
- 快慢指针
- 插入一个节点到排序链表
- 从一个链表中移除一个节点
- 翻转链表
- 合并两个链表
- 找到链表的中间节点

## 常见题型

### [remove-duplicates-from-sorted-list](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

> 给定一个排序链表，删除所有重复的元素，使得每个元素只出现一次。

```c++
ListNode* deleteDuplicates(ListNode* head){
    ListNode* current = head;
    while(current != nullptr){
        if(current->next != nullptr && current->val == currnet->next->val)
            current->next = current->next->next;
        else
        	current = current->next;
    }
    return head;
}
```



### [remove-duplicates-from-sorted-list-ii](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)

> 给定一个排序链表，删除所有含有重复数字的节点，只保留原始链表中   没有重复出现的数字。

思路：链表头结点可能被删除，所以用 dummy node 辅助删除

```c++
ListNode* deleteDuplicates(ListNode* head){
    if(head == nullptr)
        return head;
    
    ListNode* newList = new ListNode(0);
    //给原链表添加一个头节点，方便删除原本的头节点
    newList->next = head;
    head = newList;
    while(head->next != nullptr && head->next->next != nullptr){
        if(head->next->val == head->next->next->val){
            int rmVal = head->next->val;
            while(head->next != nullptr && head->next->val == rmVal){
                head->next = head->next->next;
            }
        }
        else
            head = head->next;
    }
    return newList->next;
}
```

注意点
• A->B->C 删除 B，A.next = C
• 删除用一个 Dummy Node 节点辅助（允许头节点可变）
• 访问 X.next 、X.value 一定要保证 X != nil

### [reverse-linked-list](https://leetcode-cn.com/problems/reverse-linked-list/)

> 反转一个单链表。

思路：用一个 prev 节点保存向前指针，temp 保存向后的临时指针

```c++
ListNode* reverseList(ListNode* head){
    ListNode* prev;
    while(head != nullptr){
        // nullptr <-1    2->3->4
       	//		  prev   head
        ListNode* tmp = head->next;
        head->next = prev;
        prev = head;
        head = tmp;
    }
    return prev;
}
```

### [reverse-linked-list-ii](https://leetcode-cn.com/problems/reverse-linked-list-ii/)

> 反转从位置  *m*  到  *n*  的链表。请使用一趟扫描完成反转。

思路：先遍历到 m 处，翻转，再拼接后续，注意指针处理

```c++
ListNode* reverseBetween(ListNode* head, int m, int n) {
    if(head==NULL || n == 1 || n == m)
        return head;

    //增加一个头节点
    ListNode* result = new ListNode(0);
    result->next = head;
    head = result;

    ListNode* pre;
    int i=0;
    while(i<m){
        pre = head;
        head = head->next;
        i++;
    }
    ListNode* reBegin;
    // ListNode* reHead = st->next;
    ListNode* reEnd = head;
    //开始反转
    while(head != NULL && i<=n){
        ListNode* tmp = head->next;
        head->next = reBegin;
        reBegin = head;
        head = tmp;
        i++;
    }
    pre->next = reBegin;
    reEnd->next = head;
    return result->next;    
}
```



### [merge-two-sorted-lists](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

> 将两个升序链表合并为一个新的升序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。

思路：通过 dummy node 链表，连接各个元素

```c++
ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
    ListNode* head = new ListNode(0);
    ListNode* result = head;
    while(l1 != nullptr && l2 != nullptr){
        if(l1->val < l2->val){
            head->next = l1;
            l1 = l1->next;
        }
        else{
            head->next = l2;
            l2 = l2->next;
        }
        head = head->next;
    }
    while(l1){
        head->next = l1;
        head = head->next;
        l1 = l1->next;
    }
    while(l2){
        head->next = l2;
        head = head->next;
        l2 = l2->next;
    }
    return result->next;
}
```

### [partition-list](https://leetcode-cn.com/problems/partition-list/)

> 给定一个链表和一个特定值 x，对链表进行分隔，使得所有小于  *x*  的节点都在大于或等于  *x*  的节点之前。

思路：将大于 x 的节点，放到另外一个链表，最后连接这两个链表

```c++
ListNode* partition(ListNode* head, int x) {
    ListNode* dummy = new ListNode(0);
    ListNode* dummyHead = dummy;
    ListNode* p = new ListNode(0);
    p->next = head;
    head = p;
    while(p->next){
        if(p->next->val < x){
            p = p->next;
        }
        else{
            //放到另一个链表
            dummy->next = p->next;
            dummy = dummy->next;
            //删除该节点
            p->next = p->next->next;
        }
    }
    
    //拼接链表
    dummy->next = nullptr;
    p->next = dummyHead->next;

    return head->next;
}
```

哑巴节点使用场景

> 当头节点不确定的时候，使用哑巴节点

### [sort-list](https://leetcode-cn.com/problems/sort-list/)

> 在  *O*(*n* log *n*) 时间复杂度和常数级空间复杂度下，对链表进行排序。

思路：归并排序，找中点和合并操作

```c++
ListNode* sortList(ListNode* head) {
    return mergeSort(head);
}

ListNode* mergeTwoList(ListNode* l1, ListNode* l2){
    ListNode* result = new ListNode(0);
    ListNode* head = result;
    while(l1 != NULL && l2 !=NULL){
        if(l1->val < l2->val){
            head->next = l1;
            l1 = l1->next;
        }
        else{
            head->next = l2;
            l2 = l2->next;
        }
        head = head->next;
    }
    while(l1){
        head->next = l1;
        head = head->next;
        l1 = l1->next;
    }
    while(l2){
        head->next = l2;
        head = head->next;
        l2 = l2->next;
    }
    return result->next;
}

ListNode* mergeSort(ListNode *head){
    if(head == NULL || head->next == NULL)
        return head;

    //find middle
    ListNode* slow = head;
    ListNode* fast = head->next;
    while(fast != NULL && fast->next != NULL){
        fast = fast->next->next;
        slow = slow->next;
    }
    //slow为中点
    ListNode* tail = slow->next;
    slow->next = NULL;
    ListNode* left = mergeSort(head);
    ListNode* right = mergeSort(tail);
    return mergeTwoList(left, right);
}
```

注意点

- 快慢指针 判断 fast 及 fast.Next 是否为 nil 值
- 递归 mergeSort 需要断开中间节点
- 递归返回条件为 head 为 nil 或者 head.Next 为 nil

### [reorder-list](https://leetcode-cn.com/problems/reorder-list/)

> 给定一个单链表  *L*：*L*→*L*→…→*L\_\_n*→*L*
> 将其重新排列后变为： *L*→*L\_\_n*→*L*→*L\_\_n*→*L*→*L\_\_n*→…

思路：找到中点断开，翻转后面部分，然后合并前后两个链表

```c++
void reorderList(ListNode* head) {
    if(head == nullptr || head->next == nullptr)
        return;

    //find mid
    ListNode* slow = head;
    ListNode* fast = head->next;
    while(fast != nullptr && fast->next != nullptr){
        fast = fast->next->next;
        slow = slow->next;
    }
    ListNode* tail = reverseList(slow->next);
    slow->next = nullptr;
    head = mergeTwoList(head, tail);
    return;
}

ListNode* reverseList(ListNode* head){
    ListNode* pre;
    while(head){
        ListNode* tmp = head->next;
        head->next = pre;
        pre = head;
        head = tmp;
    }
    return pre;
}

ListNode* mergeTwoList(ListNode* l1, ListNode* l2){
    ListNode* result = new ListNode(0);
    ListNode* head = result;
    bool toggle = true;
    while(l1 != nullptr && l2 != nullptr){
        if(toggle){
            head->next = l1;
            l1 = l1->next;
        }
        else{
            head->next = l2;
            l2 = l2->next;
        }
        toggle = !toggle;
        head = head->next;
    }
    while(l1){
        head->next = l1;
        head = head->next;
        l1 = l1->next;
    }
    while(l2){
        head->next = l2;
        head = head->next;
        l2 = l2->next;
    }
    return result->next;
}
```



### [linked-list-cycle](https://leetcode-cn.com/problems/linked-list-cycle/)

> 给定一个链表，判断链表中是否有环。

思路：快慢指针，快慢指针相同则有环，证明：如果有环每走一步快慢指针距离会减 1
![fast_slow_linked_list](https://img.fuiboom.com/img/fast_slow_linked_list.png)

```c++
bool hasCycle(ListNode* head){
    if(head == nullptr)
        return false;

    ListNode* fast = head->next;
    ListNode* slow = head;
    
    while(fast != nullptr && fast->next != nullptr){
        if(fast == slow)
            return true;
        fast = fast->next->next;
        slow = slow->next;
    }
    return false;
}
```



### [linked-list-cycle-ii](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

> 给定一个链表，返回链表开始入环的第一个节点。  如果链表无环，则返回  `null`。

思路：快慢指针，快慢相遇之后，慢指针回到头，快慢指针步调一致一起移动，相遇点即为入环点
![cycled_linked_list](https://img.fuiboom.com/img/cycled_linked_list.png)

fast走的节点数 = 2 * slow走的节点数

F + n(a + b) + a = 2(F + a)

(n-1)(a + b) + b = F

得 F =b + (n-1)圈的环

```c++
ListNode* detectCycle(ListNode* head){
    if(head == nullptr)
        return head;
    ListNode* fast = head->next;
    ListNode* slow = head;
    while(fast != nullptr && fast->next != nullptr){
        if(fast == slow){
            fast = head;
            slow = slow->next;
            while(fast != slow){
                fast = fast->next;
                slow = slow->next;
            }
            return slow
        }
        fast = fast->next->next;
        slow = slow->next;
    }
    return nullptr;
}
```

坑点

- 指针比较时直接比较对象，不要用值比较，链表中有可能存在重复值情况
- 第一次相交后，快指针需要从下一个节点开始和头指针一起匀速移动

另外一种方式是 fast=head,slow=head

```c++
ListNode* detectCycle(ListNode* head){
    if(head == nullptr)
        return head;
    ListNode* fast = head；
    ListNode* slow = head;
    while(fast != nullptr && fast->next != nullptr){
        fast = fast->next->next;
        slow = slow->next;
        if(fast == slow){
            fast = head;
            while(fast != slow){
                fast = fast->next;
                slow = slow->next;
            }
            return slow;
        }
    }
    return nullptr
}
```

这两种方式不同点在于，**一般用 fast=head.Next 较多**，因为这样可以知道中点的上一个节点，可以用来删除等操作。

- fast 如果初始化为 head.Next 则中点在 slow.Next
- fast 初始化为 head,则中点在 slow

### [palindrome-linked-list](https://leetcode-cn.com/problems/palindrome-linked-list/)

> 请判断一个链表是否为回文链表。

```c++
bool isPalindrome(ListNode* head){
    if(head == nullptr){
        return true;
    }
    ListNode* fast = head->next;
    ListNode* slow = head;
    while(fast != nullptr && fast->next != nullptr){
        fast = fast->next->next;
        slow = slow->next;
    }
    ListNode* tail = reverseList(slow->next);

    slow->next = nullptr;
    while(head != nullptr && tail != nullptr){
        if(head->val != tail->val){
            return false;
        }
        head = head->next;
        tail = tail->next;
    }

    return true;
}

ListNode* reverseList(ListNode* head){
    if(head == nullptr)
        return head;
    ListNode* pre = nullptr;
    while(head != nullptr){
        ListNode* tmp = head->next;
        head->next = pre;
        pre = head;
        head = tmp;
    }
    return pre;
}
```



### [copy-list-with-random-pointer](https://leetcode-cn.com/problems/copy-list-with-random-pointer/)

> 给定一个链表，每个节点包含一个额外增加的随机指针，该指针可以指向链表中的任何节点或空节点。
> 要求返回这个链表的 深拷贝。

思路：1、hash 表存储指针，2、复制节点跟在原节点后面

```c++
Node* copyRandomList(Node* head) {
    if(head == NULL)
        return head;
    
    // 复制节点，紧挨到到后面
	// 1->2->3  ==>  1->1'->2->2'->3->3'
    Node* cur = head;
    while(cur != NULL){
        Node* dummyNode = new Node(cur->val);
        dummyNode->next = cur->next;
        cur->next = dummyNode;
        cur = dummyNode->next;
    }
	//处理random指针
    cur = head;
    while(cur != NULL){
        if(cur->random != NULL)
            cur->next->random = cur->random->next;
        cur = cur->next->next;
    }
	//分离两个链表
    cur = head;
    Node* result = cur->next;
    while(cur != NULL && cur->next != NULL){
        Node* tmp = cur->next;
        cur->next = cur->next->next;
        cur = tmp;
    }
	// 原始链表头：head 1->2->3
	// 克隆的链表头：cloneHead 1'->2'->3'
    return result;
}
```



## 总结

链表必须要掌握的一些点，通过下面练习题，基本大部分的链表类的题目都是手到擒来~

- null/nil 异常处理
- dummy node 哑巴节点
- 快慢指针
- 插入一个节点到排序链表
- 从一个链表中移除一个节点
- 翻转链表
- 合并两个链表
- 找到链表的中间节点

## 练习

- [ ] [remove-duplicates-from-sorted-list](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)
- [ ] [remove-duplicates-from-sorted-list-ii](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)
- [ ] [reverse-linked-list](https://leetcode-cn.com/problems/reverse-linked-list/)
- [ ] [reverse-linked-list-ii](https://leetcode-cn.com/problems/reverse-linked-list-ii/)
- [ ] [merge-two-sorted-lists](https://leetcode-cn.com/problems/merge-two-sorted-lists/)
- [ ] [partition-list](https://leetcode-cn.com/problems/partition-list/)
- [ ] [sort-list](https://leetcode-cn.com/problems/sort-list/)
- [ ] [reorder-list](https://leetcode-cn.com/problems/reorder-list/)
- [ ] [linked-list-cycle](https://leetcode-cn.com/problems/linked-list-cycle/)
- [ ] [linked-list-cycle-ii](https://leetcode-cn.com/problems/linked-list-cycle-ii/)
- [ ] [palindrome-linked-list](https://leetcode-cn.com/problems/palindrome-linked-list/)
- [ ] [copy-list-with-random-pointer](https://leetcode-cn.com/problems/copy-list-with-random-pointer/)
