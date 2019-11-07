# Nested RecyclerView 가로 스크롤 초기화 해결 
```kotlin
    private val positionList = SparseIntArray()
```
```kotlin
    override fun onViewRecycled(holder: RecyclerView.ViewHolder) {
    	if (holder is ItemViewHolder) {
    		val itemViewHolder: ItemViewHolder = holder
    	  val position: Int = itemViewHolder.adapterPosition
        val firstVisiblePosition: Int = itemViewHolder.lm.findFirstVisibleItemPosition()
        positionList.put(position, firstVisiblePosition)
      }
      super.onViewRecycled(holder)
    }
```
```kotlin
    // inside of onBindViewHolder
    fun adjustRecyclerViewItemPosition(position: Int) {
      val lastSeenPosition = positionList.get(position)
      if (lastSeenPosition >= 0) {
          lm.scrollToPositionWithOffset(lastSeenPosition, 0)
      }
    }
```
리사이클러뷰 사용할 때 내부에 가로 리사이클러뷰를 추가해야할 일이 있을 때가 있다. 그럴 때 뷰가 리사이클되면 스크롤이 초기화되는데 이를 막을 수 있는 방법이 상위 어댑터에 포지션을 저장해두고 bind될 때 재지정 해주는 것이다.
