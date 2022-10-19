<template>

 <div class="building">
  <div class="building__elevator"
  :class="{'rest': elevator__data.rest == true}"
  :style="{'transform': `translateY(calc(${elevator__data.goto} * -150px))`,
  'transition': `transform ${elevatorTransi}s linear`}"
  >
   <div
    class="building__elevator_floor"
    :class="{'active': elevator__data.goto != elevator__data.floor}"
    >
    <img :class="{'rotate': elevator__data.goto > elevator__data.floor}" src="../../public/up-arrow-svgrepo-com.svg" alt="">
   {{ elevatorFloor }}
   </div>
  </div>
 </div>

</template>

<script>
export default {

 props: {
  elevator__data: {
   type: Object,
   default() {
    return {}
   }
  },
 },

 methods: {
  elevatorTransiFunc() {
   return Math.abs(this.elevator__data.goto - this.elevator__data.floor)
  },
 },

 computed: {
  elevatorTransi() {
   return Math.abs(this.elevator__data.goto - this.elevator__data.floor)
  },

  elevatorFloor() {
   if (this.elevator__data.moving == 0) {
    return 1
   } else {
    return this.elevator__data.moving
   }
  },
 },

 mounted() {
  this.elevatorTransiFunc()
 },

}
</script>

<style lang="scss">
.building {
 position: relative;
 width: 150px;
 height: 100%;
 border-left: 2px solid rgba(0, 0, 0, 0.899);
 border-right: 2px solid rgba(0, 0, 0, 0.899);
}

.building__elevator {
 position: absolute;
 display: flex;
 align-items: start;
 justify-content: center;
 width: 147px;
 height: 150px;
 left: 0;
 bottom: 0;
 background: rgb(10, 199, 199);
 transform: translateY(0);

 &.rest {
  animation: tic 1s infinite linear;
 }
}

.building__elevator_floor {
 display: none;
 width: fit-content;
 background-color: rgba(232, 232, 232, 0.714);
 font-size: 20px;
 padding: 4px 8px;

 &.active {
  display: block;
 }

 img {
  width: 20px;
  height: 20px;
  transform: rotate(180deg);

  &.rotate {
   transform: rotate(0);
  }
 }
}

@keyframes tic {
 0% {
  opacity: 20%;
 }

 50% {
  opacity: 1;
 }

 100% {
  opacity: 20%;
 }
}
</style>