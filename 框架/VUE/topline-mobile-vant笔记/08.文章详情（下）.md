# 八、文章详情（下）

<img src="./assets/1566972493322.png" width="300">



## 准备文章评论组件

<img src="assets/1569481517218.png" width="300">

1、创建 `views/article/components/article-comment.vue` 并写入以下内容：

```html
<template>
  <div class="article-comments">
    <!-- 评论列表 -->
    <van-list
      v-model="loading"
      :finished="finished"
      finished-text="没有更多了"
      @load="onLoad"
    >
      <van-cell
        v-for="item in list"
        :key="item"
        :title="item"
      >
        <van-image
          slot="icon"
          round
          width="30"
          height="30"
          style="margin-right: 10px;"
          src="https://img.yzcdn.cn/vant/cat.jpeg"
        />
        <span style="color: #466b9d;" slot="title">hello</span>
        <div slot="label">
          <p style="color: #363636;">我出去跟别人说我的是。。。</p>
          <p>
            <span style="margin-right: 10px;">3天前</span>
            <van-button size="mini" type="default">回复</van-button>
          </p>
        </div>
        <van-icon slot="right-icon" name="like-o" />
      </van-cell>
    </van-list>
    <!-- 评论列表 -->

    <!-- 发布评论 -->
    <van-cell-group class="publish-wrap">
      <van-field
        clearable
        placeholder="请输入评论内容"
      >
        <van-button slot="button" size="mini" type="info">发布</van-button>
      </van-field>
    </van-cell-group>
    <!-- /发布评论 -->
  </div>
</template>

<script>
export default {
  name: 'ArticleComment',
  props: {},
  data () {
    return {
      list: [], // 评论列表
      loading: false, // 上拉加载更多的 loading
      finished: false // 是否加载结束
    }
  },

  methods: {
    onLoad () {
      // 异步更新数据
      setTimeout(() => {
        for (let i = 0; i < 10; i++) {
          this.list.push(this.list.length + 1)
        }
        // 加载状态结束
        this.loading = false

        // 数据全部加载完成
        if (this.list.length >= 40) {
          this.finished = true
        }
      }, 500)
    }
  }
}
</script>

<style scoped lang='less'>
.publish-wrap {
  position: fixed;
  left: 0;
  bottom: 0;
  width: 100%;
}

.van-list {
  margin-bottom: 45px;
}
</style>

```

2、在文章详情页面中加载注册文章评论子组件：

```js
import ArticleComment from './components/article-comment'

export default {
  ...
  components: {
    ArticleComment
  }
}
```

3、在文章详情页面中使用文章评论子组件

```html
<div class="article-container">
	...  	
  <!-- 文章评论 -->
  <article-comment />
  <!-- /文章评论 -->
</div>
```

## 展示文章评论列表

> 提示：有评论数据的文章id：138765。

步骤：

- 封装接口
- 请求获取数据
- 处理模板

实现：

1、在 `api/comment.js` 中封装请求方法

```js
/**
 * 评论接口相关请求模块
 */
import request from '@/utils/request'

export function getComments (params) {
  return request({
    method: 'GET',
    url: '/app/v1_0/comments',
    params
  })
}

```

2、在 `views/article/components/article-comment.vue` 组件中加载获取数据

```js
import { getComments } from '@/api/comment'

export default {
  name: 'ArticleComment',
  props: {},
  data () {
    return {
      list: [], // 评论列表
      loading: false, // 上拉加载更多的 loading
      finished: false, // 是否加载结束
+      offset: null
    }
  },

  methods: {
    + async onLoad () {
    +   // 1. 请求获取数据
    +   const { data } = await getComments({
    +     type: 'a', // 评论类型，a-对文章(article)的评论，c-对评论(comment)的回复
    +     source: this.$route.params.articleId, // 源id，文章id或评论id
    +     offset: this.offset, // 获取评论数据的偏移量，值为评论id，表示从此id的数据向后取，不传表示从第一页开始读取数据
    +     // limit // 获取的评论数据个数，不传表示采用后端服务设定的默认每页数据量
    +   })
    + 
    +   // 2. 将数据添加到数组中
    +   this.list.push(...data.data.results)
    + 
    +   // 3. 结束 loading
    +   this.loading = false
    + 
    +   // 4. 判断是否加载结束
    +   //    如果还有数据，则更新获取下一页数据的页码（offset）
    +   //    如果没有数据，则 finished = true
    +   if (data.data.results.length) {
    +     this.offset = data.data.last_id
    +   } else {
    +     this.finished = true
    +   }
    + }
  }
}
```

3、模板绑定

```html
<template>
  <div class="article-comments">
    <!-- 评论列表 -->
    <van-list
      v-model="loading"
      :finished="finished"
      finished-text="没有更多了"
      @load="onLoad"
    >
      <van-cell
        v-for="(item, index) in list"
        :key="index"
+        :title="item.aut_name"
      >
        <van-image
          slot="icon"
          round
          width="30"
          height="30"
          style="margin-right: 10px;"
+          :src="item.aut_photo"
        />
        <span style="color: #466b9d;" slot="title">hello</span>
        <div slot="label">
+          <p style="color: #363636;">{{ item.content }}</p>
          <p>
+            <span style="margin-right: 10px;">{{ item.pubdate | relativeTime }}</span>
+            <van-button size="mini" type="default">回复 {{ item.reply_count }}</van-button>
          </p>
        </div>
        <van-icon slot="right-icon" name="like-o" />
      </van-cell>
    </van-list>
    <!-- 评论列表 -->

    <!-- 发布评论 -->
    <van-cell-group class="publish-wrap">
      <van-field
        clearable
        placeholder="请输入评论内容"
      >
        <van-button slot="button" size="mini" type="info">发布</van-button>
      </van-field>
    </van-cell-group>
    <!-- /发布评论 -->
  </div>
</template>
```



在文章详情组件中传递：

```html
<!-- 文章评论 -->
<article-comment :article-id="$route.params.articleId" />
<!-- /文章评论 -->
```



在文章评论组件中声明接收：

```js
export default {
  ...
  props: ['articleId']
}
```



3、然后在评论列表中请求获取文章评论列表

```js
import { getComments } from '@/api/comment'

export default {
  name: 'ArticleComment',
  props: ['articleId'],
  data () {
    return {
      list: [], // 评论列表
      loading: false, // 上拉加载更多的 loading
      finished: false, // 是否加载结束
+      offset: null, // 获取下一页数据的页码，第1页就是 null
+      limit: 10 // 每页大小
    }
  },

  methods: {
    async onLoad () {
      // 1. 请求获取评论数据
      const { data } = await getComments({
        type: 'a',
        source: this.articleId,
        offset: this.offset,
        limit: this.limit
      })

      // 2. 将数据添加到数组中
      this.list.push(...data.data.results)

      // 3. 将 loading 设置为 false
      this.loading = false

      // 4. 判断数据是否已加载结束
      if (data.data.last_id) {
        // 4.1 如果后面还有数据，则更新获取下一页数据的页码
        this.offset = data.data.last_id
      } else {
        // 4.2 如果后面没数据了，则将 finished 设置为 true
        this.finished = true
      }
    }
  }
}
```

4、最后，模板绑定

```html
  <!-- 评论列表 -->
  <van-list
    v-model="loading"
    :finished="finished"
    finished-text="没有更多了"
    @load="onLoad"
  >
    <van-cell
+      v-for="comment in list"
+      :key="comment.com_id.toString()"
    >
      <van-image
        slot="icon"
        round
        width="30"
        height="30"
        style="margin-right: 10px;"
+        :src="comment.aut_photo"
      />
+      <span style="color: #466b9d;" slot="title">{{ comment.aut_name }}</span>
      <div slot="label">
+        <p style="color: #363636;">{{ comment.content }}</p>
        <p>
+          <span style="margin-right: 10px;">{{ comment.pubdate | relativeTime }}</span>
          <van-button size="mini" type="default">回复</van-button>
        </p>
      </div>
      <van-icon slot="right-icon" name="like-o" />
    </van-cell>
  </van-list>
  <!-- 评论列表 -->
```





## 发布文章评论

步骤：

- 注册发布处理函数
- 请求提交表单
- 根据响应结果进行后续处理

实现：

1、在 `api/comment.js` 中添加封装数据接口

```js
/**
 * 评论接口相关请求模块
 */
import request from '@/utils/request'

/**
 * 获取评论列表
 */
export function getComments (params) {
  return request({
    method: 'GET',
    url: '/app/v1_0/comments',
    params
  })
}

/**
 * 发布评论
 */
+ export function addComment (data) {
+  return request({
+    method: 'POST',
+    url: '/app/v1_0/comments',
+    data
+  })
+ }

```

2、在 `views/article/components/article-comment.vue` 组件中

```html
<template>
  <div class="article-comments">
    <!-- 评论列表 -->
    <van-list
      v-model="loading"
      :finished="finished"
      finished-text="没有更多了"
      @load="onLoad"
    >
      <van-cell
        v-for="(item, index) in list"
        :key="index"
        :title="item.aut_name"
      >
        <van-image
          slot="icon"
          round
          width="30"
          height="30"
          style="margin-right: 10px;"
          :src="item.aut_photo"
        />
        <span style="color: #466b9d;" slot="title">hello</span>
        <div slot="label">
          <p style="color: #363636;">{{ item.content }}</p>
          <p>
            <span style="margin-right: 10px;">{{ item.pubdate | relativeTime }}</span>
            <van-button size="mini" type="default">回复 {{ item.reply_count }}</van-button>
          </p>
        </div>
        <van-icon slot="right-icon" name="like-o" />
      </van-cell>
    </van-list>
    <!-- 评论列表 -->

    <!-- 发布评论 -->
    <van-cell-group class="publish-wrap">
      <van-field
        v-model="commentText"
        clearable
        placeholder="请输入评论内容"
      >
        <van-button
          slot="button"
          size="mini"
          type="info"
+          @click="onAddComment"
        >发布</van-button>
      </van-field>
    </van-cell-group>
    <!-- /发布评论 -->
  </div>
</template>

<script>
+ import { getComments, addComment } from '@/api/comment'

export default {
  name: 'ArticleComment',
  props: {},
  data () {
    return {
      list: [], // 评论列表
      loading: false, // 上拉加载更多的 loading
      finished: false, // 是否加载结束
      offset: null,
+      commentText: ''
    }
  },

  methods: {
    async onLoad () {
      // 1. 请求获取数据
      const { data } = await getComments({
        type: 'a', // 评论类型，a-对文章(article)的评论，c-对评论(comment)的回复
        source: this.$route.params.articleId, // 源id，文章id或评论id
        offset: this.offset // 获取评论数据的偏移量，值为评论id，表示从此id的数据向后取，不传表示从第一页开始读取数据
        // limit // 获取的评论数据个数，不传表示采用后端服务设定的默认每页数据量
      })

      // 2. 将数据添加到数组中
      this.list.push(...data.data.results)

      // 3. 结束 loading
      this.loading = false

      // 4. 判断是否加载结束
      //    如果还有数据，则更新获取下一页数据的页码（offset）
      //    如果没有数据，则 finished = true
      if (data.data.results.length) {
        this.offset = data.data.last_id
      } else {
        this.finished = true
      }
    },

    /**
     * 发布评论
     */
+    async onAddComment () {
+      // 获取评论内容
+      const commentText = this.commentText.trim()
+      if (!commentText) {
+        return
+      }

+      // 请求提交
+      const { data } = await addComment({
+        target: this.$route.params.articleId, // 评论的目标id（评论文章即为文章id，对评论进行回复则为评论id）
6        content: commentText // 评论内容
        // art_id // 文章id，对评论内容发表回复时，需要传递此参数，表明所属文章id。对文章进行评论，不要传此参数。
+      })

+      // 将最新添加的评论数据放到顶部展示
+      this.list.unshift(data.data.new_obj)

+      // 清空文本框
+      this.commentText = ''
+    }
  }
}
</script>

<style scoped lang='less'>
.publish-wrap {
  position: fixed;
  left: 0;
  bottom: 0;
  width: 100%;
}

.van-list {
  margin-bottom: 45px;
}
</style>

```

## 文章评论点赞

1、在 `api/comment.js` 中添加两个数据接口

```js
/**
 * 评论接口相关请求模块
 */
import request from '@/utils/request'

/**
 * 获取评论列表
 */
export function getComments (params) {
  return request({
    method: 'GET',
    url: '/app/v1_0/comments',
    params
  })
}

/**
 * 发布评论
 */
export function addComment (data) {
  return request({
    method: 'POST',
    url: '/app/v1_0/comments',
    data
  })
}

/**
 * 对评论点赞
 */
+ export function addCommentLike (commentId) {
+   return request({
+     method: 'POST',
+     url: '/app/v1_0/comment/likings',
+     data: {
+       target: commentId
+     }
+   })
+ }

/**
 * 取消对评论点赞
 */
+ export function deleteCommentLike (commentId) {
+   return request({
+     method: 'DELETE',
+     url: '/app/v1_0/comment/likings/' + commentId
+   })
+ }

```

2、然后在 `views/article/components/article-comment.vue` 组件中

```html
<template>
  <div class="article-comments">
    <!-- 评论列表 -->
    <van-list
      v-model="loading"
      :finished="finished"
      finished-text="没有更多了"
      @load="onLoad"
    >
      <van-cell
        v-for="(item, index) in list"
        :key="index"
        :title="item.aut_name"
      >
        <van-image
          slot="icon"
          round
          width="30"
          height="30"
          style="margin-right: 10px;"
          :src="item.aut_photo"
        />
        <span style="color: #466b9d;" slot="title">hello</span>
        <div slot="label">
          <p style="color: #363636;">{{ item.content }}</p>
          <p>
            <span style="margin-right: 10px;">{{ item.pubdate | relativeTime }}</span>
            <van-button size="mini" type="default">回复 {{ item.reply_count }}</van-button>
          </p>
        </div>
        <van-icon
          slot="right-icon"
+          :name="item.is_liking ? 'like' : 'like-o'"
+          @click="onCommentLike(item)"
        />
      </van-cell>
    </van-list>
    <!-- 评论列表 -->

    <!-- 发布评论 -->
    <van-cell-group class="publish-wrap">
      <van-field
        v-model="commentText"
        clearable
        placeholder="请输入评论内容"
      >
        <van-button
          slot="button"
          size="mini"
          type="info"
          @click="onAddComment"
        >发布</van-button>
      </van-field>
    </van-cell-group>
    <!-- /发布评论 -->
  </div>
</template>

<script>
import {
  getComments,
  addComment,
+  addCommentLike,
+  deleteCommentLike
} from '@/api/comment'

export default {
  name: 'ArticleComment',
  props: {},
  data () {
    return {
      list: [], // 评论列表
      loading: false, // 上拉加载更多的 loading
      finished: false, // 是否加载结束
      offset: null,
      commentText: ''
    }
  },

  methods: {
    async onLoad () {
      // 1. 请求获取数据
      const { data } = await getComments({
        type: 'a', // 评论类型，a-对文章(article)的评论，c-对评论(comment)的回复
        source: this.$route.params.articleId, // 源id，文章id或评论id
        offset: this.offset // 获取评论数据的偏移量，值为评论id，表示从此id的数据向后取，不传表示从第一页开始读取数据
        // limit // 获取的评论数据个数，不传表示采用后端服务设定的默认每页数据量
      })

      // 2. 将数据添加到数组中
      this.list.push(...data.data.results)

      // 3. 结束 loading
      this.loading = false

      // 4. 判断是否加载结束
      //    如果还有数据，则更新获取下一页数据的页码（offset）
      //    如果没有数据，则 finished = true
      if (data.data.results.length) {
        this.offset = data.data.last_id
      } else {
        this.finished = true
      }
    },

    /**
     * 发布评论
     */
    async onAddComment () {
      // 获取评论内容
      const commentText = this.commentText.trim()
      if (!commentText) {
        return
      }

      // 请求提交
      const { data } = await addComment({
        target: this.$route.params.articleId, // 评论的目标id（评论文章即为文章id，对评论进行回复则为评论id）
        content: commentText // 评论内容
        // art_id // 文章id，对评论内容发表回复时，需要传递此参数，表明所属文章id。对文章进行评论，不要传此参数。
      })

      // 将最新添加的评论数据放到顶部展示
      this.list.unshift(data.data.new_obj)

      // 清空文本框
      this.commentText = ''
    },

    /**
     * 评论点赞/取消评论点赞
     */
    + async onCommentLike (comment) {
    +   const commentId = comment.com_id.toString()
    +   // 如果已点赞，则取消点赞
    +   if (comment.is_liking) {
    +     await deleteCommentLike(commentId)
    +   } else {
    +     // 否则，点赞
    +     await addCommentLike(commentId)
    +   }
    +   comment.is_liking = !comment.is_liking
    + }
  }
}
</script>

<style scoped lang='less'>
.publish-wrap {
  position: fixed;
  left: 0;
  bottom: 0;
  width: 100%;
}

.van-list {
  margin-bottom: 45px;
}
</style>

```



## 评论回复-准备

### 弹层

```html
<template>
  <div class="article-comments">
    <!-- 评论列表 -->
    <van-list
      v-model="loading"
      :finished="finished"
      finished-text="没有更多了"
      @load="onLoad"
    >
      <van-cell
        v-for="(item, index) in list"
        :key="index"
        :title="item.aut_name"
      >
        <van-image
          slot="icon"
          round
          width="30"
          height="30"
          style="margin-right: 10px;"
          :src="item.aut_photo"
        />
        <span style="color: #466b9d;" slot="title">hello</span>
        <div slot="label">
          <p style="color: #363636;">{{ item.content }}</p>
          <p>
            <span style="margin-right: 10px;">{{ item.pubdate | relativeTime }}</span>
            <van-button
              size="mini"
              type="default"
+              @click="onReplyShow"
            >回复 {{ item.reply_count }}</van-button>
          </p>
        </div>
        <van-icon
          slot="right-icon"
          :name="item.is_liking ? 'like' : 'like-o'"
          @click="onCommentLike(item)"
        />
      </van-cell>
    </van-list>
    <!-- 评论列表 -->

    <!-- 发布评论 -->
    <van-cell-group class="publish-wrap">
      <van-field
        v-model="commentText"
        clearable
        placeholder="请输入评论内容"
      >
        <van-button
          slot="button"
          size="mini"
          type="info"
          @click="onAddComment"
        >发布</van-button>
      </van-field>
    </van-cell-group>
    <!-- /发布评论 -->

    <!-- 评论回复 -->
+    <van-popup
+      v-model="isReplyShow"
+      round
+      position="bottom"
+      :style="{ height: '90%' }"
+    />
    <!-- /评论回复 -->
  </div>
</template>

<script>
import {
  getComments,
  addComment,
  addCommentLike,
  deleteCommentLike
} from '@/api/comment'

export default {
  name: 'ArticleComment',
  props: {},
  data () {
    return {
      list: [], // 评论列表
      loading: false, // 上拉加载更多的 loading
      finished: false, // 是否加载结束
      offset: null,
      commentText: '',
+      isReplyShow: false
    }
  },

  methods: {
    async onLoad () {
      // 1. 请求获取数据
      const { data } = await getComments({
        type: 'a', // 评论类型，a-对文章(article)的评论，c-对评论(comment)的回复
        source: this.$route.params.articleId, // 源id，文章id或评论id
        offset: this.offset // 获取评论数据的偏移量，值为评论id，表示从此id的数据向后取，不传表示从第一页开始读取数据
        // limit // 获取的评论数据个数，不传表示采用后端服务设定的默认每页数据量
      })

      // 2. 将数据添加到数组中
      this.list.push(...data.data.results)

      // 3. 结束 loading
      this.loading = false

      // 4. 判断是否加载结束
      //    如果还有数据，则更新获取下一页数据的页码（offset）
      //    如果没有数据，则 finished = true
      if (data.data.results.length) {
        this.offset = data.data.last_id
      } else {
        this.finished = true
      }
    },

    /**
     * 发布评论
     */
    async onAddComment () {
      // 获取评论内容
      const commentText = this.commentText.trim()
      if (!commentText) {
        return
      }

      // 请求提交
      const { data } = await addComment({
        target: this.$route.params.articleId, // 评论的目标id（评论文章即为文章id，对评论进行回复则为评论id）
        content: commentText // 评论内容
        // art_id // 文章id，对评论内容发表回复时，需要传递此参数，表明所属文章id。对文章进行评论，不要传此参数。
      })

      // 将最新添加的评论数据放到顶部展示
      this.list.unshift(data.data.new_obj)

      // 清空文本框
      this.commentText = ''
    },

    /**
     * 评论点赞/取消评论点赞
     */
    async onCommentLike (comment) {
      const commentId = comment.com_id.toString()
      // 如果已点赞，则取消点赞
      if (comment.is_liking) {
        await deleteCommentLike(commentId)
      } else {
        // 否则，点赞
        await addCommentLike(commentId)
      }
      comment.is_liking = !comment.is_liking
    },

    /**
     * 展示回复弹层
     */
+    onReplyShow () {
+      this.isReplyShow = true
+    }
  }
}
</script>

<style scoped lang='less'>
.publish-wrap {
  position: fixed;
  left: 0;
  bottom: 0;
  width: 100%;
}

.van-list {
  margin-bottom: 45px;
}
</style>

```



### 布局

1、创建 `views/article/components/comment-reply.vue` 并写入

```html
<template>
  <div class="article-comments">
    <!-- 导航栏 -->
    <van-nav-bar title="1条回复">
      <van-icon slot="left" name="cross" />
    </van-nav-bar>
    <!-- /导航栏 -->

    <!-- 当前评论 -->
    <van-cell title="当前评论">
      <van-image
        slot="icon"
        round
        width="30"
        height="30"
        style="margin-right: 10px;"
        src=""
      />
      <span style="color: #466b9d;" slot="title">hello</span>
      <div slot="label">
        <p style="color: #363636;">评论内容</p>
        <p>
          <span style="margin-right: 10px;">几天前</span>
          <van-button
            size="mini"
            type="default"
          >回复 10</van-button>
        </p>
      </div>
      <van-icon slot="right-icon" />
    </van-cell>
    <!-- /当前评论 -->

    <van-cell title="全部评论" />

    <!-- 回复列表 -->
    <van-list
      v-model="loading"
      :finished="finished"
      finished-text="没有更多了"
      @load="onLoad"
    >
      <van-cell
        v-for="(item, index) in list"
        :key="index"
        :title="item.aut_name"
      >
        <van-image
          slot="icon"
          round
          width="30"
          height="30"
          style="margin-right: 10px;"
          :src="item.aut_photo"
        />
        <span style="color: #466b9d;" slot="title">hello</span>
        <div slot="label">
          <p style="color: #363636;">{{ item.content }}</p>
          <p>
            <span style="margin-right: 10px;">{{ item.pubdate | relativeTime }}</span>
            <van-button
              size="mini"
              type="default"
            >回复 {{ item.reply_count }}</van-button>
          </p>
        </div>
        <van-icon
          slot="right-icon"
          :name="item.is_liking ? 'like' : 'like-o'"
          @click="onCommentLike(item)"
        />
      </van-cell>
    </van-list>
    <!-- 回复列表 -->

    <!-- 发布回复 -->
    <van-cell-group class="publish-wrap">
      <van-field
        v-model="commentText"
        clearable
        placeholder="请输入评论内容"
      >
        <van-button
          slot="button"
          size="mini"
          type="info"
          @click="onAddComment"
        >发布</van-button>
      </van-field>
    </van-cell-group>
    <!-- /发布回复 -->
  </div>
</template>

<script>
import {
  getComments,
  addComment,
  addCommentLike,
  deleteCommentLike
} from '@/api/comment'

export default {
  name: 'ArticleComment',
  props: {},
  data () {
    return {
      list: [], // 评论列表
      loading: false, // 上拉加载更多的 loading
      finished: false, // 是否加载结束
      offset: null,
      commentText: ''
    }
  },

  methods: {
    async onLoad () {
      // 1. 请求获取数据
      const { data } = await getComments({
        type: 'a', // 评论类型，a-对文章(article)的评论，c-对评论(comment)的回复
        source: this.$route.params.articleId, // 源id，文章id或评论id
        offset: this.offset // 获取评论数据的偏移量，值为评论id，表示从此id的数据向后取，不传表示从第一页开始读取数据
        // limit // 获取的评论数据个数，不传表示采用后端服务设定的默认每页数据量
      })

      // 2. 将数据添加到数组中
      this.list.push(...data.data.results)

      // 3. 结束 loading
      this.loading = false

      // 4. 判断是否加载结束
      //    如果还有数据，则更新获取下一页数据的页码（offset）
      //    如果没有数据，则 finished = true
      if (data.data.results.length) {
        this.offset = data.data.last_id
      } else {
        this.finished = true
      }
    },

    /**
     * 发布评论
     */
    async onAddComment () {
      // 获取评论内容
      const commentText = this.commentText.trim()
      if (!commentText) {
        return
      }

      // 请求提交
      const { data } = await addComment({
        target: this.$route.params.articleId, // 评论的目标id（评论文章即为文章id，对评论进行回复则为评论id）
        content: commentText // 评论内容
        // art_id // 文章id，对评论内容发表回复时，需要传递此参数，表明所属文章id。对文章进行评论，不要传此参数。
      })

      // 将最新添加的评论数据放到顶部展示
      this.list.unshift(data.data.new_obj)

      // 清空文本框
      this.commentText = ''
    },

    /**
     * 评论点赞/取消评论点赞
     */
    async onCommentLike (comment) {
      const commentId = comment.com_id.toString()
      // 如果已点赞，则取消点赞
      if (comment.is_liking) {
        await deleteCommentLike(commentId)
      } else {
        // 否则，点赞
        await addCommentLike(commentId)
      }
      comment.is_liking = !comment.is_liking
    },

    /**
     * 展示回复弹层
     */
    onReplyShow () {
      this.isReplyShow = true
    }
  }
}
</script>

<style scoped lang='less'>
.publish-wrap {
  position: fixed;
  left: 0;
  bottom: 0;
  width: 100%;
}

.van-list {
  margin-bottom: 45px;
}
</style>

```

2、然后在 `views/article/components/article-comment.vue` 组件中加载使用

```html
<template>
  <div class="article-comments">
    <!-- 评论列表 -->
    <van-list
      v-model="loading"
      :finished="finished"
      finished-text="没有更多了"
      @load="onLoad"
    >
      <van-cell
        v-for="(item, index) in list"
        :key="index"
        :title="item.aut_name"
      >
        <van-image
          slot="icon"
          round
          width="30"
          height="30"
          style="margin-right: 10px;"
          :src="item.aut_photo"
        />
        <span style="color: #466b9d;" slot="title">hello</span>
        <div slot="label">
          <p style="color: #363636;">{{ item.content }}</p>
          <p>
            <span style="margin-right: 10px;">{{ item.pubdate | relativeTime }}</span>
            <van-button
              size="mini"
              type="default"
              @click="onReplyShow"
            >回复 {{ item.reply_count }}</van-button>
          </p>
        </div>
        <van-icon
          slot="right-icon"
          :name="item.is_liking ? 'like' : 'like-o'"
          @click="onCommentLike(item)"
        />
      </van-cell>
    </van-list>
    <!-- 评论列表 -->

    <!-- 发布评论 -->
    <van-cell-group class="publish-wrap">
      <van-field
        v-model="commentText"
        clearable
        placeholder="请输入评论内容"
      >
        <van-button
          slot="button"
          size="mini"
          type="info"
          @click="onAddComment"
        >发布</van-button>
      </van-field>
    </van-cell-group>
    <!-- /发布评论 -->

    <!-- 评论回复 -->
    <van-popup
      v-model="isReplyShow"
      round
      position="bottom"
      :style="{ height: '90%' }"
    >
+      <comment-reply />
    </van-popup>
    <!-- /评论回复 -->
  </div>
</template>

<script>
import {
  getComments,
  addComment,
  addCommentLike,
  deleteCommentLike
} from '@/api/comment'
+ import CommentReply from './comment-reply'

export default {
  name: 'ArticleComment',
+  components: {
+    CommentReply
+   },
  props: {},
  data () {
    return {
      list: [], // 评论列表
      loading: false, // 上拉加载更多的 loading
      finished: false, // 是否加载结束
      offset: null,
      commentText: '',
      isReplyShow: false
    }
  },

  methods: {
    async onLoad () {
      // 1. 请求获取数据
      const { data } = await getComments({
        type: 'a', // 评论类型，a-对文章(article)的评论，c-对评论(comment)的回复
        source: this.$route.params.articleId, // 源id，文章id或评论id
        offset: this.offset // 获取评论数据的偏移量，值为评论id，表示从此id的数据向后取，不传表示从第一页开始读取数据
        // limit // 获取的评论数据个数，不传表示采用后端服务设定的默认每页数据量
      })

      // 2. 将数据添加到数组中
      this.list.push(...data.data.results)

      // 3. 结束 loading
      this.loading = false

      // 4. 判断是否加载结束
      //    如果还有数据，则更新获取下一页数据的页码（offset）
      //    如果没有数据，则 finished = true
      if (data.data.results.length) {
        this.offset = data.data.last_id
      } else {
        this.finished = true
      }
    },

    /**
     * 发布评论
     */
    async onAddComment () {
      // 获取评论内容
      const commentText = this.commentText.trim()
      if (!commentText) {
        return
      }

      // 请求提交
      const { data } = await addComment({
        target: this.$route.params.articleId, // 评论的目标id（评论文章即为文章id，对评论进行回复则为评论id）
        content: commentText // 评论内容
        // art_id // 文章id，对评论内容发表回复时，需要传递此参数，表明所属文章id。对文章进行评论，不要传此参数。
      })

      // 将最新添加的评论数据放到顶部展示
      this.list.unshift(data.data.new_obj)

      // 清空文本框
      this.commentText = ''
    },

    /**
     * 评论点赞/取消评论点赞
     */
    async onCommentLike (comment) {
      const commentId = comment.com_id.toString()
      // 如果已点赞，则取消点赞
      if (comment.is_liking) {
        await deleteCommentLike(commentId)
      } else {
        // 否则，点赞
        await addCommentLike(commentId)
      }
      comment.is_liking = !comment.is_liking
    },

    /**
     * 展示回复弹层
     */
    onReplyShow () {
      this.isReplyShow = true
    }
  }
}
</script>

<style scoped lang='less'>
.publish-wrap {
  position: fixed;
  left: 0;
  bottom: 0;
  width: 100%;
}

.van-list {
  margin-bottom: 45px;
}
</style>

```

## 展示当前评论

1、把在文章评论组件中所点击的评论对象传递到评论回复组件中

```html
<template>
  <div class="article-comments">
    <!-- 评论列表 -->
    <van-list
      v-model="loading"
      :finished="finished"
      finished-text="没有更多了"
      @load="onLoad"
    >
      <van-cell
        v-for="(item, index) in list"
        :key="index"
        :title="item.aut_name"
      >
        <van-image
          slot="icon"
          round
          width="30"
          height="30"
          style="margin-right: 10px;"
          :src="item.aut_photo"
        />
        <span style="color: #466b9d;" slot="title">{{ item.aut_name }}</span>
        <div slot="label">
          <p style="color: #363636;">{{ item.content }}</p>
          <p>
            <span style="margin-right: 10px;">{{ item.pubdate | relativeTime }}</span>
            <van-button
              size="mini"
              type="default"
+              @click="onReplyShow(item)"
            >回复 {{ item.reply_count }}</van-button>
          </p>
        </div>
        <van-icon
          slot="right-icon"
          :name="item.is_liking ? 'like' : 'like-o'"
          @click="onCommentLike(item)"
        />
      </van-cell>
    </van-list>
    <!-- 评论列表 -->

    <!-- 发布评论 -->
    <van-cell-group class="publish-wrap">
      <van-field
        v-model="commentText"
        clearable
        placeholder="请输入评论内容"
      >
        <van-button
          slot="button"
          size="mini"
          type="info"
          @click="onAddComment"
        >发布</van-button>
      </van-field>
    </van-cell-group>
    <!-- /发布评论 -->

    <!-- 评论回复 -->
    <van-popup
      v-model="isReplyShow"
      round
      position="bottom"
      :style="{ height: '90%' }"
    >
+      <comment-reply :comment="currentComment" />
    </van-popup>
    <!-- /评论回复 -->
  </div>
</template>

<script>
import {
  getComments,
  addComment,
  addCommentLike,
  deleteCommentLike
} from '@/api/comment'
import CommentReply from './comment-reply'

export default {
  name: 'ArticleComment',
  components: {
    CommentReply
  },
  props: {},
  data () {
    return {
      list: [], // 评论列表
      loading: false, // 上拉加载更多的 loading
      finished: false, // 是否加载结束
      offset: null,
      commentText: '',
      isReplyShow: false,
+      currentComment: {} // 查看回复的当前评论
    }
  },

  methods: {
    async onLoad () {
      // 1. 请求获取数据
      const { data } = await getComments({
        type: 'a', // 评论类型，a-对文章(article)的评论，c-对评论(comment)的回复
        source: this.$route.params.articleId, // 源id，文章id或评论id
        offset: this.offset // 获取评论数据的偏移量，值为评论id，表示从此id的数据向后取，不传表示从第一页开始读取数据
        // limit // 获取的评论数据个数，不传表示采用后端服务设定的默认每页数据量
      })

      // 2. 将数据添加到数组中
      this.list.push(...data.data.results)

      // 3. 结束 loading
      this.loading = false

      // 4. 判断是否加载结束
      //    如果还有数据，则更新获取下一页数据的页码（offset）
      //    如果没有数据，则 finished = true
      if (data.data.results.length) {
        this.offset = data.data.last_id
      } else {
        this.finished = true
      }
    },

    /**
     * 发布评论
     */
    async onAddComment () {
      // 获取评论内容
      const commentText = this.commentText.trim()
      if (!commentText) {
        return
      }

      // 请求提交
      const { data } = await addComment({
        target: this.$route.params.articleId, // 评论的目标id（评论文章即为文章id，对评论进行回复则为评论id）
        content: commentText // 评论内容
        // art_id // 文章id，对评论内容发表回复时，需要传递此参数，表明所属文章id。对文章进行评论，不要传此参数。
      })

      // 将最新添加的评论数据放到顶部展示
      this.list.unshift(data.data.new_obj)

      // 清空文本框
      this.commentText = ''
    },

    /**
     * 评论点赞/取消评论点赞
     */
    async onCommentLike (comment) {
      const commentId = comment.com_id.toString()
      // 如果已点赞，则取消点赞
      if (comment.is_liking) {
        await deleteCommentLike(commentId)
      } else {
        // 否则，点赞
        await addCommentLike(commentId)
      }
      comment.is_liking = !comment.is_liking
    },

    /**
     * 展示回复弹层
     */
    onReplyShow (comment) {
+      this.currentComment = comment
      this.isReplyShow = true
    }
  }
}
</script>

<style scoped lang='less'>
.publish-wrap {
  position: fixed;
  left: 0;
  bottom: 0;
  width: 100%;
}

.van-list {
  margin-bottom: 45px;
}
</style>

```

2、然后在子组件接收使用

```html
<template>
  <div class="article-comments">
    <!-- 导航栏 -->
+    <van-nav-bar :title="comment.reply_count + '条回复'">
      <van-icon slot="left" name="cross" />
    </van-nav-bar>
    <!-- /导航栏 -->

    <!-- 当前评论 -->
    <van-cell title="当前评论">
      <van-image
        slot="icon"
        round
        width="30"
        height="30"
        style="margin-right: 10px;"
+        :src="comment.aut_photo"
      />
+      <span style="color: #466b9d;" slot="title">{{ comment.aut_name }}</span>
      <div slot="label">
+        <p style="color: #363636;">{{ comment.content }}</p>
        <p>
+          <span style="margin-right: 10px;">{{ comment.pubdate | relativeTime }}</span>
          <van-button
            size="mini"
            type="default"
+          >回复 {{ comment.reply_count }}</van-button>
        </p>
      </div>
      <van-icon slot="right-icon" />
    </van-cell>
    <!-- /当前评论 -->

    <van-cell title="全部评论" />

    <!-- 回复列表 -->
    <van-list
      v-model="loading"
      :finished="finished"
      finished-text="没有更多了"
      @load="onLoad"
    >
      <van-cell
        v-for="(item, index) in list"
        :key="index"
        :title="item.aut_name"
      >
        <van-image
          slot="icon"
          round
          width="30"
          height="30"
          style="margin-right: 10px;"
          :src="item.aut_photo"
        />
        <span style="color: #466b9d;" slot="title">hello</span>
        <div slot="label">
          <p style="color: #363636;">{{ item.content }}</p>
          <p>
            <span style="margin-right: 10px;">{{ item.pubdate | relativeTime }}</span>
            <van-button
              size="mini"
              type="default"
            >回复 {{ item.reply_count }}</van-button>
          </p>
        </div>
        <van-icon
          slot="right-icon"
          :name="item.is_liking ? 'like' : 'like-o'"
          @click="onCommentLike(item)"
        />
      </van-cell>
    </van-list>
    <!-- 回复列表 -->

    <!-- 发布回复 -->
    <van-cell-group class="publish-wrap">
      <van-field
        v-model="commentText"
        clearable
        placeholder="请输入评论内容"
      >
        <van-button
          slot="button"
          size="mini"
          type="info"
          @click="onAddComment"
        >发布</van-button>
      </van-field>
    </van-cell-group>
    <!-- /发布回复 -->
  </div>
</template>

<script>
import {
  getComments,
  addComment,
  addCommentLike,
  deleteCommentLike
} from '@/api/comment'

export default {
  name: 'CommentReply',
+  props: ['comment'],
  data () {
    return {
      list: [], // 评论列表
      loading: false, // 上拉加载更多的 loading
      finished: false, // 是否加载结束
      offset: null,
      commentText: ''
    }
  },

  methods: {
    async onLoad () {
      // 1. 请求获取数据
      const { data } = await getComments({
        type: 'a', // 评论类型，a-对文章(article)的评论，c-对评论(comment)的回复
        source: this.$route.params.articleId, // 源id，文章id或评论id
        offset: this.offset // 获取评论数据的偏移量，值为评论id，表示从此id的数据向后取，不传表示从第一页开始读取数据
        // limit // 获取的评论数据个数，不传表示采用后端服务设定的默认每页数据量
      })

      // 2. 将数据添加到数组中
      this.list.push(...data.data.results)

      // 3. 结束 loading
      this.loading = false

      // 4. 判断是否加载结束
      //    如果还有数据，则更新获取下一页数据的页码（offset）
      //    如果没有数据，则 finished = true
      if (data.data.results.length) {
        this.offset = data.data.last_id
      } else {
        this.finished = true
      }
    },

    /**
     * 发布评论
     */
    async onAddComment () {
      // 获取评论内容
      const commentText = this.commentText.trim()
      if (!commentText) {
        return
      }

      // 请求提交
      const { data } = await addComment({
        target: this.$route.params.articleId, // 评论的目标id（评论文章即为文章id，对评论进行回复则为评论id）
        content: commentText // 评论内容
        // art_id // 文章id，对评论内容发表回复时，需要传递此参数，表明所属文章id。对文章进行评论，不要传此参数。
      })

      // 将最新添加的评论数据放到顶部展示
      this.list.unshift(data.data.new_obj)

      // 清空文本框
      this.commentText = ''
    },

    /**
     * 评论点赞/取消评论点赞
     */
    async onCommentLike (comment) {
      const commentId = comment.com_id.toString()
      // 如果已点赞，则取消点赞
      if (comment.is_liking) {
        await deleteCommentLike(commentId)
      } else {
        // 否则，点赞
        await addCommentLike(commentId)
      }
      comment.is_liking = !comment.is_liking
    },

    /**
     * 展示回复弹层
     */
    onReplyShow () {
      this.isReplyShow = true
    }
  }
}
</script>

<style scoped lang='less'>
.publish-wrap {
  position: fixed;
  left: 0;
  bottom: 0;
  width: 100%;
}

.van-list {
  margin-bottom: 45px;
}
</style>

```





## 展示评论回复列表

1、将评论回复组件中请求获取评论列表的参数稍加修改就可以获取回复列表了（同一个数据接口）

```js
import {
  getComments,
  addComment,
  addCommentLike,
  deleteCommentLike
} from '@/api/comment'

export default {
  name: 'CommentReply',
  props: ['comment'],
  data () {
    return {
      list: [], // 评论列表
      loading: false, // 上拉加载更多的 loading
      finished: false, // 是否加载结束
      offset: null,
      commentText: ''
    }
  },

  created () {
    console.log('created')
  },

  methods: {
    async onLoad () {
      // 1. 请求获取数据
      const { data } = await getComments({
+        type: 'c', // 评论类型，a-对文章(article)的评论，c-对评论(comment)的回复
+        source: this.comment.com_id.toString(), // 源id，文章id或评论id
        offset: this.offset // 获取评论数据的偏移量，值为评论id，表示从此id的数据向后取，不传表示从第一页开始读取数据
        // limit // 获取的评论数据个数，不传表示采用后端服务设定的默认每页数据量
      })

      // 2. 将数据添加到数组中
      this.list.push(...data.data.results)

      // 3. 结束 loading
      this.loading = false

      // 4. 判断是否加载结束
      //    如果还有数据，则更新获取下一页数据的页码（offset）
      //    如果没有数据，则 finished = true
      if (data.data.results.length) {
        this.offset = data.data.last_id
      } else {
        this.finished = true
      }
    },

    /**
     * 发布评论
     */
    async onAddComment () {
      // 获取评论内容
      const commentText = this.commentText.trim()
      if (!commentText) {
        return
      }

      // 请求提交
      const { data } = await addComment({
        target: this.$route.params.articleId, // 评论的目标id（评论文章即为文章id，对评论进行回复则为评论id）
        content: commentText // 评论内容
        // art_id // 文章id，对评论内容发表回复时，需要传递此参数，表明所属文章id。对文章进行评论，不要传此参数。
      })

      // 将最新添加的评论数据放到顶部展示
      this.list.unshift(data.data.new_obj)

      // 清空文本框
      this.commentText = ''
    },

    /**
     * 评论点赞/取消评论点赞
     */
    async onCommentLike (comment) {
      const commentId = comment.com_id.toString()
      // 如果已点赞，则取消点赞
      if (comment.is_liking) {
        await deleteCommentLike(commentId)
      } else {
        // 否则，点赞
        await addCommentLike(commentId)
      }
      comment.is_liking = !comment.is_liking
    },

    /**
     * 展示回复弹层
     */
    onReplyShow () {
      this.isReplyShow = true
    }
  }
}
```

2、文章评论列表组件中使用 `v-if` 控制回复列表组件的动态渲染

```html
<template>
  <div class="article-comments">
    <!-- 评论列表 -->
    <van-list
      v-model="loading"
      :finished="finished"
      finished-text="没有更多了"
      @load="onLoad"
    >
      <van-cell
        v-for="(item, index) in list"
        :key="index"
        :title="item.aut_name"
      >
        <van-image
          slot="icon"
          round
          width="30"
          height="30"
          style="margin-right: 10px;"
          :src="item.aut_photo"
        />
        <span style="color: #466b9d;" slot="title">{{ item.aut_name }}</span>
        <div slot="label">
          <p style="color: #363636;">{{ item.content }}</p>
          <p>
            <span style="margin-right: 10px;">{{ item.pubdate | relativeTime }}</span>
            <van-button
              size="mini"
              type="default"
              @click="onReplyShow(item)"
            >回复 {{ item.reply_count }}</van-button>
          </p>
        </div>
        <van-icon
          slot="right-icon"
          :name="item.is_liking ? 'like' : 'like-o'"
          @click="onCommentLike(item)"
        />
      </van-cell>
    </van-list>
    <!-- 评论列表 -->

    <!-- 发布评论 -->
    <van-cell-group class="publish-wrap">
      <van-field
        v-model="commentText"
        clearable
        placeholder="请输入评论内容"
      >
        <van-button
          slot="button"
          size="mini"
          type="info"
          @click="onAddComment"
        >发布</van-button>
      </van-field>
    </van-cell-group>
    <!-- /发布评论 -->

    <!-- 评论回复 -->
    <van-popup
      v-model="isReplyShow"
      round
      position="bottom"
      :style="{ height: '90%' }"
    >
      <comment-reply
        :comment="currentComment"
+        v-if="isReplyShow"
      />
    </van-popup>
    <!-- /评论回复 -->
  </div>
</template>
```

弹层渲染出来以后就只是简单的切换显示和隐藏，里面的内容也不再重新渲染了，所以会导致我们的评论的回复列表不会动态更新了。解决办法就是在每次弹层显示的时候重新渲染组件。

## 发布评论回复

在评论回复组件中，对发布评论的请求稍加修改即可：

```js
import {
  getComments,
  addComment,
  addCommentLike,
  deleteCommentLike
} from '@/api/comment'

export default {
  name: 'CommentReply',
  props: ['comment'],
  data () {
    return {
      list: [], // 评论列表
      loading: false, // 上拉加载更多的 loading
      finished: false, // 是否加载结束
      offset: null,
      commentText: ''
    }
  },

  created () {
    console.log('created')
  },

  methods: {
    async onLoad () {
      // 1. 请求获取数据
      const { data } = await getComments({
        type: 'c', // 评论类型，a-对文章(article)的评论，c-对评论(comment)的回复
        source: this.comment.com_id.toString(), // 源id，文章id或评论id
        offset: this.offset // 获取评论数据的偏移量，值为评论id，表示从此id的数据向后取，不传表示从第一页开始读取数据
        // limit // 获取的评论数据个数，不传表示采用后端服务设定的默认每页数据量
      })

      // 2. 将数据添加到数组中
      this.list.push(...data.data.results)

      // 3. 结束 loading
      this.loading = false

      // 4. 判断是否加载结束
      //    如果还有数据，则更新获取下一页数据的页码（offset）
      //    如果没有数据，则 finished = true
      if (data.data.results.length) {
        this.offset = data.data.last_id
      } else {
        this.finished = true
      }
    },

    /**
     * 发布评论
     */
    async onAddComment () {
      // 获取评论内容
      const commentText = this.commentText.trim()
      if (!commentText) {
        return
      }

      // 请求提交
      const { data } = await addComment({
+        target: this.comment.com_id.toString(), // 评论的目标id（评论文章即为文章id，对评论进行回复则为评论id）
        content: commentText, // 评论内容
+        art_id: this.$route.params.articleId // 文章id，对评论内容发表回复时，需要传递此参数，表明所属文章id。对文章进行评论，不要传此参数。
      })

      // 将最新添加的评论数据放到顶部展示
      this.list.unshift(data.data.new_obj)

      // 清空文本框
      this.commentText = ''

      // 更新当前评论的回复数量
+      this.comment.reply_count++
    },

    /**
     * 评论点赞/取消评论点赞
     */
    async onCommentLike (comment) {
      const commentId = comment.com_id.toString()
      // 如果已点赞，则取消点赞
      if (comment.is_liking) {
        await deleteCommentLike(commentId)
      } else {
        // 否则，点赞
        await addCommentLike(commentId)
      }
      comment.is_liking = !comment.is_liking
    },

    /**
     * 展示回复弹层
     */
    onReplyShow () {
      this.isReplyShow = true
    }
  }
}
```

## 在评论回复组件中关闭弹层

1、在评论回复组件中发布一个自定义事件

```html
<template>
  <div class="article-comments">
    <!-- 导航栏 -->
    <van-nav-bar :title="comment.reply_count + '条回复'">
      <van-icon
        slot="left"
        name="cross"
+        @click="$emit('close')"
      />
    </van-nav-bar>
    <!-- /导航栏 -->

    <!-- 当前评论 -->
    <van-cell title="当前评论">
      <van-image
        slot="icon"
        round
        width="30"
        height="30"
        style="margin-right: 10px;"
        :src="comment.aut_photo"
      />
      <span style="color: #466b9d;" slot="title">{{ comment.aut_name }}</span>
      <div slot="label">
        <p style="color: #363636;">{{ comment.content }}</p>
        <p>
          <span style="margin-right: 10px;">{{ comment.pubdate | relativeTime }}</span>
          <van-button
            size="mini"
            type="default"
          >回复 {{ comment.reply_count }}</van-button>
        </p>
      </div>
      <van-icon slot="right-icon" />
    </van-cell>
    <!-- /当前评论 -->

    <van-cell title="全部评论" />

    <!-- 回复列表 -->
    <van-list
      v-model="loading"
      :finished="finished"
      finished-text="没有更多了"
      @load="onLoad"
    >
      <van-cell
        v-for="(item, index) in list"
        :key="index"
        :title="item.aut_name"
      >
        <van-image
          slot="icon"
          round
          width="30"
          height="30"
          style="margin-right: 10px;"
          :src="item.aut_photo"
        />
        <span style="color: #466b9d;" slot="title">hello</span>
        <div slot="label">
          <p style="color: #363636;">{{ item.content }}</p>
          <p>
            <span style="margin-right: 10px;">{{ item.pubdate | relativeTime }}</span>
            <van-button
              size="mini"
              type="default"
            >回复 {{ item.reply_count }}</van-button>
          </p>
        </div>
        <van-icon
          slot="right-icon"
          :name="item.is_liking ? 'like' : 'like-o'"
          @click="onCommentLike(item)"
        />
      </van-cell>
    </van-list>
    <!-- 回复列表 -->

    <!-- 发布回复 -->
    <van-cell-group class="publish-wrap">
      <van-field
        v-model="commentText"
        clearable
        placeholder="请输入评论内容"
      >
        <van-button
          slot="button"
          size="mini"
          type="info"
          @click="onAddComment"
        >发布</van-button>
      </van-field>
    </van-cell-group>
    <!-- /发布回复 -->
  </div>
</template>
```

2、在文章评论组件中监听处理

```html
<template>
  <div class="article-comments">
    <!-- 评论列表 -->
    <van-list
      v-model="loading"
      :finished="finished"
      finished-text="没有更多了"
      @load="onLoad"
    >
      <van-cell
        v-for="(item, index) in list"
        :key="index"
        :title="item.aut_name"
      >
        <van-image
          slot="icon"
          round
          width="30"
          height="30"
          style="margin-right: 10px;"
          :src="item.aut_photo"
        />
        <span style="color: #466b9d;" slot="title">{{ item.aut_name }}</span>
        <div slot="label">
          <p style="color: #363636;">{{ item.content }}</p>
          <p>
            <span style="margin-right: 10px;">{{ item.pubdate | relativeTime }}</span>
            <van-button
              size="mini"
              type="default"
              @click="onReplyShow(item)"
            >回复 {{ item.reply_count }}</van-button>
          </p>
        </div>
        <van-icon
          slot="right-icon"
          :name="item.is_liking ? 'like' : 'like-o'"
          @click="onCommentLike(item)"
        />
      </van-cell>
    </van-list>
    <!-- 评论列表 -->

    <!-- 发布评论 -->
    <van-cell-group class="publish-wrap">
      <van-field
        v-model="commentText"
        clearable
        placeholder="请输入评论内容"
      >
        <van-button
          slot="button"
          size="mini"
          type="info"
          @click="onAddComment"
        >发布</van-button>
      </van-field>
    </van-cell-group>
    <!-- /发布评论 -->

    <!-- 评论回复 -->
    <van-popup
      v-model="isReplyShow"
      round
      position="bottom"
      :style="{ height: '90%' }"
    >
      <comment-reply
        :comment="currentComment"
        v-if="isReplyShow"
+        @close="isReplyShow = false"
      />
    </van-popup>
    <!-- /评论回复 -->
  </div>
</template>
```



