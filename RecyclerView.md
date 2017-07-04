# RecyclerView手順
---

## 概要
RecyclerViewとはカスタマイズ性の高いListViewである。  
> RecyclerViewはListViewの代替品では無いのであしからず

## RecyclerView全体の流れ
1. RecyclerViewの参照取得 -> `findViewById(R.id.RECYCLER_VIEW_ID)`
1. RecyclerViewのLayoutManagerを通してLayoutの指定
> 1. 必要があればDivider（リストの区切り線）の設定
1. RecyclerViewのadapterにRecyclerView.Adapterを継承した自作クラスをセット

## RecyclerViewに必要なもの
### Class
- RecyclerViewを呼び出すためのアクティビティ（今回はMainActivity）
- RecyclerView.Adapterを継承したクラス
> - Listの中身の情報を保存するRecyclerView.ViewHolderを継承したViewHolderクラス
> 場合によってはRecyclerView.Adapterを継承したクラス内にprivateクラスで作成しても良い

### xml
- Listの中身のデザインを規定したxml
- RecyclerViewのデザインを規定したxml（今回はactivity_main.xml）

## コード
__activity_main.xml__
```xml
// 省略

<android.support.v7.widget.RecyclerView
    android:id="@+id/recyclerView"
    android:layout_width="368dp"
    android:layout_height="match_parent"
    android:layout_gravity="center_vertical"
    android:layout_marginBottom="8dp"
    android:layout_marginLeft="8dp"
    android:layout_marginRight="8dp"
    android:layout_marginTop="8dp"
    app:layout_constraintBottom_toBottomOf="parent"
    app:layout_constraintHorizontal_bias="0.0"
    app:layout_constraintLeft_toLeftOf="parent"
    app:layout_constraintRight_toRightOf="parent"
    app:layout_constraintTop_toTopOf="parent">
</android.support.v7.widget.RecyclerView>

// 省略
```

__MainActivity.kt__
```Kotlin
// 省略

val items = listOf("hoge","foo","bar","piyo")

val recyclerView = findViewById(R.id.recyclerView) as RecyclerView
// RecyclerViewのサイズを維持し続ける設定らしい
// RecyclerViewのサイズが固定で決まっているならtrueにするのが良いらしい
recyclerView.setHasFixedSize(true)
// RecyclerView内の表示形式にLinearLayoutを指定
recyclerView.layoutManager = LinearLayoutManager(this)
// 区切り線の設定
val divider = DividerItemDecoration(recyclerView.context, LinearLayoutManager(this).orientation)
// RecyclerViewに区切り線を追加
recyclerView.addItemDecoration(divider)
// RecylerViewAdapterは自作クラスです。引数は適宜変更してください。
recyclerView.adapter = RecyclerViewAdapter(items)

// 省略
```

__RecyclerViewAdapter.kt__
```Kotlin
class RecyclerViewAdapter(val items: List<String>): RecyclerView.Adapter<RecyclerView.ViewHolder>() {
    override fun getItemCount(): Int { // RecylerViewの子Viewの数をカウントする
      return items.size
    }

    override fun onBindViewHolder(holder: RecyclerView.ViewHolder?, position: Int) { // 生成したViewHolderに処理を行う部分
      // holderは単一のRecyclerViewの子を示している
      (holder as ViewHolder).itemText.text = items[position].title
      // クリックイベントの設定などもここで行う。
    }

    override fun onCreateViewHolder(parent: ViewGroup?, viewType: Int): RecyclerView.ViewHolder { // ViewHolderの生成部分
      // RecyclerViewに表示する子のViewのレイアウトxmlからViewを生成
      // ここではlist_itemという名称
      val view: View = LayoutInflater.from(parent?.context).inflate(R.layout.list_item, parent, false)
      return ViewHolder(view)
    }
}
```

__ViewHolder.kt__
```Kotlin
class ViewHolder(itemView: View?) : RecyclerView.ViewHolder(itemView) {
  // 今回のViewHolderではitemのEditTextを参照しているだけです。
  val itemText = itemView?.findViewById<View>(R.id.listItemText) as TextView
}
```

__list_item.xml__
```xml
<!-- 省略 -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:orientation="vertical">

  <TextView
    android:id="@+id/listItemText"
    android:layout_width="match_parent"
    android:layout_height="50dp" />
</LinearLayout>

```
こんな感じで行けたはず