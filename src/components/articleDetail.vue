<template>
  <div id="content">
    <div class="article_wrap">
      <div class="article_title article_detail_title">{{ article.title }}</div>
      <div class="article_info">
        <span class="article_info_date">发表于：{{ article.date }}</span>
        <span class="article_info_label">标签：
        <!-- <span v-if="article.labels.length === 0">未分类</span> -->
        <!-- <el-tag v-else class="tag_margin" type="primary" v-for="tag in article.labels">{{ tag }}</el-tag> -->
        </span>
      </div>
      <div>
        <input type="text" placeholder="请输入编码" v-model="inputCode"/>
        <button type="button" @click="updateText">转换</button>
      </div>
      <div>
        <el-radio-group v-model="radio1" @change="changeFonts">
          <el-radio-button label="MicrosoftYahei">微软雅黑-138</el-radio-button>
          <el-radio-button label="towMicrosoftYahei">微软雅黑-240</el-radio-button>
          <el-radio-button label="PingfangSC">苹方-138</el-radio-button>
          <el-radio-button label="towPingfangSC">苹方-240</el-radio-button>
        </el-radio-group>
      </div>
      <div :class="[
        'article_detail_content',
        'maia_default_copy_listener-' + radio1
      ]" v-html="articleData"></div>
    </div>
  </div>
</template>

<script>
  import marked from 'marked'
  import highlight from 'highlight.js'
  import '../assets/atom-one-light.css'
  import Vue from 'vue'
  marked.setOptions({
    highlight: function (code) {
      return highlight.highlightAuto(code).value
    }
  })
  export default {
    name: 'articleDetail',
    data() {
      return {
        article: {},
        articleData: '',
        inputCode: '',
        radio1: 'MicrosoftYahei'
      }
    },
    mounted: function () {
      let id = this.$route.params.id
      this.$http.get('/api/articleDetail/' + id).then((response) => {
        this.article = response.body
        this.changeFonts()
        this.compiledMarkdown()
      })
      // this.compiledMarkdown()
    },
    methods: {
      compiledMarkdown: function () {
          this.$copy.connect().then(res => {
            this.articleData = res.transform(marked(this.article.content || '', {sanitize: true}))
          })
      },
      updateText(){
        if(!this.inputCode){
          return
        }
        const user_name = this.getUrlParam('username') || 'defaultUser'
        this.$copy.remove()
        window.createCopy(user_name, 'd2eef18732a241c192edb08e3b8494be', {
          fontName: this.radio1,
          customCode: this.inputCode
        }, Vue, null, true)
        this.compiledMarkdown() 
      },
      changeFonts(val){
        const user_name = this.getUrlParam('username') || 'defaultUser'
        this.$copy.remove()
        window.createCopy(user_name, 'd2eef18732a241c192edb08e3b8494be', {
          fontName: val || this.radio1
        }, Vue, null, true)
        this.compiledMarkdown()
      },
      getUrlParam(name) {
        //构造一个含有目标参数的正则表达式对象
        var reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)");
        //匹配目标参数
        var r = window.location.search.substr(1).match(reg);
        //返回参数
        if (r != null) {
            return unescape(r[2]);
        } else {
            return null;
        }
      }
    }
  }
</script>

<style>
  .article_detail_title {
    cursor: default;
    margin: 40px 0 0;
  }

  .article_detail_content {
    text-align: left;
    padding: 60px 0;
    font-size: 14px;
  }
</style>