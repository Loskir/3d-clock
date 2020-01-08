<template>
  <div id="app">
    <div class="svg-wrapper">
      <svg class="svg" viewBox="-10 -10 1020 1020">
        <circle :r="clockRadius" :cx="clockRadius" :cy="clockRadius" class="clock"/>

        <g class="secondary">
          <VectorLine :vector="new Vector(secondPoint, secondPoint.add(minutePoint))"/>
          <VectorLine :vector="new Vector(secondPoint, secondPoint.add(hourPoint))"/>
          <VectorLine :vector="new Vector(minutePoint, minutePoint.add(secondPoint))"/>
          <VectorLine :vector="new Vector(minutePoint, minutePoint.add(hourPoint))"/>
          <VectorLine :vector="new Vector(hourPoint, hourPoint.add(secondPoint))"/>
          <VectorLine :vector="new Vector(hourPoint, hourPoint.add(minutePoint))"/>
          <VectorLine :vector="new Vector(secondPoint.add(minutePoint), secondPoint.add(minutePoint).add(hourPoint))"/>
          <VectorLine :vector="new Vector(secondPoint.add(hourPoint), secondPoint.add(minutePoint).add(hourPoint))"/>
          <VectorLine :vector="new Vector(minutePoint.add(hourPoint), secondPoint.add(minutePoint).add(hourPoint))"/>
        </g>
        <g class="primary">
          <VectorLine :vector="new Vector(new Point(0, 0), this.secondPoint)"/>
          <VectorLine :vector="new Vector(new Point(0, 0), this.minutePoint)"/>
          <VectorLine :vector="new Vector(new Point(0, 0), this.hourPoint)"/>
        </g>
        <circle class="center" r="7" cx="500" cy="500"/>
      </svg>
    </div>
    <footer class="footer">
      <span>Made with ❤️ by <a href="https://loskir.ru" target="_blank">@Loskir</a></span>
      <span><a href="https://github.com/Loskir/3d-clock" target="_blank">GitHub</a></span>
    </footer>
  </div>
</template>

<script>
  import VectorLine from '@/components/VectorLine.vue'
  import PointCircle from '@/components/Point.vue'

  class Point {
    constructor(x, y) {
      this.x = x
      this.y = y
    }
    add(point) {
      return new Point(this.x+point.x, this.y+point.y)
    }
  }

  class Vector {
    constructor(start, end) {
      this.start = start
      this.end = end
    }
  }

  export default {
    name: 'app',
    components: {
      VectorLine,
      PointCircle,
    },
    data() {
      return {
        clockRadius: 500,
        secondRadius: 250,
        minuteRadius: 170,
        hourRadius: 80,
        centerRadius: 100,
        pointLength: 100,
        slowAnimationDuration: 2000,
        fastAnimationDuration: 500,

        ms: 0,

        debugDrawings: false,

        timeTimeout: undefined,

        devMenuOpened: false,

        Point,
        Vector,
      }
    },
    methods: {
      startInterval() {
        clearTimeout(this.timeTimeout)
        if (!this.devMenuOpened) {
          this.updateTime()
        }
      },
      updateTime() {
        if (!this.devMenuOpened) {
          this.ms = Date.now() % 86400000 - new Date().getTimezoneOffset() * 60000
          console.log('time_update, ms', this.ms)

          requestAnimationFrame(this.updateTime)
        }
      },
      normalizePosition(pos) {
        let c = this.clockRadius
        return [c + pos[0], c - pos[1]]
      }
    },
    computed: {
      second() {
        return this.ms / 1000 % 60
      },
      minute() {
        return this.ms / 60000 % 60
      },
      hour() {
        return this.ms / 3600000 % 24
      },
      secondAngle() {
        return this.second / 30 * Math.PI
      },
      minuteAngle() {
        return this.minute / 30 * Math.PI
      },
      hourAngle() {
        return this.hour / 6 * Math.PI
      },
      secondPoint() {
        let a = this.secondAngle
        let r = this.secondRadius
        return new Point(r * Math.sin(a), r * Math.cos(a))
      },
      minutePoint() {
        let a = this.minuteAngle
        let r = this.minuteRadius
        return new Point(r * Math.sin(a), r * Math.cos(a))
      },
      hourPoint() {
        let a = this.hourAngle
        let r = this.hourRadius
        return new Point(r * Math.sin(a), r * Math.cos(a))
      },
      centerPosition() {
        let c = this.clockRadius
        return [c, c]
      },
    },
    watch: {
      devMenuOpened(val) {
        if (!val) {
          this.startInterval()
        }
      }
    },
    mounted() {
      this.updateTime()
//      setInterval(() => {
//        this.minute++
//      }, 1000)
    }
  }
</script>

<style lang="stylus">
  body {
    margin: 0;
  }

  #app
    font-family: monospace;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    height: 100vh;
    overflow: hidden;


  a {
    color: #007bff;
    text-decoration: none;
    transition: .2s;

    &:hover {
      color: #0056b3
    }
  }

  .svg-wrapper {
    padding: 100px 50px;
    width: 100%;
    height: 100%;
    box-sizing: border-box;
  }

  .svg
    max-width: 100%;
    max-height: 100%;

    .clock {
      fill: #e6e6e6
      stroke #464f60
      stroke-width 10
    }

    .center {
      fill: #363636;
    }

    .primary
      stroke #1f1f1f
      stroke-width 7
      stroke-linecap round

    .secondary
      stroke #999999
      stroke-width 1
      stroke-linecap round

  .footer {
    position: absolute;
    z-index: 100;
    width: 100%;
    bottom: 0;
    background: white;
    border-top: solid 1px #ced4da;
    padding: 20px 50px;
    box-sizing: border-box;
    display: flex;
    flex-direction: row;
    justify-content: space-between;
  }
</style>
