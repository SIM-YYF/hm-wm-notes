# 029_切换Fragment
## 学习目标
- 实现点击底部 tab 栏切换内容区域

## 学习基础要求
- 熟悉 fragment 的使用
- 理解 fragment 切换本质

## 引言和回顾
底部 tab 栏实现了，下面我们就来实现点击底部 tab 栏切换 tab 对应的内容区域，我们打算采用前面大家都很熟悉的 fragment 切换来实现，首先一起来回顾一下fragment 的基本使用步骤：

1. 获取 FragmentManager 对象
2. 开启 fragment 事务
3. 创建 fragment 对象
4. 使用 fragment 事务操作 fragment
5. 提交事务

## 课堂内容
### 实现点击底部 tab 栏切换内容区域的 fragment
1. 创建 fragment 集合用来保存 tab 栏对应的 fragment 对象
2. 创建 fragment 对象并保存到集合中
3. 处理点击事件，切换fragment
4. 初始化设置选中第一个tab

```java
public class MainActivity extends AppCompatActivity {
    ......
    
    //1创建 fragment 集合用来保存 tab 栏对应的 fragment 对象
    List<Fragment> fragments = new ArrayList<>();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ButterKnife.inject(this);

        //2创建fragment保存到集合中
        fragments.add(new HomeFragment());
        fragments.add(new InvestFragment());
        fragments.add(new MeFragment());
        fragments.add(new MoreFragment());

        int childCount = switcherContainer.getChildCount();
        for (int i = 0; i < childCount; i++) {
            switcherContainer.getChildAt(i).setOnClickListener(switherOcl);
        }
        //4初始化设置选中第一个tab
        switherOcl.onClick(switcherContainer.getChildAt(0));
    }

    private View.OnClickListener switherOcl = new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            int index = switcherContainer.indexOfChild(v);
            updateUi(index);
            //3处理点击事件，切换fragment
            switchFragment(index);
        }
    };

    /**
     * 3.1使用 FragmentManager 开启 fragment 事务，切换 fragment
     */
    private void switchFragment(int index) {
        Fragment fragment = fragments.get(index);
        getSupportFragmentManager()
                .beginTransaction()
                .replace(R.id.main_fragment_container,fragment)
                .commit();
    }
}
```


## 问题
1. fragment 切换的本质是什么?
    是 View 树的结构的变化，一些 View 从 View 树上移除掉，一些 View 被添加到 View 树上

