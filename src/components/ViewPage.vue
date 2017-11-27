<template>
  <div @drop="drop($event)" @dragover='dragover($event)' ref="dropTarget">
    <h2>Drap And Drop Component Here</h2>
    <ul>
      <li
        v-for="(item, index) in list"
        :key="index"
        :class="{active: currentActiveIndex === index}"
        class="compWrapper"
        @click="clickCompWrapper(index)">
        <component v-bind:is="item.name" v-bind="item.data" />
      </li>
    </ul>
  </div>
</template>

<script>
import bus from './bus'

export default {
  data () {
    return {
      target: '',
      list: [],
      currentActiveIndex: -1,
      cIndex: -1
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
            data: {}
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
  }
}
</script>

<style lang="less">
.compWrapper {
  &.active {
    border: 1px solid red;
  }
}
</style>
