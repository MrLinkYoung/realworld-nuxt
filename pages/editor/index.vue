<template>
  <div class="editor-page">
    <div class="container page">
    <div class="row">
      <div class="col-md-10 offset-md-1 col-xs-12">
        <form @submit.prevent="onSubmit">
            <fieldset class="form-group">
                <input type="text" class="form-control form-control-lg" placeholder="Article Title" v-model="article.title" required minlength="1">
            </fieldset>
            <fieldset class="form-group">
                <input type="text" class="form-control" placeholder="What's this article about?" v-model="article.description" required minlength="1">
            </fieldset>
            <fieldset class="form-group">
                <textarea class="form-control" rows="8" placeholder="Write your article (in markdown)" v-model="article.body" required></textarea>
            </fieldset>
            <fieldset class="form-group">
                <input type="text" class="form-control" placeholder="Enter tags"><div class="tag-list"></div>
            </fieldset>
            <button 
            class="btn btn-lg pull-xs-right btn-primary" 
            >
                Publish Article
            </button>
        </form>
      </div>
    </div>
  </div>
</div>
</template>
<script>
import { createArticle, updateArticle, getArticle } from '@/api/article.js'
export default {
  //在路由匹配组件渲染之前会先执行中间件
  middleware: 'authenticated',
  name: 'EditorIndex',
  data () {
    return {
      article: {
        title:'',
        description: "",
        body:"",
        tagList: []
      }
    }
  },
  async mounted() {
    if(this.$route.params.slug){
      const { data } = await getArticle(this.$route.params.slug)
      this.article = data.article
    }
  },
  methods: {
    async onSubmit(){
      const RequestFn = this.$route.params.slug ? updateArticle : createArticle;
      try {
        const { data } = await RequestFn({article : this.article})
        const article = data.article
        this.$router.push({ path: `/article/${ article.slug }` , param: article.author})
      }catch(error){
        console.log(error)
      }
      
    }
  },
}
</script>