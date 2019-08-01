# RecyclerView 동적으로 Column 설정하기

안드로이드에서 리사이클러뷰를 사용하다 보면 각각의 row에서 다른 column을 설정해야할 상황이 생긴다. 대표적으로 구글 포토에서 한 줄에 3개의 이미지가 있기도 하고 4개의 이미지가 있기도 하다.

이러한 일들은 GridLayoutManager에서 SpanSize를 설정하는 것에 따라 결정된다. 아래와 같이 spanCount를 12로 정해주면 12개의 컬럼이 생기고 해당 컬럼에 어떠한 비율로 아이템을 설정할지는 SpanSizeLookup 클래스의 getSpanSize 메서드에서 정해줄 수 있다. getSpanSize에서 return 하는 값에 따라 row에서 아이템이 차지할 수 있는 비율이 결정된다. spanCount가 12이기 때문에 4를 return 하면 3개의 컬럼이 되는 것이고 3을 return 하면 4개의 컬럼이 되는 것이다.
```kotlin
    val gm = GridLayoutManager(this, 12)
    rv_test.adapter = testAdapter
    rv_test.layoutManager = gm
    gm.spanSizeLookup = object : GridLayoutManager.SpanSizeLookup() {
        override fun getSpanSize(position: Int): Int {
            return if (list[position].type == TestAdapter.TYPE_HIGH) 4 else 3
        }
    }
```
즉 return 값에 따라 아래와 같은 뷰를 생성할 수 있는 것이다.

![](Screen%20Shot%202019-08-01%20at%209.29.32%20PM.png)
