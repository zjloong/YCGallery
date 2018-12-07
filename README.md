## YCGallery
画廊浏览和图片缩放控件

#### 目录介绍
- 1.本库优势亮点
- 2.使用介绍
    - 2.1 图片缩放与滑动控件
    - 2.2 支持加载loading的图片缩放控件
    - 2.3 支持ViewPager滑动的画廊控件
- 3.注意要点
    - 3.1 项目注意点
    - 3.2 项目难点问题
    - 3.3 待优化部分
- 4.效果展示
- 5.其他介绍



### 0.使用场景模拟
![image](https://github.com/yangchong211/YCGallery/blob/master/image/galley.gif)


### 1.本库优势亮点
- 支持缩放，目前有两种方式，第一种是双击一下放大n倍，再双击一下则缩放n倍【也就是回到起初图片大小】
- 可以自定义设置缩放最大比例大小，默认是2倍，这个不要瞎设置，如果设置参数小于1或者大于10，则取默认值为2倍
- 双击放大或者缩小时，添加了过渡动画，这样使得体验效果更好。同时放大时，放大图片区域是靠近双击点击的位置
- 双手指可以手势缩放图片大小，缩放模式下，手指长按可以拖动图片，不允许拖出边界之外。如果要设置不缩放，则可以把缩放比例设置为1
- 缩放模式下屏幕上有多于2个手指【可能有的程序猿用5个手指滑动】并且按照任意顺序抬起放下均不会引起错乱和不平滑
- 该缩放控件可以和ViewPager结合使用，效果非常棒，这个你可以跑下demo看下效果就知道
- 当该缩放控件加载大图时，比如超过2MB以上的图片，那么加载图片也需要时间，这时候ZoomLayoutView支持加载loading
- 开发者可以自己实现单击事件和长按事件，比如前产品要求，点击关闭缩放浏览页面，这个也可以实现


### 2.使用介绍
- 如何引用：implementation 'cn.yc:YCGalleryLib:1.1.3'

#### 2.1 图片缩放与滑动控件
- 在布局中
    ```
    <com.yc.cn.ycgallerylib.zoom.view.ZoomImageView
            android:id="@+id/image"
            android:layout_width="match_parent"
            android:layout_height="match_parent" />
    ```
- 代码设置
    ```
    imageView = findViewById(R.id.image);
    imageView.setOnZoomClickListener(new OnZoomClickListener() {
        @Override
        public void onClick(View v) {
            finish();
        }
    });
    imageView.setOnZoomLongClickListener(new OnZoomLongClickListener() {
        @Override
        public boolean onLongClick(View v) {
            return false;
        }
    });
    imageView.setMaxScale(4);
    imageView.setImageResource(R.drawable.image1);
    //注意不要使用setBackground设置图片，它不支持缩放
    //imageView.setBackground();
    ```
- 可以适当添加优化代码
    ```
    @Override
    protected void onDestroy() {
        super.onDestroy();
        if (imageView!=null){
            //重置所有状态，清空mask，停止所有手势，停止所有动画
            imageView.reset();
        }
    }
    ```


#### 2.2 支持加载loading的图片缩放控件
- 布局代码
    ```
    <com.yc.cn.ycgallerylib.zoom.view.ZoomLayoutView
        android:id="@+id/zoomView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
    ```
- 代码设置
    ```
    zoomView = findViewById(R.id.zoomView);
    zoomView.setLoadingVisibility(true);
    Uri parse = Uri.parse("file:///android_asset/yc.png");
    Picasso.with(this)
            .load(parse)
            .memoryPolicy(MemoryPolicy.NO_CACHE)
            .into(zoomView.getImageView(), new Callback() {
                @Override
                public void onSuccess() {
                    zoomView.setZoomViewVisibility(true);
                }
    
                @Override
                public void onError() {
    
                }
            });
    ```


#### 2.3 支持ViewPager滑动的画廊控件
- 在布局中
    ```
    <com.yc.cn.ycgallerylib.gallery.GalleryImageView
        android:id="@+id/scroll_gallery_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="#000" />
    ```
- 在代码中设置
    ```
    scrollGalleryView = findViewById(R.id.scroll_gallery_view);
    scrollGalleryView
            //设置viewPager底部缩略图大小尺寸
            .setThumbnailSize(200)
            //设置是否支持缩放
            .setZoom(true)
            //设置是否隐藏底部缩略图
            .hideThumbnails(false)
            //设置FragmentManager
            .setFragmentManager(getSupportFragmentManager())
            //添加滑动事件，也可以不用添加
            .addOnPageChangeListener(new ViewPager.OnPageChangeListener() {
                @Override
                public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {
    
                }
    
                @Override
                public void onPageSelected(int position) {
                    scrollGalleryView.setCurrentItem(position);
                }
    
                @Override
                public void onPageScrollStateChanged(int state) {
    
                }
            })
            //添加单张图片
            .addBitmap(toBitmap(R.drawable.image1))
            .addBitmap(toBitmap(R.drawable.image2))
            .addBitmap(toBitmap(R.drawable.image3))
            .addBitmap(toBitmap(R.drawable.image4))
            .addBitmap(toBitmap(R.drawable.image5))
            .addBitmap(toBitmap(R.drawable.image6))
            .addBitmap(toBitmap(R.drawable.image7))
            //或者添加图片集合
            .addBitmap(list);
    //获取当前索引处
    int currentItem = scrollGalleryView.getCurrentItem();
    //获取控件Viewpager
    ViewPager viewPager = scrollGalleryView.getViewPager();
    ```

### 3.注意要点
#### 3.1 项目注意点


#### 3.2 项目难点问题
- 3.2.1 滑动点击事件较多，比较难处理
    - 当触摸事件手指抬起或者滑动结束之后，注意如果之前是缩放模式，则还需要触发一下缩放结束动画
    - 当触摸事件手指在第一个点按下，开启滚动模式，记录开始滚动的点，则在矩阵动画过程中不允许启动滚动模式
    - 当触摸事件为ACTION_POINTER_DOWN【代表用户又使用一个手指触摸到屏幕上，在已有一个触摸点的情况下，有新出现了一个触摸点】，则停止所有动画，切换到缩放模式，保存缩放的两个手指
    - 当手指在move移动过程中，记录移动的点，然后处理缩放的逻辑。处理缩放时，注意按照图片缩放中心缩放，并且让缩放中心在缩放点中点上，让图片的缩放中点跟随手指缩放中点
- 3.2.2 图片缩放频繁操作，创建Matrix的优化
    - 创建对象池类。防止频繁new对象产生内存抖动，由于对象池最大长度限制，如果吞度量超过对象池容量，仍然会发生抖动。
    - 此时需要增大对象池容量,但是会占用更多内存
- 3.2.3 图片缩放过程中动画过渡效果处理


#### 3.3 待优化部分
- 3.3.1 如果频繁调用图片缩放控件，如何增加bitmap复用，会不会造成bitmap内存泄漏？
- 3.3.2 如何防止加载过大Bitmap，后期能否添加maxSize来控制加载到内存的图片的尺寸
- 3.3.3 暂时不支持gif动态图片，后期能否添加该功能
- 3.3.4 对于加载超大图，在网上看到有的方案说可以分块显示超大图功能，具体如何实现有待研究
- 3.3.5 对于图片缩放，频繁操作，其实质还是通过Matrix实现，为了防止频繁new对象产生内存抖动，其对象池容量大小该设置多少才合理


### 4.效果展示
![image](https://github.com/yangchong211/YCGallery/blob/master/image/1.png)
![image](https://github.com/yangchong211/YCGallery/blob/master/image/2.jpg)
![image](https://github.com/yangchong211/YCGallery/blob/master/image/3.jpg)
![image](https://github.com/yangchong211/YCGallery/blob/master/image/4.jpg)
![image](https://github.com/yangchong211/YCGallery/blob/master/image/5.jpg)



### 5.其他介绍
#### 关于其他内容介绍
![image](https://upload-images.jianshu.io/upload_images/4432347-7100c8e5a455c3ee.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 关于博客汇总链接
- 1.[技术博客汇总](https://www.jianshu.com/p/614cb839182c)
- 2.[开源项目汇总](https://blog.csdn.net/m0_37700275/article/details/80863574)
- 3.[生活博客汇总](https://blog.csdn.net/m0_37700275/article/details/79832978)
- 4.[喜马拉雅音频汇总](https://www.jianshu.com/p/f665de16d1eb)
- 5.[其他汇总](https://www.jianshu.com/p/53017c3fc75d)


#### 其他推荐
- 博客笔记大汇总【15年10月到至今】，包括Java基础及深入知识点，Android技术博客，Python学习笔记等等，还包括平时开发中遇到的bug汇总，当然也在工作之余收集了大量的面试题，长期更新维护并且修正，持续完善……开源的文件是markdown格式的！同时也开源了生活博客，从12年起，积累共计47篇[近20万字]，转载请注明出处，谢谢！
- 链接地址：https://github.com/yangchong211/YCBlogs
- 如果觉得好，可以star一下，谢谢！当然也欢迎提出建议，万事起于忽微，量变引起质变！


#### 关于LICENSE
```
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```



















