# 042_整合TabLayout与ViewPager
## 学习目标

## 引言和回顾

## 课堂内容
### 1. 定义 tab 栏数据数组
```java
private String[] titles = new String[]{"商品", "评价", "商家"};
```

- 在 SellerDetailActivity 中定义 tab 栏数组。

### 2. 找到 ViewPager 和 TabLayout
```java
public class SellerDetailActivity extends BaseActivity {

    @InjectView(R.id.toolbar)
    Toolbar toolbar;
    @InjectView(R.id.tabs)
    TabLayout tabs;
    @InjectView(R.id.vp)
    ViewPager vp;

    private String[] titles = new String[]{"商品", "评价", "商家"};

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_seller_detail);
        ButterKnife.inject(this);

        ....
    }
}
```

### 3. 设置 ViewPager 和 TabLayout
```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    ....

    adapter = new MyAdapter(getSupportFragmentManager());
    vp.setAdapter(adapter);
    tabs.setupWithViewPager(vp);
}
```

- 给 ViewPager 设置 Adapter;
- 给 TabLayout 设置 ViewPager。

### 4. 定义 Adapter
```java
private class MyAdapter extends FragmentPagerAdapter {

    public MyAdapter(FragmentManager fm) {
        super(fm);
    }

    @Override
    public Fragment getItem(int position) {
        Fragment fragment = null;
        switch (position) {
            case 0:
                fragment = new GoodsFragment();
                break;
            case 1:
                fragment = new RecommendFragment();
                break;
            case 2:
                fragment = new SellerFragment();
                break;
        }
        return fragment;
    }

    @Override
    public int getCount() {
        return titles.length;
    }

    @Override
    public CharSequence getPageTitle(int position) {
        return titles[position];
    }
}
```

- `getPageTitle()` 方法返回每个 position 对应的 tab 栏文字；
- FragmentPagerAdapter 和 FragmentStatePagerAdapter 的区别是，FragmentPagerAdapter 会缓存 Fragment 对象，FragmentStatePagerAdapter 只会缓存 Fragment 的状态；FragmentPagerAdapter 更加适合 fragment 数量比较少，页面切换要求响应速度更快的情况；
- 定义3个Fragment类。

## 重点难点讲解

## 问题和练习

### 问题

### 练习

## 拓展和参考资料

