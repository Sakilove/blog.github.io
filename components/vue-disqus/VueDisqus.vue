<template>
  <div id="disqus_thread"></div>
</template>

<script>
  export default {
    name: 'vue-disqus',
    props: {
      shortname: {
        type: String,
        required: true
      },
      identifier: {
        type: String,
        required: false
      },
      url: {
        type: String,
        required: false
      },
      title: {
       type: String,
       required: false
      },
      remote_auth_s3: {
       type: String,
       required: false
      },
      api_key: {
       type: String,
       required: false
      },
      sso_config: {
        type: Object,
        required: false
      },
      language: {
        type: String,
        required: false
      }
    },
    mounted () {
      if (window.DISQUS) {
        this.reset(window.DISQUS)
        return
      }
      this.init()
    },
    beforeDestroy() {
      this.$emit('destroy')
    },
    methods: {
      reset (dsq) {
        const self = this
        dsq.reset({
          reload: true,
          config: function () {
            self.setBaseConfig(this)
          }
        })
      },
      errload(event){
        this.$emit('fail')
      },
      init () {
        const self = this
        window.disqus_config = function() {
          self.setBaseConfig(this)
        }
        setTimeout(() => {
          let d = document
            , s = d.createElement('script')
          s.setAttribute('id', 'embed-disqus')
          s.setAttribute('data-timestamp', +new Date())
          s.type = 'text/javascript'
          s.async = true
          s.src = `//${this.shortname}.disqus.com/embed.js`
          ;(d.head || d.body).appendChild(s)
          s.addEventListener('error', self.errload,false);
        }, 0)

      },
      setBaseConfig (disqusConfig){
        disqusConfig.page.identifier = (this.identifier || this.$route.path || window.location.pathname)
        disqusConfig.page.url = (this.url || this.$el.baseURI)
        if (this.title){
          disqusConfig.page.title = this.title;
        }
        if (this.remote_auth_s3){
          disqusConfig.page.remote_auth_s3 = this.remote_auth_s3;
        }
        if (this.key){
          disqusConfig.page.api_key = this.key;
        }
        if (this.sso_config){
          disqusConfig.sso = this.sso_config;
        }
        if (this.language){
          disqusConfig.language = this.language;
        }
        console.log(disqusConfig)
        disqusConfig.callbacks.onReady = [() => {
          this.$emit('ready')
          console.log('preData')
        }]
      }
    }
  }
</script>
