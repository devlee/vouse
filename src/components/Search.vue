<template>
  <div>
    <Input v-model="key" placeholder="Search" style="width: 100%"></Input>
    <ul>
      <li v-for="item in filterList" :key="item">
        <div class="comp" draggable="true" @dragstart="dragstart($event)" :data-name="item">Search Component {{ item }}</div>
      </li>
    </ul>
  </div>
</template>

<script>
import bus from './bus'

export default {
  data () {
    return {
      key: '',
      list: ['Hello', 2, 3],
      target: null
    }
  },
  computed: {
    filterList () {
      return this.list.filter(item => String(item).indexOf(this.key) > -1)
    }
  },
  methods: {
    dragstart (e) {
      bus.$emit('dragstart', e.target.dataset.name)
    }
  }
}
</script>

<style>
.comp {
  height: 30px;
  background-color: #f1f1f1;
}
</style>


