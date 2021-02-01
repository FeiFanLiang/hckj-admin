<template>
  <el-image
    style="width: 100px; height: 100px"
    :src="url"
    :preview-src-list="srcList"
  />
</template>
<script>
export default {
  props: {
    url: String
  },
  data() {
    return {

    }
  },
  computed: {
    srcList() {
      return [this.url]
    }
  }
}
</script>
