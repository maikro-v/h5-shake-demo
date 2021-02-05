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
      SHAKE_THRESHOLD: 3000,
      current: {
        time: 0,
        x: 0,
        y: 0,
        z: 0
      },
      last: {
        time: 0,
        x: 0,
        y: 0,
        z: 0
      },
      text: '',
      shakePermissionVisible: false
    }
  },
  mounted() {
    // if (typeof (DeviceMotionEvent) !== 'undefined' && typeof (DeviceMotionEvent.requestPermission) === 'function') {
    //   this.getPermission()
    // } else {
    //   this.text = '您的设备不支持摇一摇'
    // }
    this.checkEquipment()
  },
  methods: {
    // 检查设备是否可以监听摇一摇
    checkEquipment() {
      console.log(typeof (DeviceMotionEvent) !== 'undefined', typeof (DeviceMotionEvent.requestPermission))
      if (typeof (DeviceMotionEvent) !== 'undefined' && typeof (DeviceMotionEvent.requestPermission) === 'function') {
        this.getPermission()
      } else {
        // 如果不是ios
        window.addEventListener('devicemotion', this.deviceMotionHandler, false)
        // 不提示了，因为安卓也是走进这里，如果实在要提示就判断设备吧
        // this.text = '您的设备不支持摇一摇'
        // this.$toast('您的设备不支持摇一摇，需手动点击按钮抽签')
      }
    },
    getPermission() {
      DeviceMotionEvent.requestPermission().then((response) => {
        if (response === 'denied') {
          this.text = '拒绝摇一摇权限'
          this.shakePermissionVisible = false
          return
        }
        this.text = '已授权'
        this.shakePermissionVisible = false
        if (response === 'granted') {
          window.addEventListener('devicemotion', this.deviceMotionHandler, false)
        }
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
    },
    deviceMotionHandler(eventData) {
      // let SHAKE_THRESHOLD = 4000
      // let last_update = 0
      // let x, y, z, last_x = 0, last_y = 0, last_z = 0
      // return (eventData) => {
      const acceleration = eventData.accelerationIncludingGravity
      this.current.time = new Date().getTime()
      if ((this.current.time - this.last.time) > 100) {
        const diffTime = this.current.time - this.last.time
        this.last.time = this.current.time
        this.current.x = acceleration.x
        this.current.y = acceleration.y
        this.current.z = acceleration.z
        const speed = Math.abs(this.current.x + this.current.y + this.current.z - this.last.x - this.last.y - this.last.z) / diffTime * 10000
        this.speed = speed
        if (speed > this.SHAKE_THRESHOLD) {
          window.removeEventListener('devicemotion', () => {
            console.log('不再监听摇一摇事件')
          }, false)
          // 开始抽奖
          // this.start()
        }
        this.last.x = this.current.x
        this.last.y = this.current.y
        this.last.z = this.current.z
      }
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
