```java
    /**
     * Definition for singly-linked list.
     * public class ListNode {
     *     int val;
     *     ListNode next;
     *     ListNode(int x) { val = x; }
     * }
     */
    
    class Solution {
        public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
            ListNode answer = new ListNode(0);
            ListNode answerNext = answer;
    
            int valueFlag = 0;
            ListNode l1Next = l1;
            ListNode l2Next = l2;
            while (l1Next != null || l2Next != null){
                int addedVal = 0;
                if (l1Next != null && l2Next != null){
                    addedVal = l1Next.val + l2Next.val + valueFlag;
                } else if (l1Next != null && l2Next == null){
                    addedVal = l1Next.val + valueFlag;    
                } else if (l1Next == null && l2Next != null){
                    addedVal = l2Next.val + valueFlag;    
                }
                int quot = addedVal / 10;
                int remainder = addedVal % 10;
                
                answerNext.next = new ListNode(remainder);
                answerNext = answerNext.next;
                if(quot >= 1) {
                    valueFlag = 1;       
                } else {
                    valueFlag = 0;
                }
                if(l1Next != null){
                    l1Next = l1Next.next;        
                }
                if(l2Next != null){
                    l2Next = l2Next.next;       
                }        
            }
            if(valueFlag > 0){
                answerNext.next = new ListNode(valueFlag);
            }
            return answer.next;
        }
    }
```
