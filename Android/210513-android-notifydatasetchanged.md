# 안드로이드 RecyclerView의 notifyDataSetChanged(), notifyItemInserted() 에 대한 고려

## 1. 개요
- RecyclerView의 Adapter가 그려주는 각 아이템들의 데이터에 변화(추가, 삭제, 수정 등)가 생겼을때, Adapter에 그려줘야하는 데이터에 변화가 생겼음을 알려줘야함.
- 이 경우, 다음과 같은 메소드들을 사용할 수 있음. [링크](!https://developer.android.com/reference/androidx/recyclerview/widget/RecyclerView.Adapter)
<img src="https://user-images.githubusercontent.com/26498433/118092091-99ea3a80-b406-11eb-9bb6-dec35aace2aa.png">
- 예를들어 아이템이 추가된 경우, 대표적으로는 notifyDataSetChanged()와 notifyItemInserted()를 고민할 수 있을 것이다.

## 2. notifyDataSetChanged() vs notifyItemInserted()
- 위의 메소드를 사용하면 모두 recyclerview에 정상적으로 아이템이 추가될 것이다.
- 하지만, 현재 Adapter가 그려주는 데이터의 수량과 특성을 고려하여야한다.
- 그려줘야하는 아이템이 많거나, 인덱스(position)을 명확히 할 수 있는 경우에는 notifyItemInserted()가 효율적이다.
- notifyDataSetChanged()의 경우 LayoutManager가 모든 아이템을 다시 rebind해야하므로, 오버헤드가 클 수 있기 때문이다.