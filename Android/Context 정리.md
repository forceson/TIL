# Context 정리

Created: Jul 19, 2019 3:34 PM

Android를 하다보면 Context는 무조건 마주치게 된다. 그것도 자주. Context는 Activity가 되기도하고 Application이 되기도 했었다. 대략적으로 Context를 어떤 객체에 전달할때는 해당 Activity나 Application에서 그 객체를 사용중이라 전달해준다라는 느낌으로 사용하기만 했었다. 여태껏 깊게 탐구해보지 않았던 Context에 대해서 알아보자.

![](Untitled-d6e59ac0-e980-4321-bab7-cdad7f7ec5b5.png)

일단 Context는 추상클래스이다. 그리고 Context를 직접적으로 상속받는 서브클래스는 ContextWrapper와 MockContext, 간접적으로 상속받는 클래스는 꽤나 많은데 눈에 띄는 클래스는 Activity와 Application이다. Service도 꽤나 많다.

공식문서의 설명에는 다음과 같이 나와 있다.

> Interface to global information about an application environment. This is an abstract class whose implementation is provided by the Android system. It allows access to application-specific resources and classes, as well as up-calls for application-level operations such as launching activities, broadcasting and receiving intents, etc.

> 어플리케이션 환경에 대한 전역정보를 접근한다. 안드로이드 시스템에 의해 구현이 제공되는 추상클래스이다. 어플리케이션에 특화된 resource와 class 뿐만 아니라 Activity의 실행, Broadcasting 및 Intent 수신 등의 어플리케이션 레벨에 대한 호출도 제공한다.

Context는 추상클래스로서 getPackageName(), getResource(), startActivity(), bindService()과 같이 어플리케이션 레벨에서의 시스템 정보에 접근할 수 있는 메서드를 제공한다.

## Context가 필요한 이유

어떤 기능을 하는지는 알았는데 왜 Context가 필요할까? 요약하면 다른 플랫폼에서는 직접적으로 시스템에 접근하는 방법이 있는데 안드로이드는 어플리케이션과 프로세스가 서로 독립적인 존재이므로 어플리케이션을 따로 관리해줄 녀석이 필요하다고 한다. ActivityManagerService가 그 녀석인데 각 어플리케이션들을 key-value로 묶어서 관리 중이다. 그래서 Context는 자신이 어떤 어플리케이션인지 나타내고 있는 ID의 역할을 함과 동시에 ActivityManagerService에 접근할 수 있도록 한다고 한다. 결국 각 어플리케이션마다 Context를 통해 ActivityMangerService를 거쳐 시스템에 접근하는 구조로 안드로이드가 되어있기 때문에 이러한 현상이 벌어진 것 같다.

해당 블로그에서 이에 대한 고찰을 자세히 논해놓아서 저장해둬야겠다. 잊어버릴것 같을때마다 와서 봐야지.

[https://arabiannight.tistory.com/entry/272](https://arabiannight.tistory.com/entry/272)

## Context 종류

그런데 이 Context는 어플리케이션마다 ID 역할을 하며 존재하는데 어플리케이션 컴포넌트마다 또 따로 생성된 Context가 존재한다.

### Application

Applicaiton Context는 어플리케이션과 관련이 있고 싱글톤이므로 어플리케이션이 살아있는 동안 변경되지 않는다. Activity나 Service에서 getApplicationContext()로 해당 Context를 얻어올 수 있다. 해당 Context는 어플리케이션 생명주기와 관련이 있기 때문에 현재 컴포넌트의 Context와 분리된 Context가 필요하거나, Activity Scope를 벗어난 경우에 사용한다.

**어떤 싱글톤 객체를 객체를 만들고 해당 객체에 Context가 필요한 경우 Application Context를 이용한다. Application Context에 Activity를 참조하게 되면 Activity가 Destory되더라도 참조가 유지되므로 Memory Leak가 발생하니 주의한다.**

다른 Context보다 가장 오래 보존될만한 Context가 필요할 때만 사용하도록 한다.

Thread나 Handler 등의 백그라운드 작업시에도 사용하는 것이 좋은 습관이다.

### Activity / Service

Activity 관련 Context로 Activity가 Destroy되면 함께 객체가 해제된다. 새로운 Activity를 생성할 때 사용될 수 있으며 새로 생성된 Activity는 이전 Activity과 연관된 채 Stack에 보관된다. 해당 작업에 Applicaition Context도 사용가능하나 FLAG_ACTIVITY_NEW_TASK를 반드시 설정해주어야한다. 

Service에서도 Context가 있는데 해당 Context는 Activity와 다르다. 기능은 아래 표를 참고한다.

BroadcastReceiver는 자기자신이 Context는 아니다. onRecevie() 호출 시 Context를 가져올 수 있다고 한다. 그리고 리시버가 브로드캐스트를 처리할 때마다 새로운 Context가 생성된다고 한다.

## Context 기능

![https://t1.daumcdn.net/cfile/tistory/262F4338525109831F](https://t1.daumcdn.net/cfile/tistory/262F4338525109831F)

출처 : [http://www.kmshack.kr/2013/10/android-context-what-context/#comment-1195](http://www.kmshack.kr/2013/10/android-context-what-context/#comment-1195)

## Context 얻기

### View.getContext()

현재 뷰가 가지고 있는 Context를 반환하는데, 일반적으로는 Activity에서 뷰를 띄우므로 Activity의 Context가 된다.

### Activity.getApplicationContext()

어플리케이션 전체의 Context를 반환한다. 현재 Activity 뿐만 아니라 어플리케이션 수명주기와 관련된 Context가 필요한 경우 사용.

### ContextWrapper.getBaseContext()

어떤 Context에서 다른 Context를 참조해야하는 경우 ContextWrapper 객체를 사용하는데, 그 ContextWrapper에 있는 Context를 참조하는 경우 사용.

BaseContext는 뭔소리인지 잘 모르겠어서 좀 더 찾아보니 ContextWrapper를 가지고 있을 때만 관계가 있다고 한다. ContextWrapper는 원래의 Context를 변화시키지 않으면서 행동에 변화를 줄 수 있게 해준다고 한다. 예를 들면 mActivity라는 객체가 있고 해당 객체와 다른 theme을 가진 View를 만들고 싶을 때 다음과 같이 할 수 있다고 한다.
```java
    ContextWrapper customTheme = new ContextWrapper(myActivity) {
      @Override
      public Resources.Theme getTheme() { 
        return someTheme;
      }
    }
    View myView = new MyView(customTheme);
```
ContextWrapper는 Context가 가진 대부분의 메서드를 오버라이드 할 수 있기 때문에 강력하다고 한다. 특정한 상황에 Context를 커스터마이즈해서 활용할 수 있을 것 같다.

## 요약

Context는 안드로이드 시스템 레벨에서 제공하는 기능들을 제공하는 클래스이며 안드로이드 특유의 구조 때문에 각 어플리케이션에서 시스템에 접근하는 어플리케이션 마다의 ID 역할을 한다. 어플리케이션 마다 하나의 Context가 있고 어플리케이션 컴포넌트 마다 따로 Context가 존재한다. 주의할 점은 어플리케이션 Context는 싱글톤이라서 앱 활성화 기간 중 오랫동안 살아있을 클래스에서 참조하는데 쓰는것이고 컴포넌트들의 Context는 오랫동안 살아있는 싱글톤 객체에서 참조할 경우 Memory Leak이 발생할 수 있으므로 각 컴포넌트 레벨에서만 컴포넌트에서 생성된 Context를 이용하도록 한다. 그리고 각 컴포넌트 Context 마다 할 수 있는 기능이 다르다는 것을 참고한다. 마지막으로 Context를 얻는 세가지 방법까지 알고 있으면서 적절히 용도에 따라 사용하면 된다.

## 레퍼런스

[https://developer.android.com/reference/android/content/Context](https://developer.android.com/reference/android/content/Context)

[https://www.charlezz.com/?p=1080](https://www.charlezz.com/?p=1080)

[https://arabiannight.tistory.com/entry/272](https://arabiannight.tistory.com/entry/272)

[https://gogorchg.tistory.com/entry/Android-getBaseContext-getApplicationContext-설명](https://gogorchg.tistory.com/entry/Android-getBaseContext-getApplicationContext-%EC%84%A4%EB%AA%85)

[https://medium.com/@ali.muzaffar/which-context-should-i-use-in-android-e3133d00772c](https://medium.com/@ali.muzaffar/which-context-should-i-use-in-android-e3133d00772c)
