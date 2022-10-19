<template>
<div class="floor">
  <div class="floor__btn">
    {{ btn__data.floor }}

    <div
      @click="floorActive"
      class="floor__btn_target"
      :class="{active: btn__data.active}">
    </div>
  </div>
</div>
</template>

<script>
export default {
 props: {
  btn__data: {
   type: Object,
   default() {
    return {}
   }
  },
 },

 methods: {
  floorActive() {
    if (this.btn__data.used == true) {
      return false
    } else {
      this.$emit('containsBtns', this.btn__data)
    }
  }
 },
}
</script>

<style lang="scss">
.floor {
 height: 150px;
 display: flex;
 align-items: center;
}

.floor__btn {
 display: flex;
 flex-direction: column;
 align-items: center;
 gap: 5px;
 font-size: 16px;
 font-weight: 500;
}

.floor__btn_target {
 position: relative;
 cursor: pointer;
 width: 20px;
 height: 20px;
 padding: 10px;
 border: 1.5px solid #000;
 border-radius: 20%;

 &::after {
  position: absolute;
  content: '';
  background: #000;
  border-radius: 100%;
  width: 15px;
  height: 15px;
  left: 2.6px;
  top: 2.6px;
 }

 &.active {
  border: 1.5px solid rgb(220, 35, 35);

  &::after {
   background: rgb(230, 1, 1);
  }
 }
}
</style>