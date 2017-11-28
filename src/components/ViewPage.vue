<template>
  <div @drop="drop($event)" @dragover='dragover($event)' ref="dropTarget">
    <h2>Drap And Drop Component Here</h2>
    <div class="page">
      <Motion :values="getMotionValue(item.idx, index)" :spring="spring" v-for="(item, index) in list" :key="item.idx">
        <template slot-scope="mv">
          <div
            :class="{active: currentActiveIndex === index}"
            class="compWrapper"
            @click="clickCompWrapper(index)"
            :style="getStyle(mv, item.idx, index)"
            @mousedown="mouseDownCompWrapper($event, item.idx, mv.y)"
          >
            <component v-bind:is="item.name" v-bind="item.data" />
          </div>
        </template>
      </Motion>
    </div>
  </div>
</template>

<script>
import bus from './bus'

function reinsert (arr, from, to) {
  const result = arr.slice(0)
  const val = result[from]
  result.splice(from, 1)
  result.splice(to, 0, val)
  return result
}

function clamp (n, min, max) {
  return Math.max(Math.min(n, max), min)
}

function getIndex (arr, i) {
  let idx = 0
  arr.map((item, index) => {
    if (item.idx === i) {
      idx = index
    }
    return item
  })
  return idx
}

export default {
  data () {
    return {
      target: '',
      list: [],
      currentActiveIndex: -1,
      cIndex: -1,
      dragType: 'add',
      spring: {
        stiffness: 100,
        damping: 20
      },
      isPressed: false,
      lastPressedIdx: 0,
      mouseY: 0,
      disY: 0
    }
  },
  methods: {
    drop (e) {
      import(`./${this.target}.vue`)
        .then(comp => {
          this.cIndex++
          const name = `${this.target}${this.cIndex}`
          this.$options.components[name] = comp.default
          this.list.push({
            name,
            data: {},
            idx: this.list.length
          })
        })
      // this.$refs.dropTarget.appendChild(this.target)
    },
    dragover (e) {
      e.preventDefault()
    },
    clickCompWrapper (idx) {
      this.currentActiveIndex = idx
      bus.$emit('editcomp', {
        idx,
        comp: this.$options.components[this.list[idx].name],
        data: this.list[idx].data
      })
    },
    getMotionValue (i, idx) {
      const target = this.isPressed && (this.lastPressedIdx === i)
        ? {
          scale: 1.05,
          shadow: 4,
          y: this.mouseY
        } : {
          scale: 1,
          shadow: 0,
          y: getIndex(this.list, i) * 120
        }
      return target
    },
    getStyle ({ scale, shadow, y }, i, idx) {
      const count = this.list.length
      const style = {
        boxShadow: `rgba(0, 0, 0, 0.2) 0px ${shadow}px ${2 * shadow}px 0px`,
        transform: `translate3d(0, ${y}px, 0) scale(${scale})`,
        WebkitTransform: `translate3d(0, ${y}px, 0) scale(${scale})`,
        zIndex: i === this.lastPressedIdx ? count : i
      }

      if (shadow > 0.5) {
        style.borderRadius = '2px'
      } else if (idx === 0) {
        style.borderTopLeftRadius = '2px'
        style.borderTopRightRadius = '2px'
      } else if (idx === (count - 1)) {
        style.borderBottomLeftRadius = '2px'
        style.borderBottomRightRadius = '2px'
      } else {
        style.borderRadius = '0'
      }
      return style
    },
    mouseDownCompWrapper ({ pageY }, pressIdx, pressY) {
      this.disY = pageY - pressY
      this.mouseY = pressY
      this.isPressed = true
      this.lastPressedIdx = pressIdx
    },
    handleMouseMove ({ pageY }) {
      const count = this.list.length
      const range = this.list
      if (this.isPressed) {
        const mouseY = pageY - this.disY
        const currentRow = clamp(Math.round(mouseY / 120), 0, count - 1)
        if (currentRow !== getIndex(this.list, this.lastPressedIdx)) {
          const newRange = reinsert(range, getIndex(this.list, this.lastPressedIdx), currentRow)
          this.mouseY = mouseY
          this.list = newRange
        } else {
          this.mouseY = mouseY
        }
      }
    },
    handleMouseUp ({ pageY }) {
      this.isPressed = false
      this.disY = 0
    }
  },
  created () {
    bus.$on('dragstart', target => {
      this.target = target
    })
    bus.$on('update', data => {
      console.log(data)
      this.list = [].concat(this.list.map((item, idx) => {
        console.log(item)
        if (idx === data.idx) {
          item.data = data.data
        }
        return item
      }))
      console.log(this.list)
    })
    window.addEventListener('mousemove', this.handleMouseMove)
    window.addEventListener('mouseup', this.handleMouseUp)
  }
}
</script>

<style lang="less">
.page {
  position: relative;
}
.compWrapper {
  display: inline-block;
  width: 240px;
  height: 120px;
  background-color: #00bcd4;
  box-shadow: 0 1px 1px rgba(0, 0, 0, 0.12), 0 1px 1px rgba(0, 0, 0, 0.12);

  position: absolute;

  transform-origin: 50% 50% 0px;

  box-sizing: border-box;

  font-size: 24px;

  text-align: center;

  line-height: 120px;

  color: white;

  user-select: none;
  &.active {
    border: 1px solid red;
  }
}
</style>
