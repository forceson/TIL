```java
    class Solution {
        public String longestPalindrome(String s) {
            String subString = s;
            boolean isSame = false;
            for(int i = s.length(); i > 0; i--){
                for(int j = 0; j < s.length() - (i - 1); j++){
                    subString = s.substring(j, j+i);
                    for(int k = 0; k < subString.length() / 2; k++){
                        if(subString.charAt(k) != subString.charAt(subString.length() - k - 1)){
                            isSame = false;
                            break;
                        }
                        isSame = true;
                    }
                    if(isSame){
                        return subString;
                    }
                }
            }
            if(s.length() != 0){
                return s.substring(0,1);
            } else {
                return "";    
            }
        }
    }
```
