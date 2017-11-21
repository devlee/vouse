<template>
  <div>
    <h2>edit components</h2>
    <ul>
      <li v-for="(item, index) in props" :key="index">
        <label :for="`label${index}`">{{item}}</label>
        <input type="text" :id="`label${index}`" v-model="data[item]" @keyup="change">
      </li>
    </ul>
  </div>
  
</template>

<script>
import bus from './bus'

export default {
  data () {
    return {
      comp: null,
      props: [],
      data: {},
      idx: -1
    }
  },
  created () {
    bus.$on('editcomp', data => {
      this.idx = data.idx
      this.comp = data.comp
      if (data.comp.props) {
        Object.keys(data.comp.props).map(item => {
          this.props.push(item)
          this.data[item] = ''
        })
      }
    })
  },
  methods: {
    change () {
      bus.$emit('update', {
        idx: this.idx,
        data: this.data
      })
    }
  }
}
</script>
