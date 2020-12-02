# Vue 小案例 音乐播放器

### 1. 要完成的功能点

```
1.歌曲搜索
2.歌曲播放
3.歌曲封面
4.歌曲评论
5.播放动画
6.mv播放器
```

### 2.歌曲搜索

```
1.按下回车进行搜索(v-on  .enter)
2.查询数据（axios 接口  v-model）
3.数据渲染 （v-for）


4.歌曲搜索的接口
  https://autumnfish.cn/search?keywords=后来
  请求方式:get
  请求参数名：keywords
```

**2.1 代码实现**

```
1.搜索表单绑定 query 属性
2.回车发送发送ajax请求

new Vue({
		el: '#player',
		data: {
			query: '',
			musicArr: []
		},
		methods: {
			//音乐搜索
			searchMusic() {
				//alert(this.serchName)
				var that = this;
				//发送ajax请求
				axios.get("https://autumnfish.cn/search?keywords=" + that.query).then(function(response) {
					//console.log(response.data); //返回的数据
					//console.log(response.data.result.songs)
					that.musicArr = response.data.result.songs;
				})
			}
		}
	})
```

### 3.歌曲播放

```
1.点击播放，v-on 绑定事件，并把歌曲的id传过去。歌曲的id，在上步歌曲搜索的时候，可以拿到。
2.歌曲地址的获取。请求对应的接口 根据id获取
3.歌曲地址的设置，把歌曲的地址设置给 video标签的src属性即可。

4.歌曲的接口
  https://autumnfish.cn/song/url?id=254574
  请求方式get
  请求参数id  值为歌曲的id编号
  
  
```

**3.1 代码实现**

```
1.给歌曲条目绑定点击事件，并把歌曲的id传递过来
   <li v-for="item,index in musicArr" :key="index">
								<a href="javascript:;" @click="playMuisc(item.id)"></a>
								<b>{{item.name}}</b>
								<span><i></i></span>
							</li>

2.根据传递过来的歌曲id，查询接口。返回该首歌的信息
 playMusic(id) {
				var that = this;
				//发送ajax请求
				axios.get("https://autumnfish.cn/song/url?id=" + id).then(function(response) {
					//console.log(response.data.data[0].url);
					that.musicUrl=response.data.data[0].url;
				})
			}


3.给vue增加一个属性musicUrl 用来保存 歌曲的url地址
	
4.把从接口中获取出的歌曲地址，设置给vue的属性 musicUrl

5.找到audio标签给，绑定他的src属性。把musicUrl绑定给他
<audio ref='audio' :src="musicUrl" controls autoplay loop class="myaudio"></audio>
							
```

### 4.歌曲封面

```
1. 在刚才的点击播放中的代码中增加点逻辑
2. 歌曲封面的获取（请求对应的接口，根据歌曲id获取封面的信息）
3. 歌曲封面的设置 (属性的绑定)

5.歌曲封面信息的接口
   https://autumnfish.cn/song/detail?ids=254574
   请求方式 get
   请求参数名 ids  值为歌曲的id
   
   
```

**4.1 代码实现**

```
1. 在 playMusic()方法中，根据id 请求歌曲封面接口，获取歌曲封面图片的url
playMusic(id) {
				var that = this;
				//发送ajax请求
				axios.get("https://autumnfish.cn/song/url?id=" + id).then(function(response) {
					//console.log(response.data.data[0].url);
					that.musicUrl=response.data.data[0].url;
				});
				//请求封面的详情
				axios.get("https://autumnfish.cn/song/detail?ids="+id).then(function(response) {
					//console.log(response.data.songs[0].al.picUrl);
					that.imgUrl=response.data.songs[0].al.picUrl;
				});
			}
2. 在vue点的属性中增加一个 imgUrl 用来保存 上一步请求到的歌曲封面图片地址
3. 给img标签绑定属性

```

### 5.歌曲评论

```
1.在点击播放中增加逻辑，来获取歌曲的评论
2.请求该歌曲的评论接口
3.歌曲评论的渲染
4.歌曲评论的接口地址
  https://autumnfish.cn/comment/hot?type=0&id=254574
  请求方式：get
  请求参数 type=0 是固定的
  请求参数 id  值是该歌曲的id
```

**5.1代码实现**

```
1.在playMusic()方法中，根据id 请求歌曲的评论内容
//请求歌曲评论内容
				axios.get("https://autumnfish.cn/comment/hot?type=0&id="+id).then(function(response) {
					//console.log(response.data.hotComments);
					that.hotComments=response.data.hotComments;
				});
2.在vue的属性中提供一个属性 hotComments 类型是数组
3.把请求到的歌曲评论，设置给 hotComments
4.页面进行渲染数据
<h5 class='title'>热门留言</h5>
						<div class='comment_list'>
							<dl v-for="item in hotComments">
								<!--评论人的图像 -->
								<dt><img :src="item.user.avatarUrl" alt=""></dt>
								<!-- 评论人的昵称 -->
								<dd class="name">{{item.user.nickname}}</dd>
								<!-- 评论人的评论内容 -->
								<dd class="detail">
									{{item.content}}
								</dd>
							</dl>
						</div>
```

### 6. 黑胶唱片的旋转动画

```
1.唱片的旋转与暂停，是在增删一个 class 类名
2.播放时增加这个 class类名 暂停时删除这个class类名
3.audio标签上，有播放和暂停的事件属性 ，我们绑定这两个事件，在事件中，来改变是否播放的标记
```

**6.1 代码实现**

```
1.给audio标签,绑定点击事件
<audio ref='audio' @play="play()" @pause="pause()" :src="musicUrl" controls autoplay loop class="myaudio"></audio>

2.在vue的属性中添加一个 isPlaying=false  用来表示播放的状态，默认false 表示没有播放

3. 在play()方法中添加 把 isPlaying 置为true
   在pause()方法中 把isPlaying 置为true false
   
4. 在标签上，添加class属性 class 属性名为 playing  
<div class="player_con" :class="{playing:isPlaying}">
```

### 7.MV的播放

```
1.mv图标的显示，v-if 有的歌曲有mv 有的么有
2.mv地址的获取，请求相应的接口
3.遮罩层的显示和隐藏 v-show v-on
4.在video标签上 绑定视频的地址
5.接口：
 https://autumnfish.cn/mv/url?id=5365400
 请求方式：get
 请求参数 id  值为mv的id  (mvid=0 说明该首歌没有mv)
 
```

**7.1 代码编写**

```
1.mv图标的是否显示 在我们根据id搜索歌曲后，响应信息中有一个mvid 判断他不等于0 说明有mv 显示mv图标即可
<span v-if="item.mvid!=0"><i></i></span>
2.给mv图标绑定点击事件，并传递mvid
<span v-if="item.mvid!=0" @click="playMv(item.mvid)"><i></i></span>


3.在vue中添加一个mvUrl属性，用来保存上一步请求的mv路径
4.在vue中再提供一个属性isShow 用来是否展示遮罩层， isShow=false 默认为false 不显示

5.编写playMv()方法，请求接口。获取mv的路径，以及把 isShow置为ture 表示显示遮罩层

playMv(mvid){
				var that=this;
				axios.get("https://autumnfish.cn/mv/url?id=" + mvid).then(function(response) {
					//console.log(response.data.data.url);
					that.mvUrl=response.data.data.url;
					that.isShow=true;
				});
			}

6.在div上和video标签上绑定定数据
  <div class="video_con" style="display: none;" v-show="isShow">
					<video :src="mvUrl" controls="controls"></video>
					<div class="mask"></div>
				</div>


 7.点击遮罩层，取消遮罩。暂停mv
 <!-- 遮罩层，给他绑定事件，取消遮罩 -->
	<div class="mask" @click="hide()" ref="mv"></div>
	
  8.编写hide()方法
  		
			hide(){
				//修改标记，取消遮罩
				this.isShow=false;
				//获取视频标签对象，调用暂停方法暂停mv
				this.$refs.mv.pause();
			}

```







