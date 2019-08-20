```java
    class Solution {
        public int canCompleteCircuit(int[] gas, int[] cost) {
            for(int i = 0; i < gas.length; i++){
                if(gas[i] >= cost[i]){
                    int travelCost = gas[i];
                    int k = i;
                    for(int j = 0; j < gas.length; j++){
                        travelCost -= cost[k];
                        if(k == gas.length - 1){
                            k = 0;
                        } else {
                            k++;  
                        }
                        if(travelCost < 0 || k == i){
                            break;
                        } else {
                            travelCost += gas[k];
                        }
                    }
                    if(travelCost >= 0){
                        return i;
                    }
                } 
            }   
            return -1;
        }
    }
```
