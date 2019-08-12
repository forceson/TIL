# Range Sum of BST

leetcode BST 문제 풀이

주어진 BST root 노드로부터, L, R을 포함한 범위내의 노드 값의 합 구하기

Input: root = [10,5,15,3,7,null,18], L = 7, R = 15

Output: 32
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
        int ans;
        public int rangeSumBST(TreeNode root, int L, int R) {
            dfs(root, L, R);
            return ans;
        }
        
        public void dfs(TreeNode root, int L, int R){
            if(root == null){
                return;
            }
            if(root.val >= L && root.val <= R){
                ans += root.val;
            }
            dfs(root.left, L, R);
            dfs(root.right, L, R);
        }
    }
```
