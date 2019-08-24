```java
    /**
     * Definition for a binary tree node.
     * public class TreeNode {
     *     int val;
     *     TreeNode left;
     *     TreeNode right;
     *     TreeNode(int x) { val = x; }
     * }
     */
    class Solution {
        public TreeNode mergeTrees(TreeNode t1, TreeNode t2) {
            if(t1 == null && t2 == null){
                return null;
            }
            if(t1 == null && t2 != null){
                return t2;
            } else if (t1 != null && t2 == null){
                return t1;
            } else {
                TreeNode newNode = new TreeNode(t1.val + t2.val);
                newNode.left = mergeTrees(t1.left, t2.left);
                newNode.right = mergeTrees(t1.right, t2.right);
                return newNode;
            }
        }
    }
```
