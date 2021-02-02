<template>
  <div id="app" class="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <p>摇一摇</p>
    <p>{{ text }}</p>
    <van-action-sheet v-model="shakePermissionVisible" title="申请" :closeable="false" :close-on-click-overlay="false" class="popup">
      <div class="popup-body">获取您的摇一摇权限</div>
      <div class="popup-footer">
        <van-button plain  type="primary" size="small" class="popup-footer__btn" @click="shakePermissionVisible = false">拒 绝</van-button>
        <van-button type="primary" size="small" class="popup-footer__btn" @click="getPermission">允 许</van-button>
      </div>
    </van-action-sheet>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      text: '',
      shakePermissionVisible: false
    }
  },
  mounted() {
    if (typeof (DeviceMotionEvent) !== 'undefined' && typeof (DeviceMotionEvent.requestPermission) === 'function') {
      this.getPermission()
    } else {
      this.text = '您的设备不支持摇一摇'
    }
  },
  methods: {
    getPermission() {
      DeviceMotionEvent.requestPermission().then((response) => {
        if (response === 'denied') {
          this.text = '拒绝摇一摇权限'
          this.shakePermissionVisible = false
          return
        }
        this.text = '已授权'
        this.shakePermissionVisible = false
        // (optional) Do something after API prompt dismissed.
        // if (response === 'granted') {
        //   window.addEventListener('devicemotion', (e) => {
        //     // do something for 'e' here.
        //   })
        // }
      }).catch((err) => {
        this.text = err + new Date().getTime()
        this.shakePermissionVisible = true
      })
    }
  }
}
</script>

<style lang="scss">
.app {
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.popup {
  &-body {
    padding-bottom: 50px;
  }
  &-footer {
    padding-bottom: 20px;
    &__btn {
      min-width: 80px;
      border-radius: 6px !important;
      margin: 0 10px !important;
    }
  }
}
</style>
