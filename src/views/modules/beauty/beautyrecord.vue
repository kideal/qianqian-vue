<template>
  <div class="mod-config">
    <div class="container">
      <p style="color:red;">Num：{{ key }}</p>
    </div>

    <div class="demo-image__placeholder">
      <div class="block">
        <span class="demonstration">默认</span>
        <el-image :src="src"></el-image>
      </div>
      <div class="block">
        <span class="demonstration">自定义</span>
        <el-image :src="src">
          <div slot="placeholder" class="image-slot">
            加载中<span class="dot">...</span>
          </div>
        </el-image>
      </div>
    </div>
  </div>


</template>
<script src="//unpkg.com/vue/dist/vue.js"></script>
<script src="//unpkg.com/element-ui@2.8.2/lib/index.js"></script>
<script>
  export default {
    data () {
      return {

        // 保存传递过来的index
        key: '',
        url: '',
        urls: [],
        src: ''
      }
    },
    components: {

    },
    activated () {

    },
    methods: {
      getData () {
        this.$http({
          url: this.$http.adornUrl('/beautyproject/info/'+this.key),
          method: 'get'
        }).then(({data}) => {
          if (data && data.code === 0) {
              this.src = 'https://cube.elemecdn.com/6/94/4d3ea53c084bad6931a56d5158a48jpeg.jpeg'
          } else {

          }
          this.dataListLoading = false
        })
      }
    },
    beforeMount:
      function () {
        this.key = this.$route.params.key
        this.$route.params.key = ''
        console.log(this.key)
        this.getData ()
      }

  }
</script>
