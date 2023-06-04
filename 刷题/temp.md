一些模板，考研使用。



## 链表相关

* 头插法
* 尾插法
* 快慢指针找中点
* 循环链表
* 链表排序
* 链表归并
* 链表反转
* dummy节点的使用

```C++
//返回的slow算是一个分界点，从slow断开，slow->next=nullptr
//刚好前面节点数大于等于后面：奇数个节点多
ListNode* findMid(ListNode* head){
    ListNode* fast=head->next,*slow=head;
    while(fast){
        if(!fast->next)break;
        slow=slow->next;
        fast=fast->next->next;
    }
    return slow;
}
//使用的话
ListNode* l2=slow->next;
slow->next=nullptr;//将链表断开
```











```C++
//leetcode里面的链表都是没有头结点的，考研里面我们使用的默认都有
//头节点dummy
void reverse(ListNode* head){
    ListNode* temp=head->next;
    head->next=nullptr;
    ListNode* a;
    while(temp){
        a=temp;
        a->next=head->next;
        head->next=a;
        temp=temp->next;
    }
}
```

```C++

//写是写出来了，但是还是不熟练,建议看官方的题解；虽然好，但是我怕会想不起来
//这个题还是自己写的容易理解些,删除链表里面的重复项
ListNode* depublicate(ListNode* head){
    if(!head||!head->next){
        return head;
    }
    //leetcode需要自己添加头节点dummy
    ListNode* dummy=new ListNode(0,head);
    ListNode* ans=dummy;
    ListNode* left=head,*right=head->next;
    int len=0;
    while(right){
        while(right&&right->val==left->val){
            len++;right=right->next;
        }
        if(len==0){
            dummy->next=left;
            left=right;
            dummy=dummy->next;
        }
        else{
            left=right;//must;当有重复的时候
            dummy->next=left;
            len=0;
        }
        if(right)right=right->next;
    }
    return ans->next;
}
```





* 环形链表142&&141



 链表是否有环？环的入口？环的元素个数？这种题目难的是就地算法，最简单的可以使用hashmap。



Floyd判圈算法/龟兔赛跑算法。      



一个很常用的快慢指针算法，这里的细节是quick/slow的初始化，没必要记这个细节。    

Floyd判圈法

**看看官方推导的那个结论，在环形链表II。**