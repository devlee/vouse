<template>
  <div>
    <Row>
      <Col span="8"><Search /></Col>
      <Col span="8"><ViewPage /></Col>
      <Col span="8"><Edit /></Col>
    </Row>
  </div>
</template>

<script>
import Search from '@/components/Search.vue'
import ViewPage from '@/components/ViewPage.vue'
import Edit from '@/components/Edit.vue'

export default {
  components: {
    Search,
    ViewPage,
    Edit
  }
}
</script>
