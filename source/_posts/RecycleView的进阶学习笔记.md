---
title: RecycleView的进阶学习笔记
date: 2021-13-8 19:13:25
tags:
---

# RecycleView的进阶学习笔记

基础部分可查看文章：第一行代码-第二版-随记



## RecyclerView适配器中重载函数的作用以及调用顺序

+ getItemCount() 方法

  设置 RecyclerView 一共有多少子项。

+ onCreateViewHolder(ViewGroup parent, int viewType) 方法

  这个方法用于加载 RecyclerView 子项的布局，然后返回一个 ViewHolder 对象，ViewHolder  是一个静态内部类，继承自   RecyclerView.ViewHolder 类

  onCreateViewHolder没有position，但他有viewType，这个其实就是RecyclerView调用getItemViewType(int position)方法得到的

+ onBindViewHolder(ViewHolder viewHolder, int position) 方法

  上面那个方法为子项加载布局，这个方法为子项绑定数据。调用这两个方法后，子项就既有了布局又有了数据。



<!--more-->



**每当 RecyclerView 的子项出现在屏幕上时或将要出现在屏幕上时，系统会按照如下的顺序调用函数**

1 调用 getItemCount() 方法获取子项的数量。

2 调用 onCreateViewHolder(ViewGroup parent, int viewType) 方法加载每一个子项的布局

3 构造一个 ViewHolder 对象。

4 调用 onBindViewHolder(ViewHolder viewHolder, int position) 方法将数据绑定在 ViewHolder 对象中。

**每个子项在创建的时候会重复循环上面4个步骤，直到所有的子项加载完**

还要提醒一下的就是，当所有子项加载完后，移出屏幕的子项再次回到屏幕中时，是不会再次构造 ViewHolder 对象的，也就是说这个对象已经被构造过了，再次出现在屏幕中只需要复用就可以了

参考博客：[探究RecyclerView适配器中重载函数的作用以及调用顺序](https://blog.csdn.net/weixin_43851639/article/details/90139476)





## RecyclerView实现多种item布局（getItemViewType）

### getItemViewType(int position)

这个方法会传进一个参数position表示当前是第几个Item，然后我们可以通过position拿到当前的Item对象，然后判断这个item对象需要哪种视图，返回一个int类型的**视图标志**

每个子项在创建的时候，**onCreatViewHolder(ViewGroup parent, int viewType)**中的viewType参数就是**经getItemViewType(int position)方法处理返回**后的得到的值





### RecyclerView嵌套RecyclerView

**实现思路就是在一个Recyclerview的adapter中再初始化一个Recyclerview的adapter**

在A_RecyclerView的adapter的类外正常实现另外一个B_RecyclerView 的adapter，然后一般在A_recyclerview

的adapter的onBindViewHolder(ViewHolder viewHolder, int position)方法中实例话B_RecyclerView 的adapter，然后setAdapter()，同时setLayoutManager()设置B_RecyclerView的布局





### 大体框架步骤

**实现步骤：**

**Adapter部分（重点）：**

+ 在RecyclerView中，重写方法**getItemViewType(int position)**

+ 然后在**onCreateViewHolder(ViewGroup parent, int viewType) **方法中对应引入布局

+ 对每种item写一个**VIewHolder**来为item显示数据，同时在其中

+ 在**onBindViewHolder(ViewHolder viewHolder, int position)** 中根据对应的ViewHolder或者position对其控件设置数据并显示

  (**如果内部还有RecyclerView，在这一步执行：实例化对应的adapter、setAdapter()、setLayoutManager()**)

  + 通过ViewHolder判断

    ```java
        @Override
        public void onBindViewHolder(RecyclerView.ViewHolder holder, final int position) {
            
            if(holder instanceof NewsViewHolder){
    			...
            } else if(holder instanceof ImageViewHolder){
    			...
            } else if(holder instanceof ProgressViewHolder){
     			...
            }
        }
    ```

  + 通过position判断

    ```java
        @Override
        public void onBindViewHolder(RecyclerView.ViewHolder holder, int position) {
            if (getItemViewType(position) == EMPTY_VIEW) {
    
            } else if (getItemViewType(position) == PROGRESS_VIEW) {
    
            } else if (getItemViewType(position) == IMAGE_VIEW) {
    
            }
            
        }
    ```



**主页面处：**

**实例化上面创建的adapter、调用setAdapter()、调用setLayoutManager()**



**xml中：**

**根据界面对应实现每个不同item的xml布局**





**实例：**

<img src="F:/星辰/Android/Android_note/RecycleView的进阶学习笔记/image-20210310204709421.png" alt="image-20210310204709421" style="zoom: 50%;" />

+ 重写方法**getItemViewType(int position)**

  ```java
  	//设置int类型的视图标志
  	private final int EMPTY_VIEW = 1;
      private final int PROGRESS_VIEW = 2;
      private final int IMAGE_VIEW = 3;   
  
  	@Override
      public int getItemViewType(int position) {
          if(list.size() == 0){
              return EMPTY_VIEW;
          } else if(list.get(position) == null){
              return PROGRESS_VIEW;
          } else if(list.get(position).getType().equals(News.IMAGE_NEWS)){
              return IMAGE_VIEW;
          } else {
              return super.getItemViewType(position);
          }
      }
  ```

+ 然后在**onCreateViewHolder(ViewGroup parent, int viewType) **方法中对应引入布局

  ```java
      @Override
      public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
          View view;
          if(viewType == PROGRESS_VIEW){
              view = LayoutInflater.from(parent.getContext()).inflate(R.layout.progressbar_item, parent, false);
              return new ProgressViewHolder(view);
          } else if(viewType == EMPTY_VIEW){
              return null;
          } else if(viewType == IMAGE_VIEW){
              view = LayoutInflater.from(parent.getContext()).inflate(R.layout.image_news_item, parent, false);
              return new ImageViewHolder(view);
          } else {
              view = LayoutInflater.from(parent.getContext()).inflate(R.layout.news_item, parent, false);
              return new NewsViewHolder(view);
          }
      }
  ```

+ 对每种item写一个**VIewHolder**来为item显示数据

  ```java
      class NewsViewHolder extends RecyclerView.ViewHolder{
   
          @BindView(R.id.news_title)TextView title;
          @BindView(R.id.news_digest)TextView digest;
          @BindView(R.id.news_time)TextView time;
          @BindView(R.id.news_src)ImageView image;
   
          public NewsViewHolder(View itemView) {
              super(itemView);
              ButterKnife.bind(this, itemView);
          }
      }
   
      class ImageViewHolder extends RecyclerView.ViewHolder{
   
          @BindView(R.id.news_title) TextView title;
          @BindView(R.id.image_left) ImageView imageLeft;
          @BindView(R.id.image_right) ImageView imageRight;
          @BindView(R.id.image_middle) ImageView imageMiddle;
          @BindView(R.id.news_time) TextView time;
   
          public ImageViewHolder(View itemView) {
              super(itemView);
              ButterKnife.bind(this, itemView);
          }
      }
   
     class SeckillViewHolder extends RecyclerView.ViewHolder{
          private final Context mContext;
          private TextView tv_time_seckill;
          private TextView tv_more_seckill;
          private RecyclerView rv_seckill;
          private SeckillRecyclerViewAdapter adapter;
  
  
  
          public SeckillViewHolder(Context mContext, View itemView) {
              super(itemView);
              tv_time_seckill = (TextView) itemView.findViewById(R.id.tv_time_seckill);
              tv_more_seckill = (TextView) itemView.findViewById(R.id.tv_more_seckill);
              rv_seckill = (RecyclerView) itemView.findViewById(R.id.rv_seckill);
              this.mContext = mContext;
          }
  
          public void setData(final ResultBeanData.ResultBean.SeckillInfoBean seckill_info) {
              //1.得到数据了
              //2.设置数据：文本和RecyclerView的数据
              adapter = new SeckillRecyclerViewAdapter(mContext,seckill_info.getList());
              rv_seckill.setAdapter(adapter);
  
              //设置布局管理器
              rv_seckill.setLayoutManager(new LinearLayoutManager(mContext,LinearLayoutManager.HORIZONTAL,false));
  
  
          }
      }
  ```

+ 在onBindViewHolder中根据对应的ViewHolder对其控件设置数据并显示

  ```java
      @Override
      public void onBindViewHolder(RecyclerView.ViewHolder holder, final int position) {
          
          if(holder instanceof NewsViewHolder){
              
              NewsViewHolder viewHolder = (NewsViewHolder)holder;
              viewHolder.title.setText(list.get(position).getTitle());
              viewHolder.time.setText(list.get(position).getTime());
              /**
               * Glide加载图片
               */
              Glide.with(context).load(list.get(position).getImageUrl().get(0))
                      .override(dpToPx(72), dpToPx(72)).centerCrop().into(viewHolder.image);
              if(list.get(position).getType().equals(News.TEXT_NEWS)){
                  viewHolder.digest.setText(list.get(position).getDigest());
              } else {
                  viewHolder.digest.setText("");
              }
              
          } else if(holder instanceof ImageViewHolder){
              
              ImageViewHolder viewHolder = (ImageViewHolder)holder;
              viewHolder.title.setText(list.get(position).getTitle());
              viewHolder.time.setText(list.get(position).getTime());
              setItemImage(viewHolder, list, position);
              
          } else if(holder instanceof ProgressViewHolder){
              
              ProgressViewHolder viewHolder = (ProgressViewHolder)holder;
              viewHolder.progressBar.setIndeterminate(true);
   			viewHoldwe.setData();
              
          }
      }
  ```

  









