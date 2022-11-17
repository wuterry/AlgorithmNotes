## 题目

**[19] Remove Nth Node From End of List**

Description: Given the head of a linked list, remove the n^th node from the end of the list and return its head.

## 思路

采用**快慢指针法**，设置快指针 fast 和慢指针 slow, 具体步骤为:

1. 先移动快指针 $n$ 步, 使 slow 与 fast 间距为 n 个结点；
2. 相同速率同步移动 slow 和 fast, 当快指针 fast 移动到尾结点时，则慢指针的后一个结点即为需要删除的结点.

<font color="red">注意事项与技巧</font>

- 设置一个空头结点 dummy_head, 避免删除的结点为头结点 head 时导致返回出错；
- 当 n 大于链表长度时， 返回 NULL.

## 代码

```C++
ListNode *removeNthFromEnd(ListNode *head, int n) {
  if (head == nullptr) return head;

  // construct dummy head
  auto *dummy_head = new ListNode;
  dummy_head->next = head;
  ListNode *slow = dummy_head;
  ListNode *fast = dummy_head;

  // Spacing slow and fast with n nodes
  for (int i = 0; i < n; i++) {
    if (fast == nullptr) return nullptr;
    fast = fast->next;
  }

  // move fast point to the end, slow with the same speed with fast
  while (fast->next != nullptr) {
    slow = slow->next;
    fast = fast->next;
  }

  // delete the node
  ListNode *tmp = slow->next;
  slow->next = slow->next->next;
  delete tmp;
  return dummy_head->next;
}
```
