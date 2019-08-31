Null - 프로그램에서 아무 값도 대입하지 않은 상태.

코틀린에서의 Null 안전성이란, Null에 다양한 처리를 도와줌으로써 Null에 의한 NPE가 발생하지 않는 프로그램을 작성할 수 있게 해준다는 개념.

기본적으로 코틀린의 프로퍼티는 Null 허용,  Null 불허로 구분된다. Null 허용일 경우 타입의 뒤에 ?를 붙인다.

### Null 확인 연산자 ?.

Null이 아닐 때만 접근하도록 코드에서 작업해줄 수 있다.
```kotlin
    fun main(args: Array<String>){
    	val data: String? = "Son"
    	
    	var length: Int? = data?.length
    	println(length)
    
    	data = null
    	length = data?.length
    	println(length)
    
    	val user: User? = User()
    	println(user?.address?.city) // user가 null 아니고 address가 null 아니면.
    }
    
    // 3
    // null
    // seoul
    
    // Null이 아닌 상황에서 특정 구문을 수행하고 싶을 때, let {}
    val array = arrayOf("hello", null, "son")
    
    array.forEach {
    	it?.let {
    		println("$it .. ${it.length}")
    	}
    }
    
    // hello .. 5
    // son .. 3
```
### 엘비스 연산자 ?:

Null 허용 데이터를 처리할 때 Null 처리를 명시할 수 있다. ?. 연산자는 Null이면 null을 반환한다.
```kotlin
    fun main(args: Array<String>){
    	val data: String? = "Son"
    	
    	var length: Int = if(data != null){
    		data.length
    	} else {
    		-1
    	}
    
    	data = null
    
    	length = data?.length ?: -1
    
    	println(length)
    
    	data ?: println("data is null")
    }
    
    // -1
    // data is null
```
### 예외 발생 연산자 !!

!! 연산자는 Null이면 예외를 발생시킨다. Null일 때 ?.나 ?: 연산자를 이용하여 NPE가 발생하지 않게 작성할 수도 있지만, NPE를 발생시켜야 할 때 !!를 사용한다.
```kotlin
    fun main(args: Array<String>){
    	val data: String? = "Son"
    	
    	data!!.length
    
    	data = null
    
    	data!!length // data가 null이면 NPE
    }
```
### 안전한 캐스팅 as?

as 연산자를 사용하여 명시적 캐스팅을 할 때 캐스팅이 불가능한 경우는 ClassCastException이 발생한다. 이 상황에서 as?를 사용하여 에러 없이 null을 반환하게 할 수 있다.
```kotlin
    fun main(args: Array<String>){
    	val data: String? = "Son"
    	
    	val intData: Int? = data as? Int
    
    	println(intData)
    }
    
    // null
```
