```java
    class Solution {
        public int[] twoSum(int[] nums, int target) {
            int[] answer = new int[2];
            answer[0] = -1;
            answer[1] = -1;
            for(int i = 0; i < nums.length; i++){
                for(int j = i + 1; j < nums.length; j++){
                    if(nums[i] + nums[j] == target){
                        answer[0] = i;
                        answer[1] = j;
                        break;
                    }
                }
                if(answer[0] != -1){
                    break;
                }
            }
            return answer;
        }
    }

```
