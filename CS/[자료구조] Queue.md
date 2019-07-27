# [자료구조] Queue
큐는 데이터의 삽입이 한쪽 끝(뒤, rear)에서 이루어지고 삭제는 다른 쪽 끝(앞, front)에서 이루어지는 정렬된 리스트이다. 가장 처음 삽입된 항목이 맨 먼저 삭제된다. 그러므로 선입선출(FIFO)이나 후입후출(LILO)리스트라고 불린다. 항목이 큐에 삽입될 때, EnQueue, 항목이 큐로부터 제거될 때, DeQueue라고 한다. 빈 큐로부터 디큐하려고 하는 것을 Underflow, 꽉찬 큐에 인큐하려고 하는 것을 Overflow라고 한다.

**적용사례**

- 시스템 예약 작업 도착 순서대로 처리하기
- 멀티프로그래밍
- 비동기적 데이터 전송(파일 입출력, 파이프, 소켓)
- 고객 센터에서 고객의 대기 시간
- 수퍼마켓에서 계산대 점원 수 결정하기

## 선형 큐
```java
    public class LinearArrayQueue {
    	private int front;
    	private int rear;
    	private int capacity;
    	private int[] arr;
    	
    	public LinearArrayQueue(int size){
    		capacity = size;
    		front = -1;
    		rear = -1;
    		arr = new int[size];
    	}
    	
    	public boolean isEmpty(){
    		return front == rear;
    	}
    
    	public boolean isFull(){
    		return rear == capacity - 1;
    	}
    
    	public void enqueue(int data){
    		if(isFull()){
    			return;
    		} else {
    			arr[++rear] = data;
    		}
    	}
    
    	public int dequeue(){
    		if(isEmpty()){
    			return -1;
    		} else {
    			return arr[++front];
    		}
    	}
    }
```
문제가 발생한다. 삽입과 삭제를 조금 반복하다 보면 앞쪽 공간이 낭비되기 시작한다.

## 간단한 원형 배열 구현

나머지 연산을 통해 원형 구조를 완성시킨다.
```java
    public class CircularArrayQueue {
    	private int front;
    	private int rear;
    	private int[] arr;
    	
    	public CircularArrayQueue(int size){
    		capacity = size;
    		front = 0;
    		rear = 0;
    		arr = new int[size];
    	}
    	
    	public boolean isEmpty(){
    		return front == rear;
    	}
    
    	public boolean isFull(){
    		return (rear + 1) % capacity == front;
    	}
    
    	public void enqueue(int data){
    		if(isFull()){
    			return;
    		} else {
    			rear = (++rear) % capacity;
    			arr[rear] = data;
    		}
    	}
    
    	public int dequeue(){
    		if(isEmpty()){
    			return -1;
    		} else {
    			front = (++front) % capcity;
    			return arr[front];
    		}
    	}
    }
```
## 동적 원형 배열 구현
```java
    public class CircularDynArrayQueue {
    	private int front;
    	private int rear;
    	private int[] arr;
    	
    	public CircularDynArrayQueue(int size){
    		capacity = size;
    		front = 0;
    		rear = 0;
    		arr = new int[size];
    	}
    	
    	public boolean isEmpty(){
    		return front == rear;
    	}
    
    	public boolean isFull(){
    		return (rear + 1) % capacity == front;
    	}
    
    	public void enqueue(int data){
    		if(isFull()){
    			resizeQueue();
    		} else {
    			rear = (++rear) % capacity;
    			arr[rear] = data;
    		}
    	}
    
    	public int dequeue(){
    		if(isEmpty()){
    			return -1;
    		} else {
    			front = (++front) % capcity;
    			return arr[front];
    		}
    	}
    
    	private resizeQueue(){
    		int initCapacity = capacity;
    		capacity *= 2;
    		int[] oldArr = arr;
    		arr = new int[capacity];
    		for(int i = 0; i < oldArr.length; i++){
    			arr[i] = oldArr[i];
    		}
    		if(rear < front){
    			arr[i + initCapacity] = arr[i];
    		}
    		rear += initCapacity;
    	}
    }
```
## 연결 리스트 구현
인큐는 리스트 맨 끝에 삽입, 디큐는 리스트 맨 앞에서 항목 삭제
```java
    public class LLQueue {
    	private Node frontNode;
    	private Node rearNode;
    	
    	public LLQueue(){
    		frontNode = null;
    		rearNode = null;
    	}
    	
    	public boolean isEmpty(){
    		return frontNode == null;
    	}
    
    	public void enqueue(int data){
    		Node newNode = new Node();
    		if(rearNode != null){
    			rearNode.setNext(newNode);
    		}
    		rearNode = newNode;
    		if(frontNode == null){
    			frontNode = rearNode;
    		}
    	}
    
    	public int dequeue(){
    		if(isEmpty()){
    			return -1;
    		} else {
    			int data = frontNode.getData();
    			frontNode = frontNode.getNext();
    			return data;
    		}
    	}
    }
```
## 배열 구현 vs 연결 리스트 구현

### 배열 구현

- 연산의 수행에는 일정한 시간이 걸린다.
- 가끔 비용이 큰 두 배 확장 연산을 수행한다.

### 연결 리스트 구현

- 부드럽게 커지고 작아진다.
- 모든 연산에 일정한 시간 O(1)이 걸린다.
- 모든 연산에 레퍼런스를 다루기 위한 부가적인 공간과 시간이 필요하다.
