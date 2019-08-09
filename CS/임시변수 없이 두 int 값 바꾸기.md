# 임시변수 없이 두 int 값 바꾸기

1. Calculation
    ```java
    int a = 1;
    int b = 2;
    
    a = a + b;
    b = a - b;
    a = a - b;
    ```
2. XOR - 비트연산자
    ```java
    int a = 1;
    int b = 2;
    
    a = a ^ b; // 0001 | 0010 = 0011
    b = a ^ b; // 0011 | 0010 = 0001
    a = a ^ b; // 0011 | 0001 = 0010
    ``` 
    
반복횟수, 메모리 제약 등 고려할게 있기 때문에 상황에 따라 단위테스트 해보고 알고리즘에 적용하면 좋다. 비트연산자 활용은 새로 배워간다.

참고

[http://oskardevelopers.blogspot.com/2016/05/java-int.html](http://oskardevelopers.blogspot.com/2016/05/java-int.html)
