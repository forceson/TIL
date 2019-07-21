# Intent 정리

인텐트는 일종의 메시지 객체로 안드로이드의 네 가지 기본 컴포넌트인 Activity, Service, ContentProvider, BroadcastReceiver 사이에서 통신을 담당하는 역할을 한다. 작업을 요청할 수 있고 데이터를 주고 받을 수 있게 한다. 주로 startActivity를 통해 액티비티를 시작하거나, startService를 통해 서비스를 시작하거나, sendBroadcast()를 통해 다른 앱들에 브로드캐스트를 전달하는데 쓰인다.

## Intent의 유형

### 명시적 인텐트, Explicit Intent

시작할 구성 요소를 이름으로 지정한다 (완전히 정규화된 클래스의 이름). 명시적 인텐트는 일반적으로 본인의 앱 안에서 구성 요소를 시작할 때 사용한다. 시작하고자 하는 액티비티 또는 서비스의 클래스의 이름을 알고 있기 때문이다. 예를 들면, 새 액티비티를 시작하거나 백그라운드에서 파일을 다운로드 하기 위해 서비스를 시작하는 것 등이 해당된다.
```kotlin
    val intent = Intent(this, PostActivity::class.java)
    startActivity(intent)
```
### 암시적 인텐트, Implicit Intent

 특정 구성 요소의 이름을 대지는 않지만, 그 대신 수행할 일반적인 작업을 선언하여 또 다른 앱의 구성 요소가 이를 처리할 수 있도록 해준다. 예를 들어, 사용자에게 지도에 있는 한 위치를 표시해주고자 하는 경우, 암시적 인텐트를 사용하여 해당 기능을 갖춘 앱이 지정된 위치를 지도에 표시하도록 요청 할 수 있다.
 ```kotlin
    val location = Uri.parse("geo:37.422219,-122.08364")
    val intent = Intent(Intent.ACTION_VIEW, location)
    startActivity(intent)
```
## Intent의 구조

### ComponentName

시작할 구성 요소의 이름이다. 선택 항목이나, 이 항목이 인텐트를 명시적으로 만들어준다. ComponentName이 없으면 해당 인텐트는 암시적이 되며, 인텐트를 수신해야 하는 구성 요소는 다른 인텐트 정보를 기반으로 시스템이 결정한다. (ex. Action, Category)

### Action

수행할 일반적인 작업을 나타내는 문자열이다. 

인텐트를 통해 수행할 동작을 지정하거나 브로드캐스트 메시지일 경우 특정 상태를 의미한다. 인텐트의 나머지 부분이 어떻게 구조화될지는 대부분 Action에서 결정된다. 특히, Data 내부와 Extra 등이 해당된다. 인텐트 Action지정은 setAction() 또는 Intent 생성자를 이용한다. 안드로이드 기본 액션 외에 사용자가 액션을 만들어 사용할 수도 있다. ACTION_VIEW, ACTION_EDIT, ACTION_MAIN 등의 Action이 있다.

### Data

작업을 수행할 데이터 또는 해당 데이터의 MIME Type을 참조하는 URI이다. 제공된 데이터의 유형을 나타내는 것은 일반적으로 인텐트의 Action이다. 예를 들어 인텐트가 ACTION_EDIT인 경우, 데이터에 편집할 문서의 URI가 들어있어야 한다.

인텐트를 생성할 때 URI 이외에도 데이터의 Type을 정해주는 것이 중요하다. 데이터의 MIME Type을 지정해두면 Android 시스템이 인텐트를 최상의 구성 요소를 찾는데 도움이 된다. MIME Type은 URI에서 추론되는 경우도 있다.

데이터 URI만 설정 시 setData(), MIME 유형만 설정 시 setType(), 둘 다 동시에 설정 시, setDataAndType().

ex) ACTION_VIEW content://contacts/people/1 - identifier 1을 가진 person의 정보를 보여준다.

ACTION_DIAL content://contacts/people/1 -- 해당 person의 정보가 채워진 핸드폰의 다이얼을 보여준다.

### Category

수행되는 Action에 대한 추가적인 정보이다. 카테고리는 액션과 달리 하나의 인텐트가 여러 개의 카테고리를 가질 수 있다. 대부분의 인텐트에는 카테고리가 없어도 된다. 

CATEGORY_LAUNCHER는 이 액티비티가 작업의 최초 액티비티이며, 이것이 시스템의 어플리케이션 시작 관리자에 목록으로 게재되어있음을 의미한다.

### Extra

요청한 Action을 수행하기 위해 필요한 추가 정보를 담고 있는 key-value 쌍이다. Extra를 추가하려면 putExtra()를 사용한다. 추가 데이터를 가진 Bundle 객체를 생성할 수도 있고, 해당 Bundle을 Intent에 putExtras()로 삽입해도 된다. 본인의 앱이 수신할 인텐트에 대해, 커스텀 추가 키를 선언할 수도 있다.

### Flag

Intent 클래스에서 정의된 플래그로, 인텐트에 대한 메타데이터와 같은 역할을 한다. 이런 플래그는 Android 시스템에 액티비티를 시작할 방법에 대한 지침을 줄 수도 있고(예를 들면 Task 소속), 액티비티를 시작한 다음에 어떻게 처리해야 하는지도 알려줄 수 있다. setFlags()를 통해 설정가능.

## 앱 선택기 강제 적용

인텐트에 응답할 수 있는 앱이 여러 개이고 사용자가 매번 다른 앱을 사용하기를 원할 수도 있는 경우라면, 선택기 대화상자를 명시적으로 표시해야 한다. 선택기 대화상자는 사용자가 작업에 사용할 앱을 매번 선택하도록 물어본다. 공유하는 상황에서 주로 쓰인다.

선택기를 표시하려면, createChooser()를 사용하여 Intent를 만들고 startActivity()에 전달한다.
```kotlin
    val sendIntent = Intent(Intent.ACTION_SEND)
    // ...
    
    // Always use string resources for UI text.
    // This says something like "Share this photo with"
    val title = resources.getString(R.string.chooser_title)
    // Create intent to show the chooser dialog
    val chooser = Intent.createChooser(sendIntent, title)
    
    // Verify the original intent will resolve to at least one activity
    if (sendIntent.resolveActivity(packageManager) != null) {
        startActivity(chooser)
    }
```
## Intent Filter

본인의 앱이 수신할 수 있는 암시적 인텐트가 어느 것인지 알리려면, 각 앱 구성 요소에 대한 하나 이상의 인텐트 필터를 <intent-filter> 요소로 매니페스트 파일에 선언해야한다. <intent-filter> 내부에서는 다음과 같은 세 가지 요소 중 하나 이상을 사용하여 허용할 인텐트 유형을 지정할 수 있다.

### <action>

허용된 인텐트 Action을 선언한다.

### <data>

허용된 데이터 type을 선언한다. 이 때 데이터 URI(scheme, host, port, path 등)와 MIME 유형의 여러 가지 측면을 나타내는 하나 이상의 속성을 사용하는 방법을 사용한다.

### <category>

허용된 인텐트 카테고리를 선언한다. 암시적 인텐트를 사용하려면 CATEGORY_DEFAULT 카테고리를 반드시 포함해야한다.
```xml
    <activity android:name="ShareActivity">
        <intent-filter>
            <action android:name="android.intent.action.SEND"/>
            <category android:name="android.intent.category.DEFAULT"/>
            <data android:mimeType="text/plain"/>
        </intent-filter>
    </activity>
```
## PendingIntent

PendingIntent 객체는 Intent 객체를 감싸는 래퍼 클래스이다. PendingIntent의 기본 목적은 외부 어플리케이션에 권한을 허가하여 안에 들어 있는 Intent를 마치 본인 앱의 자체 프로세스에서 실행하는 것처럼 사용하게 하는 것이다.

컴포넌트에서 다른 컴포넌트에게 작업을 요청하는 인텐트를 사전에 생성한다는 것과 특정 시점에 자신이 아닌 다른 컴포넌트들이 PendingIntent를 사용하여 다른 컴포넌트에게 작업을 요청시키는 데 사용된다는 것이 기존 Intent와의 차이점이다.

> A한테 B인텐트를 C시점에 실행하라고 해. 지금은 실행하지 말고

getActivity(Context, int, Intent, int), getActivities(Context, int, Intent[], int), getBroadcast(Context, int, Intent, int), and getService(Context, int, Intent, int) 가 반환하는 객체를 다른 어플리케이션에 전달할 수 있으므로 개발자가 명시하는 작업을 수행할 수 있다.

### 대표적인 사례

- 사용자가 Notifiaction으로 특정한 동작을 할 때, 실행되는 인텐트를 생성할 수 있다. (NotificationManager가 인텐트 실행)
- 사용자가 AppWidger으로 특정한 동작을 할 때, 실행되는 인텐트를 생성할 수 있다.
- 미래의 특정 시점에 실행되는 인텐트를 선언할 수 있다. (안드로이드의 AlarmManager가 인텐트 실행)

### PendingIntent 생성 방법

- Activity를 시작하는 Intent의 경우 PendingIntent.getActivity()
- Service를 시작하는 Intent의 경우 PendingIntent.getService()
- BroadcastReceiver를 시작하는 Intent의 경우 PendingIntent.getBroadcast()

Context는 현재 앱의 Context이다.
```kotlin
    val intent = Intent(Intent.ACTION_VIEW, Uri.parse("https://www.google.com/"))
    val pendingIntent = PendingIntent.getActivity(this, 0, intent, 0)
```
## 레퍼런스

- [https://developer.android.com/guide/components/intents-filters#PendingIntent](https://developer.android.com/guide/components/intents-filters#PendingIntent)
- [https://hyunssssss.tistory.com/46](https://hyunssssss.tistory.com/46)
- [https://techlog.gurucat.net/80](https://techlog.gurucat.net/80)
