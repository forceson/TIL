```java
import java.util.*;
    
    class Solution {
        public int uniqueMorseRepresentations(String[] words) {
            String[] morse = {".-","-...","-.-.","-..",".","..-.","--.","....","..",".---","-.-",".-..","--","-.","---",".--.","--.-",".-.","...","-","..-","...-",".--","-..-","-.--","--.."};
            HashSet<String> hashSet = new HashSet<>();
            
            for(int i = 0; i < words.length; i++){
                StringBuilder sb = new StringBuilder();
                for(int j = 0; j < words[i].length(); j++){
                    sb.append(morse[words[i].charAt(j) - 'a']);
                }
                hashSet.add(sb.toString());
            }
            return hashSet.size();
        }
    }
```

유일한 모스 부호들을 선별하여 그 개수가 몇개인지 리턴하는 코드를 작성하면 된다. HashSet을 이용하여 해결했다.
