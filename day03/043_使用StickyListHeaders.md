# 043_使用 StickyListHeaders
## 学习目标
- 使用 StickyListHeaders 实现粘性头列表效果


## 引言和回顾

## 课堂内容
### 1. 添加 StickyListHeaders 依赖
在 module 的 build.gradle 文件中添加依赖
```gradle
dependencies {

    // 分组显示数据的三方控件
    compile 'se.emilsjolander:stickylistheaders:2.7.0'
}
```

### 2. 在布局文件中使用 StickyListHeadersListView
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">

    <se.emilsjolander.stickylistheaders.StickyListHeadersListView
        android:id="@+id/shl"
        android:layout_weight="1.0"
        android:layout_width="0dp"
        android:layout_height="match_parent">
    </se.emilsjolander.stickylistheaders.StickyListHeadersListView>


</LinearLayout>
```

- 在 GoodsFragment 使用的布局文件 fragment_goods 中使用 StickyListHeadersListView

### 3. 给 StickyListHeadersListView 设置 Adapter
```java
public class GoodsFragment extends BaseFragment {
    @InjectView(R.id.shl)
    StickyListHeadersListView shl;

    private MyGroupAdapter groupAdapter;

    @Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.fragment_goods, null);
        ButterKnife.inject(this, view);

        groupAdapter = new MyGroupAdapter();
        shl.setAdapter(groupAdapter);
        return view;
    }
}
```

- 在 GoodsFragment 中找到 StickyListHeadersListView，然后设置一个 adapter 对象

### 4. 构建测试数据
```java
public class GoodsFragment extends BaseFragment {
    ....

    public class Data{
        String info;
        int headId;
        int headIndex;
    }

    public class Head{
        String info;
    }

    private ArrayList<Data> datas = new ArrayList<>();
    private ArrayList<Head> heads = new ArrayList<>();

    private void testData(){
        for(int hi = 0; hi < 10; hi++){
            Head head = new Head();
            head.info = "头" + hi;
            heads.add(head);
        }

        for(int hi = 0; hi < heads.size(); hi++){
            for(int di = 0; di < 10; di++){
                Data data = new Data();
                data.headId = hi;
                data.headIndex = hi;
                data.info = "普通条目第" + hi + "组，条目数" + di;
                datas.add(data);
            }
        }
    }
}
```

- 这里构建了两个集合，一个是标题头集合，一个是数据列表集合，数据列表中的每条数据都有一个所属的分类

### 5. 定义 Adapter
```java
public class MyGroupAdapter extends BaseAdapter implements StickyListHeadersAdapter {

    //////////////////////////////////头管理/////////////////////////////////////////////
    @Override
    public View getHeaderView(int position, View convertView, ViewGroup parent) {
        TextView head = new TextView(parent.getContext());
        head.setText(heads.get(datas.get(position).headIndex).info);
        head.setBackgroundColor(Color.GRAY);
        return head;
    }

    @Override
    public long getHeaderId(int position) {
        return datas.get(position).headId;
    }

    //////////////////////////////////普通条目管理/////////////////////////////////////////////
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        TextView tv = new TextView(MyApplication.getContext());
        tv.setText(datas.get(position).info);
        return tv;
    }

    @Override
    public int getCount() {
        return datas.size();
    }

    @Override
    public Object getItem(int position) {
        return datas.get(position);
    }

    @Override
    public long getItemId(int position) {
        return position;
    }
}
```

- MyGroupAdapter 继承 BaseAdapter，还要实现 StickyListHeadersAdapter 接口；
- `getHeaderId()`，返回每个 position 的条目所属的分类的 id，根据前后两个条目的所属分类 id 是否一样，来决定是否显示头view，getHeaderId 返回值决定了哪些位置显示头 view；
- `getHeaderView()` 返回每个 position 对应的 headView，每个 position 都有 headview，而且 headview 和普通的 item view合并在一起形成一个新的 item，我们之所以只能看到一个分类item，是根据 `getHeaderId()` 方法的返回值，决定哪些 item 的 headView 会 gone 掉。

