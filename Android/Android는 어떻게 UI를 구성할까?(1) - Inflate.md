# Android는 어떻게 UI를 구성할까?(1) - Inflate

Created: Jul 13, 2019 7:59 PM

여태까지 안드로이드 UI를 구성하는데 크게 고민하지 않고 XML을 그리고 setContentView함수를 통해 불러오기만 했었다. 안드로이드 시스템은 어떻게 XML을 실제로 그려낼 수 있을까? 지금부터 파헤쳐본다.

## Inflate

안드로이드 프로그래밍을 하다보면 **Inflate**에 대해 많이 들어봤을 것이다.

Inflate는 사전적인 의미로 '부풀리다'라는 뜻이다. 안드로이드에서는 Inflate를 사용하면 XML에 작성된 View의 실체를 인스턴스화 하게 된다. XML에 정의된 Root Layout과 Child View들의 속성을 읽어서 실제로 View를 만들게 된다. 성능 상의 문제 때문에 XML파일은 빌드 과정에서 미리 컴파일 되어있다. 이렇게 미리 컴파일된 파일들을 [R.XXX](http://r.XXX) 형태의 리소스 ID를 찾아서 메모리에 로드한 후에 이용을 시작하는 것이다.

---

그렇다면 이러한 Inflate는 어떻게 실행될 수 있을까?

### setContentView()

setContentView()는 Activity 전체에 XML이나 View를 채우게 된다.

1. void setContentView(int layoutResID)
2. void setContentView(View view)
3. void setContentView(View view, ViewGroup.LayoutParams params)

위의 세 가지 메서드를 이용하면 R.layout.XXX나 View 객체를 통해 Activity를 채울 수 있게 된다.

이외에 addContentView()라는 메서드가 있는데 

```kotlin
    addContentView(linearLayout, ViewGroup.LayoutParams(MATCH_PARENT, WRAP_CONTENT))
```

이와 같이 기존에 생성된 View에 레이아웃을 겹칠 수 있는 기능을 한다.

### getSystemService(Context.LAYOUT_INFLATER_SERVICE), LayoutInflater.from

```kotlin
    val linearLayout = LinearLayout(this)
    linearLayout.orientation = LinearLayout.VERTICAL
    linearLayout.setBackgroundColor(Color.RED)
    
    val tv = TextView(this)
    tv.text = "하요"
    tv.textSize = 16.0f
    
    linearLayout.addView(tv)
    
    val view = LayoutInflater.from(this).inflate(R.layout.activity_home, linearLayout, false)
    
    val inflater : LayoutInflater = getSystemService(Context.LAYOUT_INFLATER_SERVICE) as LayoutInflater
    val view2 = inflater.inflate(R.layout.activity_home, linearLayout, true)
```

위의 두 메서드는 원하는 XML 파일을 집어 넣고자 하는 Layout에 맞춰서 inflate하는 내용이다.

linearLayout은 XML 파일을 집어 넣고자 하는 Root View이며 해당 View에 맞춰서 inflate된다. 마지막 boolean 값은 attachToRoot로 true면 linearLayout에 View가 자식으로 추가되고 false면 Root View는 그저 생성될 View의 LayoutParams를 생성하는데만 쓰이게 된다.

```kotlin
    val view = LayoutInflater.from(this).inflate(R.layout.activity_home, linearLayout, false)
```

해당 코드는 특히 RecyclerView를 사용할 때 onCreateViewHolder에서 자주 볼 수 있는 형태의 패턴이다.

---

사실 Inflate하는 과정은 Activity에서 View 객체가 어떻게 배치되는지에 대한 내용이었다. 그렇다면 개별적인 View 객체들은 어떻게 화면에 **그려지고 배치되는** 것일까?
