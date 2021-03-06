<template>
  <div class="header">
    <div class="content-wrapper">
      <div class="avatar">
        <img width="64" height="64" :src="seller.avatar">
      </div>
      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{seller.name}}</span>
        </div>
        <div class="description">
          {{seller.description}}/{{seller.deliveryTime}}分钟送达
        </div>
        <div class="support" v-if="seller.supports">
          <span class="icon" :class="classMap[seller.supports[0].type]"></span>
          <span class="text">{{seller.supports[0].description}}</span>
        </div>
      </div>
      <div v-if="seller.supports" class="support-count" @click="showDetail">
        <span class="count">{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div class="board-wrapper" @click="showDetail">
      <span class="board-title"></span><span class="board-text">{{seller.bulletin}}</span>
      <i class="icon-keyboard_arrow_right"></i>
    </div>
    <div class="background">
      <img :src="seller.avatar" width="100%" height="100%">
    </div>
    <transition name="fade">
    <div v-show="Detail" class="detail">
      <div class="detail-wrapper clearfix">
        <div class="detail-content">
          <h1 class="name">{{seller.name}}</h1>
          <div class="star-wrapper">
            <star-score :size="48" :score="seller.score"></star-score>
          </div>
          <div class="title">
            <div class="line"></div>
            <div class="text">优惠信息</div>
            <div class="line"></div>
          </div>
          <ul v-if="seller.supports" class="supports">
            <!-- 和 vue1.0 不同的 就是关于index语法糖的使用 -->
            <li class="support-item" v-for="(item, index) in seller.supports">
              <span class="icon" :class="classMap[seller.supports[index].type]"></span>
              <span class="text">{{seller.supports[index].description}}</span>
            </li>
          </ul>
          <div class="title">
            <div class="line"></div>
            <div class="text">商家公告</div>
            <div class="line"></div>
          </div>
          <div class="board">
            <p class="content">{{seller.bulletin}}</p>
          </div>
        </div>
      </div>
      <div class="detail-close" @click="hideDetail">
        <i class="icon-close"></i>
      </div>
    </div>
    </transition>
  </div>
</template>

<script type="text/javascript">
import {getSellerData} from '../../common/js/getApiData.js'
import starScore from '../../components/star-score/star-score.vue'

const ERR_OK = 0

export default {
  // 不能和h5原生标签重名 会弹警告的
  name: 'v-header',
  data() {
    return {
      seller: {},
      Detail: false
    }
  },
  mounted() {
    this._getSellerData()
    // 来做跟后台数据type对应的样式
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
  },
  methods: {
    _getSellerData() {
      getSellerData().then((res) => {
        if (res.errno === ERR_OK) {
          this.seller = res.data
          // console.log('seller:')
          // console.log(this.seller)
        }
      })
    },
    showDetail() {
      this.Detail = true
    },
    hideDetail() {
      this.Detail = false
    }
  },
  components: {
    starScore
  }
}
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  @import "../../common/stylus/base.styl"

  .header
    color: #fff
    position: relative
    overflow: hidden
    background: rgba(7,17,27,0.5)
    .content-wrapper
      position: relative
      padding: 24px 12px 18px 24px
      // 给父元素加上 font-size: 0 消除子元素的空白间隙
      font-size: 0
      .avatar
        display: inline-block
        vertical-align: top
        img
          border-radius: 2px
      .content
        display: inline-block
        margin-left: 16px
        .title
          margin: 2px 0 8px 0px
          .brand
            display: inline-block
            width: 30px
            height: 18px
            // 使用了mixin中的带DPR查询的方法
            bg-image('brand')
            background-size: 30px 18px
            background-repeat: no-repeat
            // 设置对齐方式 和文字信息对齐
            vertical-align: top
          .name
            margin-left: 6px
            font-size: 16px
            line-height: 18px
            font-weight: bold
        .description
          margin-bottom: 10px
          line-height: 12px
          font-size: 12px
        .support
          .icon
            display: inline-block
            vertical-align: top
            width: 12px
            height: 12px
            margin-right: 4px
            background-size: 12px 12px
            background-repeat: no-repeat
            // 父样式的各种子样式
            &.decrease
              bg-image('decrease_1')
            &.discount
              bg-image('discount_1')
            &.guarantee
              bg-image('guarantee_1')
            &.invoice
              bg-image('invoice_1')
            &.special
              bg-image('special_1')
          .text
            line-height: 12px
            font-size: 10px
      .support-count
        position: absolute
        right: 12px
        bottom: 8px
        padding: 0 8px
        height: 24px
        line-height: 24px
        border-radius: 14px
        background-color: rgba(0,0,0,0.2)
        text-align: center
        .count
          font-size: 10px
          vertical-align: top
          line-height: 24px
        .icon-keyboard_arrow_right
          font-size: 10px
          line-height: 24px
          margin-left: 2px
    .board-wrapper
      position: relative
      height: 28px
      line-height: 28px
      padding: 0 22px 0 12px
      background: rgba(7,17,27,0.2)
      // 超出文本显示 ... 的复合属性
      white-space: nowrap
      overflow: hidden
      text-overflow: ellipsis
      .board-title
        display: inline-block
        vertical-align: top
        margin-top: 8px
        width: 22px
        height: 12px
        bg-image('bulletin')
        // size拼错了 没报错 但是样式没渲染上
        background-size: 22px 12px
        background-repeat: no-repeat
      .board-text
        vertical-align: top
        margin:0 2px
        font-size: 10px
      .icon-keyboard_arrow_right
        position: absolute
        font-size: 10px
        right: 10px
        top: 8px
    .background
      position: absolute
      left: 0
      top: 0
      width: 100%
      // height: 100%
      // 背景层 压在下面的
      z-index: -1
      // 滤镜产生模糊效果
      filter: blur(10px)
    .detail
      position: fixed
      // 务必记得加上定位 不然不会是整屏的
      // 不跳转路由 只是使用 z-index 遮挡
      z-index: 100
      top: 0
      left: 0
      width: 100%
      height: 100%
      overflow: auto
      background: rgba(7,17,27,0.8)
      // IOS下的背景模糊效果
      backdrop-filter: blur(10px)
      transition: all 1.5s
      // 动画定义
      &.fade-enter-active, &.fade-leave-active
        opacity: 1
        background: rgba(7,17,27,0.8)
      &.fade-enter, &.fade-leave-to
        opacity: 0
        background: rgba(7,17,27,0)
      // sticky footer
      // 当页面内容不够长的时候 页脚块粘贴在视窗底部
      // 当内容足够长的时候 页脚块会被向下推送
      // 就是内容是否会和底部页脚重合
      // 用 fixed 布局 则不会被顶下去
      // 本例使用的方法 是在页面的大div中创建两个子div
      // 一个是装内容的div wrapper 带clearfix
      // 另一个是底部内容div
      .detail-wrapper
        width: 100%
        min-height: 100%
        .detail-content
        // 向上有margin 底部有padding 腾位置给底部元素
        // padding是一定要的!!!!
          margin-top: 64px
          padding-bottom: 64px
          .name
            line-height: 16px
            text-align: center
            font-size: 16px
            font-weight: 700
          .star-wrapper
            margin-top: 18px
            padding: 2px 0
            text-align: center
          .title
            display: flex
            width: 80%
            // 水平居中
            margin 28px auto 24px auto
            .line
              flex: 1
              position: relative
              top: -6px
              border-bottom: 1px solid rgba(255,255,255,0.2)
            .text
              padding: 0 12px
              font-size: 14px
              font-weight: 700
          .supports
            width: 80%
            margin: 0 auto
            .support-item
              padding: 0 12px
              margin-bottom: 12px
              font-size: 0
              &:last-child
                margin-bottom: 0
              .icon
                display: inline-block
                width: 16px
                height: 16px
                vertical-align: top
                margin-right: 6px
                background-size: 16px 16px
                background-repeat: no-repeat
                &.decrease
                  bg-image('decrease_2')
                &.discount
                  bg-image('discount_2')
                &.guarantee
                  bg-image('guarantee_2')
                &.invoice
                  bg-image('invoice_2')
                &.special
                  bg-image('special_2')
              .text
                line-height: 16px
                font-size: 12px
          .board
            width: 80%
            margin: 0 auto
            .content
              padding: 0 12px
              line-height: 24px
              font-size: 12px
      .detail-close
        position: relative
        width: 32px
        height: 32px
        // margin 和 清除浮动 都是固定套路
        margin: -64px auto 0 auto
        clear: both
        font-size: 32px


</style>