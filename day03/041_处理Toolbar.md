# 041_处理Toolbar
## 学习目标
- 按需求设置 SellerDetailActivity 的 Toolbar


## 引言和回顾
按照上面分析的内容:

1. 处理标题（ToolBar）
2. 处理TabLayout，展示"商品","评价","商家"
3. ViewPager处理及绑定TabLayout
4. 商品tab下的商品列表展示
5. 商品tab下的购物车
6. 商品tab下的选择商品

首先处理一下toolbar.

## 课堂内容
### 1. 设置 SellerDetailActivity 样式
```xml
<!-- styles.xml -->
<resources>
    <style name="ActivityTheme" parent="Theme.AppCompat.Light.NoActionBar">
        <!--通过编辑器，colorPrimaryDark指定状态栏的颜色-->
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
    </style>
</resources>

<!-- colors.xml -->
<resources>
    <color name="colorPrimary">#3F51B5</color>
</resources>

<!-- AndroidManifest.xml -->
<activity android:name=".ui.activity.SellerDetailActivity" android:theme="@style/ActivityTheme"></activity>
```

- 添加样式 `ActivityTheme`，用于商铺详情 Activity；
- colorPrimaryDark 可以设置状态栏颜色，但 colorPrimaryDark 属性只对 api23 以上有效果。

### 2. 设置 Toolbar 颜色
```xml
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/seller_detail_container"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <android.support.v7.widget.Toolbar
            ....

            android:background="@color/colorPrimaryDark" />

        ....

    </LinearLayout>
</FrameLayout>
```

- 在 activity_seller_detail.xml 中设置 Toolbar 背景颜色和状态栏颜色相同。

### 3. 设置 Toolbar 标题
```java
public class SellerDetailActivity extends BaseActivity {

    @InjectView(R.id.toolbar)
    Toolbar toolbar;
    @InjectView(R.id.tabs)
    TabLayout tabs;
    @InjectView(R.id.vp)
    ViewPager vp;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_seller_detail);
        ButterKnife.inject(this);

        Intent intent = getIntent();

        sellerId = intent.getLongExtra("seller_id", -1);
        String name = intent.getStringExtra("name");

        toolbar.setTitle(name);// Toolbar的相关配置需要在setSupportActionBar之前完成

        setSupportActionBar(toolbar);// 替换ActionBar
    }
}
```

- 在 SellerHolder 中增加传递商铺名称到 intent 中；
- 在 SellerDetailActivity 中获取商铺名字 `intent.getStringExtra("name")`。

### 4. 设置 Toolbar 返回按钮
```java
getSupportActionBar().setDisplayHomeAsUpEnabled(true);
```

## 重点难点讲解

## 问题和练习

### 问题

### 练习

## 拓展和参考资料
