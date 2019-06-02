# android.Fragment-notes
android入门-第一行代码的第四章内容 (所有的android之小a为入门阶段~)  
# 将以定位章节 和 自己的理解为主加以整理    

## 4.1 碎片是什么 
* 一句话就是可以放在活动里的小活动 也可以包含布局啥的还有生命周期    
![图片](https://github.com/pawNku/image.inGithub/blob/master/4.1.png?raw=true)
![图片](https://github.com/pawNku/image.inGithub/blob/master/4.1(2).png?raw=true)   
* 可以看出来适用于这样的 把两个UI界面分别变成两个碎片 然后再同一个活动里引用这两个碎片即可了    

## 4.2.1 碎片的简单用法   
![图片](https://github.com/pawNku/image.inGithub/blob/master/4.2.1.png?raw=true)   
* 在fragment类中强烈建议使用support-v4库中的Fragment 因为可以使其在所有的系统版本中保持一致 而build.gradle中的appcompat-v7中也是自动包含了support-v4的  
```
@Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container,
                             @Nullable Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.news_title_frag, container, false);
        return view;
```
* 首先在左右的自己Fragment类中重写onCreateView方法 来引入对应的布局即可   
![图片](https://github.com/pawNku/image.inGithub/blob/master/4.2.1(2).png?raw=true)   
* 通过<fragment>标签在活动的布局中加入碎片  以上都是静态的加入fragment  
![图片](https://github.com/pawNku/image.inGithub/blob/master/4.2.1(3).png?raw=true)   
  * 动态的加载Fragment 1.创建待添加的碎片实例 2.通过getSupportFragmentManager来获取FragmentManager 3.并且开启事务beginTransaction
  4.添加或者替换 replace 5.最后就是提交事务 commit    
  
## 4.2.4 碎片和活动之间进行通信 
* 从活动中获得碎片 FM的findFragmentById   
`Fragment fragment = (Fragment)getFragmentManager().findFragmentById(R.id.fragment);`    
* 在碎片中获得活动   
`MainActivity activity = (MainActivity) getActivity();`   

## 4.3.1 碎片的状态和回调   
* 1.运行状态：可见的 2.暂停状态：由于另一个**未占满**屏幕的活动添加到了栈顶 3.停止状态 通过事务的remove等方法被移除了 4.销毁状态 由于依附的活动被销毁了这样的    
* 一些重要的回调   
![图片](https://github.com/pawNku/image.inGithub/blob/master/4.3.1.png?raw=true)  
![图片](https://github.com/pawNku/image.inGithub/blob/master/4.3.1(2).png?raw=true)    

## 4.5 碎片的最佳实践-->一个简易版的新闻应用  

* 1.首先因为用到RecyclerView需要添加依赖库   
`compile 'com.android.support:recyclerview-v7:25.3.1'`  
* 2.准备好新闻的实体类 News
![图片](https://github.com/pawNku/image.inGithub/blob/master/4.5.png?raw=true)   
* 3.新闻内容的布局:头部表示显示新闻标题 正文 新闻内容 中间用一个View来当做细线分开
```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
                android:layout_width="match_parent"
                android:layout_height="match_parent">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/visibility_layout"
        android:orientation="vertical"
        android:visibility="invisible">

        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:id="@+id/news_title"
            android:gravity="center"
            android:padding="10dp"
            android:textSize="20sp"/>

        <View
            android:layout_width="match_parent"
            android:layout_height="1dp"
            android:background="#000"/>

        <TextView
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="1"
            android:id="@+id/news_content"
            android:padding="15dp"
            android:textSize="18sp"/>

    </LinearLayout>
    <View
        android:layout_width="1dp"
        android:layout_height="match_parent"
        android:layout_alignParentLeft="true"
        android:background="#000"/>

</RelativeLayout>
```
* 4.新建一个内容的Fragment 在oncreateView的回调中加载了布局 然后自定义了一个refresh方法用来显示UI
![图片](https://github.com/pawNku/image.inGithub/blob/master/4.5(2).png?raw=true)  
* 5.内容的Activity了 首先获取intent里的extra 然后在活动中获取fragment 然后通过自定义的refresh来显示UI
![图片](https://github.com/pawNku/image.inGithub/blob/master/4.5(3).png?raw=true)    
> 这里有个actionStart方法 是因为比如啊我们要往下一个活动传递信息 但是下一个活动又不是你写的 这样你也不知道传啥 所以我们在下一个活动也就是这里的NewsContentActivity  然后所有的信息都从actionStart来传递进来 --> 自己知道传递啥玩意自己high  而原来的只要在原活动中调用 NewsContentActivity.actionStart    

* 6.显示新闻列表的布局  
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="match_parent"
              android:layout_height="match_parent">

    <android.support.v7.widget.RecyclerView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/news_title_recycler_view">

    </android.support.v7.widget.RecyclerView>

</LinearLayout>
```
* 7.列表的子item布局 padding 用来给控件周围加上补白 singleLine用来只能单行显示 ellipsize end则超过后在尾部缩略     
```
<TextView
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:id="@+id/news_title"
    android:singleLine="true"
    android:ellipsize="end"
    android:textSize="18sp"
    android:paddingLeft="10dp"
    android:paddingRight="10dp"
    android:paddingTop="15dp"
    android:paddingBottom="15dp">

</TextView>
```
* 8.展示新闻列表的fragment  onCreateView就是加载布局 onActivityCreated通过判断是否有news_content_layout来看是否双页   
```
public class NewsTitleFragment extends Fragment {

    private boolean isTwoPane;

    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container,
                             @Nullable Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.news_title_frag, container, false);
        return view;
    }
    @Override
    public void onActivityCreated(@Nullable Bundle savedInstanceState) {
        super.onActivityCreated(savedInstanceState);

        //通过是否有右内容布局来判断是否双页
        if (getActivity().findViewById(R.id.news_content_layout) != null) {
            isTwoPane = true;
        } else {
            isTwoPane = false;
        }
    }
```   
* 9.各种单页双页的布局  双页模式下引入了两个碎片
![图片](https://github.com/pawNku/image.inGithub/blob/master/4.5(4).png?raw=true)    
* 10.最后的就是展示新闻列表数据的RecyclerView的适配器 通过了内部类NewsAdapter    
```
//不单独写了 so：内部类来当适配器 0.ViewHolder 1.构造方法 2.三步走
    class NewsAdapter extends RecyclerView.Adapter<NewsAdapter.ViewHolder>{

        private final List<News> mNewsList;
        //onCreateViewHolder中首先获得了News的实例 然后通过isTwoPane来判断是否为双页 看打开几个
        @Override
        public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
            View view = LayoutInflater.from(parent.getContext())
                    .inflate(R.layout.news_item, parent, false);
            final ViewHolder holder = new ViewHolder(view);
            view.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    News news = mNewsList.get(holder.getAdapterPosition());
                    if(isTwoPane) {
                        //如果是双页则寻找创建刷新出内容的碎片
                        NewsContentFragment newsContentFragment =
                                (NewsContentFragment) getFragmentManager()
                                        .findFragmentById(R.id.news_content_fragment);
                        newsContentFragment.refresh(news.getTitle(),news.getContent());
                    }else {
                        //如果是单页则直接启动一个新的活动
                        NewsContentActivity.actionStart(getActivity(),news.getTitle(),news.getContent());
                    }
                }
            });

            return holder;
        }

        @Override
        public void onBindViewHolder(ViewHolder holder, int position) {
            News news = mNewsList.get(position);
            holder.mNewsTitleText.setText(news.getTitle());
        }

        @Override
        public int getItemCount() {
            return mNewsList.size();
        }
        
        // 用ViewHolder来复用了
        public class ViewHolder extends RecyclerView.ViewHolder {

            private final TextView mNewsTitleText;  //往外升一级

            public ViewHolder(View itemView) {
                super(itemView);
                mNewsTitleText = (TextView) itemView.findViewById(R.id.news_title);
            }
        }

        public NewsAdapter(List<News> newsList) {
            mNewsList = newsList;
        }
    }
```
* 11.向RecyclerView中填充数据 所谓的填充也就是各种给RecyclerView设置适配器咯  还有就是模拟数据~   
![图片](https://github.com/pawNku/image.inGithub/blob/master/4.5(5).png?raw=true)  














