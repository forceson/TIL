# [디자인패턴] Singleton

## 싱글톤 패턴

애플리케이션이 시작될 때 어떤 클래스가 최초 한번만 메모리를 할당하고(static) 그 메모리에 인스턴스를 만들어 사용하는 디자인 패턴.

생성자가 여러 차례 호출되더라도 실제로 생성되는 객체는 하나고 최초 생성 이후에 호출된 생성자는 최초에 생성한 객체를 반환한다.

즉, 싱글톤 패턴은 단 하나의 인스턴스를 생성해 사용하는 디자인 패턴이다.

## 사용이유

고정된 메모리 영역을 얻으면서 단 하나의 인스턴스를 생성해 사용하기 때문에 메모리 낭비를 방지할 수 있다.

싱글톤으로 만들어진 인스턴스는 전역 인스턴스이기 때문에 다른 클래스의 인스턴스들이 데이터를 공유하기 쉽다.

공통된 객체를 여러 개 생성하여 사용해야하는 상황에서 많이 사용한다. (쓰레드풀, 캐시, 사용자 설정, 로그 기록 객체 등)

## 문제점

싱글톤 인스턴스가 너무 많은 일을 하거나 많은 데이터를 공유시킬 경우 다른 클래스의 인스턴스 간에 결합도가 높아져 OCP를 위배하게 된다. 따라서 수정이 어려워지고 테스트하기 어려워진다.

멀티쓰레드 환경에서 동기화 처리를 하지 않으면 인스턴스가 두 개 생성된다든지 하는 문제가 생길 수 있다.

## 사용방법

### Java

가장 일반적인 방법, thread safe 하지는 않다.

    public class Singleton {
    	private static Singleton singleton;
    
    	private Singleton() {}
    
    	public static Singleton getInstance(){
    		if(singleton == null){
    			singleton = new Singleton();
    		}
    		return singleton;
    	}
    }

holder에 의한 초기화, thread safe 한 방법

    public class Singleton {
    	private Singleton() {}
    	
    	private static class LazyHolder {
    		public static final Singleton INSTANCE = new Singleton();
    	}
    	
    	public static Singleton getInstance(){
    		return LazyHolder.INSTANCE;
    	}
    }

JVM 클래스 초기화 과정에서 보장되는 원자적 특성을 이용하여 싱글톤의 초기화 문제에 대한 책임을 JVM에 떠넘긴다. holder 안에 선언된 instance가 static이기 때문에 클래스 로딩 시점에 한번만 호출될 것이며 final을 사용해 다시 값이 할당되지도 않도록 만든 방법이다. 가장 많이 사용하고 일반적인 방법이라고 한다.

### Kotlin

**object**

코틀린에서는 object 클래스를 이용하여 클래스를 정의함과 동시에 인스턴스화 할 수 있다.

    object CarFactory {
    	val cars = mutableListOf<Car>()
    	
    	fun makeCar(horsePowers: Int): Car {
    		val car = Car(horsePowers)
    		cars.add(car)
    		return car
    	}
    }
    
    val car = CarFactory.makeCar(150)
    println(CarFactory.cars.size)

CarFactory 객체는 싱글톤으로 구현이 되어있기 때문에 여러 번 호출해도 CarFactory 객체는 한 번만 생성된다. CarFactory 자체와 내부에 선언된 cars는 thread safe하게 한 번만 생성되어 사용할 수 있게 된다. 그러나 내부적으로 공유자원을 사용하는 경우 thead safe함이 보장되지 않으므로 동기화 코드를 작성해야한다. 내부적으로 어떻게 자바로 변환되는지 확인하고 사용하면 보다 효율적으로 사용할 수 있을 것으로 본다.

## 레퍼런스

[https://jeong-pro.tistory.com/86](https://jeong-pro.tistory.com/86)

[https://codechacha.com/ko/kotlin-object-vs-class/](https://codechacha.com/ko/kotlin-object-vs-class/)

두 번째 코틀린 object 관련 포스팅은 재참조할것.
