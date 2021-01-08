<template>
  <p class="filter"></p>
</template>

<script>
  export default {
    created() {
      var listener = createCopyListener('zhoujiang1', '123456')
      listener.addRule('className', 'IDName')
      listener.connect().then(transfer => {
        // store transfer
      })
    }
  }
</script>

<style lang='stylus'>
  body {
    font-family: 'longhu-font' "Microsoft YaHei", "Helvetica Neue", Helvetica, STHeiTi, Arial, sans-serif
  }
</style>