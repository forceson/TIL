# [자료구조] Stack

스택은 삽입과 삭제가 한쪽 끝에서 이루어지는, 순서가 매겨진 리스트이다. 이 끝을 탑(top)이라고 부른다. 제일 마지막에 추가된 항목이 제일 먼저 삭제된다. 그러므로 후입선출(LIFO) 혹은 선입후출(FILO) 리스트라고 불린다.

push() - 스택에 항목 삽입

pop() - 항목을 스택으로부터 삭제

underflow - 빈 스택으로부터 항목을 팝하려는 것

overflow - 가득 찬 스택에 항목을 푸시하려는 것

**적용사례**

- 괄호 짝맞추기
- 함수 호출 구현 (재귀 포함)
- 웹 브라우저에서 방문한 페이지 기록 관리
- 텍스트 에디터 작업 취소 (Undo)
- HTML과 XML에서 태그(Tag) 짝맞추기
- 안드로이드 Activity의 Task 백스택
- 안드로이드 Fragment 백스택

## 간단한 배열에 기반한 구현
```java
    public class ArrayStack{
    	private int top;
    	private int capacity;
    	private int[] array;
    	public ArrayStack(){
    		capacity = 10;
    		array = new int[capacity];
    		top = -1;
    	}
    	public boolean isEmpty(){
    		return (top == -1);
    	}
    	public boolean isStackFull(){
    		return (top == capacity - 1);
    	}
    	public void push(int data){
    		if(isStackFull()){
    			System.out.println("Stack Overflow");
    		} else {
    			array[++top] = data;
    		}
    	}
    	public int pop(){
    		if(isEmpty()){
    			System.out.println("Stack is Empty");
    			return 0;
    		} else {
    			return array[top--];
    		}
    	}
    }
```
push(), pop() 시간복잡도 O(1)

## 동적 배열에 기반한 구현

두 배 확장 방법으로.
```java
    public class DynamicArrayStack{
    	private int top;
    	private int capacity;
    	private int[] array;
    	public DynamicArrayStack(){
    		capacity = 10;
    		array = new int[capacity];
    		top = -1;
    	}
    	public boolean isEmpty(){
    		return (top == -1);
    	}
    	public boolean isStackFull(){
    		return (top == capacity - 1);
    	}
    	public void push(int data){
    		if(isStackFull()){
    			doubleStack();
    		} else {
    			array[++top] = data;
    		}
    	}
    	public void doubleStack(){
    		int newArray[] = new int[capacity * 2];
    		System.arraycopy(array, 0, newArray, 0, capacity);
    		capacity = capacity * 2;
    		array = newArray;
    	}
    	public int pop(){
    		if(isEmpty()){
    			System.out.println("Stack is Empty");
    			return 0;
    		} else {
    			return array[top--];
    		}
    	}
    }
```
push() 시간복잡도 O(n) 확장 때문에. amortized O(1). pop() O(1)

## 연결 리스트 구현

가장 처음 노드 삽입, 삭제
```java
    public class LLStack{
    	private Node head;
    	public LLStack(){
    		head = new Node(null);
    	}
    	public boolean isEmpty(){
    		if(head == null) return true;
    		else return false;
    	}
    	public boolean isStackFull(){
    		return (top == capacity - 1);
    	}
    	public void push(int data){
    		if(head == null){
    			head = new Node(data);
    		} else if(head.getData() == null) {
    			head.setData(data);
    		} else {
    			Node node = new Node(data);
    			node.setNext(head);
    			head = node;
    		}
    	}
    	public int pop(){
    		if(head == null){
    			System.out.println("Stack is Empty");
    			return 0;
    		} else {
    			int data = head.getData();
    			head = head.getNext();
    			return data;
    		}
    	}
    }
```
push(), pop() 시간복잡도 O(1)

## 배열 구현 vs 연결 리스트 구현

### 배열 구현

- 연산의 수행에는 일정한 시간이 걸린다.
- 가끔 비용이 큰 두 배 확장 연산을 수행한다.

### 연결 리스트 구현

- 부드럽게 커지고 작아진다.
- 모든 연산에 일정한 시간 O(1)이 걸린다.
- 모든 연산에 레퍼런스를 다루기 위한 부가적인 공간과 시간이 필요하다.

성능이 극도로 중요한 경우라면 적당히 큰 크기의 배열을 먼저 메모리에 올려두고 동적 배열로 구현할 것 같다. 배열 구현에서의 push(), pop()에서 연산처리속도가 아무래도 더 빠르기 때문이다.
