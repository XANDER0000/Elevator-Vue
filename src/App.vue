<template>
<div class="main">
  <div class="building__wrapper">
    <BuildingsElevators
      v-for="elevator in elevators"
      :elevator__data="elevator"
      :key="elevator.id"
    />
  </div>

  <div class="floors__wrapper">
    <div class="floor__list"
      v-for="floor in floorsBtns.length"
      :key="floor.id"
      >
    </div>
  </div>
  <div class="btn__wrapper">
    <FloorsBtns
      v-for="btn in floorsBtns.slice().reverse()"
      :btn__data="btn"
      :key="btn.id"
      @containsBtns = containsBtns
    />
  </div>



</div>
</template>

<script>
import FloorsBtns from './components/FloorsBtns.vue';
import BuildingsElevators from './components/BuildingsElevators.vue';


export default {
    name: "App",
    components: { FloorsBtns, BuildingsElevators },
    data: () => ({
      btnCount: 7,
      elevatorsCount: 5,

      floorsBtns: [],
      elevators: [],

      btnsWaiting: [],
      btnsWaitingStorage: [],
      filteredElevators: [],
  }),

  methods: {
    renderingArrays() {
      let btnData = {
        id: 1,
        floor: 1,
        number: 0,
        active: false,
        used: false
      };

      let elevatorData = {
        id: 1,
        active: false,
        rest: false,
        moving: 0,
        floor: 0,
        goto: 0,
      }

      for(let i = 0; i < this.btnCount; i++) {
        let newBtn = {
          id: btnData.id++,
          floor: btnData.floor++,
          number: btnData.number++,
          active: false,
          used: false,
        };
        this.floorsBtns.push(newBtn);
        this.btnData = newBtn
      }

      for(let i = 0; i < this.elevatorsCount; i++) {
        let newElevator = {
          id: elevatorData.id++,
          active: false,
          rest: false,
          moving: 0,
          floor: 0,
          goto: 0,
        };
        this.elevators.push(newElevator);
        this.elevatorData = newElevator
      }
    },

    containsBtns(btn) {
      for (let i = 0; i < this.btnsWaiting.length; i++) {
        if (this.btnsWaiting[i] == btn && this.btnsWaiting.length == 0) {
          return false
        }
      }

      this.btnsWaiting.push(btn)
      // this.btnsWaitingStorage.push(btn)
      // this.saveStorageBtns()
      this.filtElevatorsActive(btn)
    },

    filtElevatorsActive(btn) {
      this.filteredElevators = []
      btn.used = false
      btn.active = true
      let vm = this;

      this.elevators.map(function (item) {
        if (item.active !== true && item.rest !== true) {
          vm.filteredElevators.push(item)
        } else {
          return false
        }
      })
      this.gotoFloor(btn);
    },

    checkBtns() {
      let vm = this
      if (this.btnsWaiting.length != 0) {
        this.btnsWaiting.map(function (btn) {
          vm.filtElevatorsActive(btn)
        })
      }
    },

    gotoFloor(btn) {
      let e = this.floorsBtns.length;
      let closest = 0;

      if (this.filteredElevators.length > 0) {
        this.filteredElevators.map(function (item) {
          if(e > Math.abs((btn.floor - item.floor-1))) {
            e = Math.abs((btn.floor - item.floor-1))
            closest = item
          }
        })
      } else {
        return false
      }

      if (closest.moving == btn.floor) {
        btn.active = false
        this.btnsWaiting.shift()
        // this.btnsWaitingStorage.shift()
        return false
      } else if (closest.moving !== btn.floor) {
        this.btnsWaiting.shift()

        closest.goto = btn.number
        closest.moving = btn.floor
        closest.active = !closest.active
        btn.used = true
        btn.active = true


        setTimeout(() => {
          closest.active = !closest.active
          closest.rest = !closest.rest

        }, Math.abs(closest.goto - closest.floor) * 1000)

        setTimeout(() => {
          closest.rest = !closest.rest
          closest.floor = btn.number
          btn.used = false
          btn.active = false

          this.saveElevators(closest)
          this.checkBtns(closest)
          // this.btnsWaitingStorage.shift()
          // this.saveStorageBtns()

        }, Math.abs(closest.goto - closest.floor) * 1000 + 3000)
      }
    },

    saveElevators(closest) {
      const parsed = JSON.stringify(this.elevators);
      if (closest.active == true || closest.rest == true) {
        return false
      }

      localStorage.setItem('elevators', parsed);
    },

    saveBtns() {
      const parsed = JSON.stringify(this.btnsWaiting);
      localStorage.setItem('btnsWaiting', parsed);
    },

    saveStorageBtns() {
      const parsed = JSON.stringify(this.btnsWaitingStorage);
      localStorage.setItem('btnsWaitingStorage', parsed);
    }
  },

  mounted() {
    this.renderingArrays()
    if (localStorage.getItem('elevators')) {
      try {
        this.elevators = JSON.parse(localStorage.getItem('elevators'));

        this.elevators.map(function (item) {
          item.active = false
          item.rest = false
        })
      } catch(e) {
        localStorage.removeItem('elevators')
      }
    }

    // if (localStorage.getItem('btnsWaitingStorage')) {
    //   try {
    //     this.btnsWaiting = JSON.parse(localStorage.getItem('btnsWaitingStorage'));

    //   } catch(e) {
    //     localStorage.removeItem('btnsWaitingStorage')
    //   }
    // }

    this.checkBtns()
  },
}
</script>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@400;500;700&display=swap');


#app {
  font-family: 'Roboto', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 0;
  padding: 0;
}

*,
*:before,
*:after {
    box-sizing: border-box;
}

.main {
  display: flex;
  position: relative;
  background:  repeating-linear-gradient(
      transparent,transparent 150px, yellowgreen 150px
    );
}

.floor__list {
  height: 150px;
  border-top: 1px solid rgba(0, 0, 0, 0.389);

  &:last-child {
    border-bottom: 1px solid rgba(0, 0, 0, 0.389);
  }
}

.floors__wrapper {
  position: absolute;
  width: 100%;
  height: 100%;
  z-index: -1;
}

.building__wrapper {
  display: flex;
  gap: 20px;
  margin-right: 30px;
}
</style>
