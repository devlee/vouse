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
      this.props = []
      this.idx = data.idx
      this.comp = data.comp
      if (data.comp.props) {
        Object.keys(data.comp.props).map(item => {
          this.props.push(item)
          this.data[item] = data.data[item] || ''
        })
      }
    })
  },
  methods: {
    change () {
      console.log(this.idx)
      bus.$emit('update', {
        idx: JSON.parse(JSON.stringify(this.idx)),
        data: JSON.parse(JSON.stringify(this.data))
      })
    }
  }
}
</script>
