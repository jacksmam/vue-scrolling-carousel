<template>
  <div class="carousel"
    @touchstart="onTouchStart"
    @touchmove="onTouchMove"
    @touchend="onTouchEnd"
    :class="{'with-animation': !onTouching}"
    :style="{'transform': `translateX(${transformOffset}px)`}">
    <slot></slot>
  </div>  
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  props:['length', 'startPage','onCarouselMove', 'onPageUpdate'],
  data: function () {
    return {
      page: this.startPage || 0, //表示中のページ0始まり
      paging: false, //ページングするときの処理
      browserWidth: window.innerWidth, //画面サイズ
      onTouching: false, //タッチ中かどうか
      verticalMoving: false, //縦方向の移動状態
      horizontalMoving: false, //横方向の移動状態
      pointX: 0, //x方向の前回の位置
      pointY: 0, //y方向の前回の位置
      transformX: 0
    }
  },
  methods: {
    onTouchStart: function (event) {
      this.paging = false;
      this.onTouching = true;
      this.browserWidth = window.innerWidth;

      var touches = event.changedTouches;
      const pointX = touches[0].pageX;
      const pointY = touches[0].pageY;
      this.pointX = pointX;
      this.pointY = pointY;
    },
    onTouchMove: function (event) {
      var touches = event.changedTouches;
      const pointX = touches[0].pageX;
      const pointY = touches[0].pageY;
      var diffX = this.pointX - pointX;
      const diffY = this.pointY - pointY;

      // 縦横の判定がまだならすぐにする
      if (!this.verticalMoving && !this.horizontalMoving) {
        const absX = Math.abs(diffX);
        const absY = Math.abs(diffY);
        // 縦方向の移動時はページングしない用にフラグを立てる
        if (absX > absY) this.horizontalMoving = true;
        if (absY > absX) this.verticalMoving = true;
      }

      // 画面移動の処理
      if (this.horizontalMoving) {
        // 画面半分までに抑制
        if (this.page === 0 && Math.abs(diffX) > (this.browserWidth / 2)) {
          diffX = (this.browserWidth / 2) * (diffX > 0 ? 1 : -1);
        }
        this.transformX = diffX;
        this.onCarouselMove(diffX / this.browserWidth);
      }
    },
    onTouchEnd: function (event) {
      var touches = event.changedTouches;
      const diffX = this.pointX - touches[0].pageX;
      if (this.isOverPagingThreshold(diffX)) {
        //ページング処理
        this.paging = true;
        this.page += diffX > 0 ? 1 : -1;
        if (this.page < 0) this.page = 0;
        if (this.page >= this.length) this.page = this.length - 1;
        // ページ変更の通知
        this.onPageUpdate(this.page);
      } else {
        this.onCarouselMove(null);
      }

      // 初期化
      this.onTouching = false;
      this.verticalMoving = false;
      this.horizontalMoving = false;
      this.pointX = 0;
      this.pointY = 0;
      this.transformX = 0;
    },
    onScroll: function (event) {
      // 横方向にスクロールしてるときは縦方向のイベントを無効化する
      if (this.horizontalMoving & event && event.preventDefault) {
        event.preventDefault();
        return;
      }
    },
    // ページングするべきところまで移動しているかのチェック処理
    isOverPagingThreshold: function (diffX) {
      if (!this.horizontalMoving) return false;

      return Math.abs(diffX) > (this.browserWidth / 4);
    }
  },
  computed: {
    transformOffset: function () {
      return (this.page * this.browserWidth + this.transformX) * -1
    }
  }
})
</script>

<style scoped>
  .carousel {
    width: 100%;
    height: calc(100% - 30px);
  }

  .with-animation {
    transition: 300ms transform ease-in-out 0ms;
  }
</style>


