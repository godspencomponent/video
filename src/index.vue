<template>
  <div class="component">
    <div v-if="type === 'code'" class="content" v-html="videoContent"></div>
    <video
      v-if="type === 'url'"
      :controls="controls"
      :loop="loop"
      preload="auto"
      ref="video"
      :autoplay="autoplay"
      :poster="poster"
      :src="src"
      @error="vedioErr"
    >您的浏览器不支持播放该视频。</video>
  </div>
</template>

<script>
  import {VueExtend} from 'godspen-lib'

  export default {
    mixins: [VueExtend.mixin],
    name: 'video',
    label: process.env.LABEL,
    style: process.env.STYLE,
    props: {
      type: {
        type: String,
        default: 'code',
        editer: {
          label: '数据源',
          type: 'enum',
          defaultList: {
            code: '视频网站代码',
            url: 'url或本地上传'
          }
        }
      },
      videoContent: {
        type: String,
        default: '',
        editer: {
          type: 'text',
          label: '视频文件代码',
          work: function () {
            return this.type == 'code'
          }
        }
      },
      src: {
        type: String,
        default: '',
        editer: {
          type: 'video',
          label: '视频路径',
          desc: '目前只支持.mp4格式的视频',
          work: function () {
            return this.type == 'url'
          }
        }
      },
      poster: {
        type: String,
        default: '',
        editer: {
          type: 'image',
          label: '封面图',
          work: function () {
            return this.type == 'url'
          }
        }
      },
      controls: {
        type: Boolean,
        default: true,
        editer: {
          label: '显示播放控件',
          type: 'boolean',
          work: function () {
            return this.type == 'url'
          }
        }
      },
      autoplay: {
        type: Boolean,
        default: false,
        editer: {
          label: '自动播放',
          type: 'boolean',
          desc: '捕获用户首次触摸操作以后才会真正自动播放',
          work: function () {
            return this.type == 'url'
          }
        }
      },
      loop: {
        type: Boolean,
        default: false,
        editer: {
          label: '循环播放',
          type: 'boolean',
          work: function () {
            return this.type == 'url'
          }
        }
      },
      errFn: {
        type: [Function, Array],
        default: function () {
          return []
        },
        editer: {
          label: '视频出错回调',
          type: 'function',
          work: function () {
            return this.type == 'url'
          }
        }
      }
    },
    data () {
      return {
        meidiaErr: false,
        playing: false
      }
    },
    mounted () {
      this.initVideo()
    },
    methods: {
      vedioErr (e) {
        this.meidiaErr = true
        this.oncallExecute(this.errFn, [e])
      },
      initVideo: function () {
        let that = this
        if (!this.autoplay) return
        document.body.addEventListener('click', touchPlay)
        function touchPlay (e) {
          that.play()
          document.body.removeEventListener('click', touchPlay)
        }
      },
      tooglePlaying () {
        if (this.playing) this.pause()
        else this.play()
      },
      play (ts) {
        if (this.meidiaErr) return
        this.$refs.video.currentTime = ts === undefined || ts === null ? this.$refs.video.currentTime : ts
        this.$refs.video.play()
        this.playing = true
      },
      pause () {
        if (this.meidiaErr) return
        this.$refs.video.pause()
        this.playing = false
      },
      mute () {
        this.$refs.video.volume = 0
      },
      volume (vol = 1) {
        this.$refs.video.volume = vol
      },
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" type="text/stylus" scoped>
  .component {
    width: 100%;
    height: 100%;
    overflow: hidden;
    position: relative;

    .content {
      width: 100%;
      height: 100%;
      position: absolute;

      >>> iframe {
        position: absolute;
        width: 100%;
        height: 100%;
        max-height: 100%;
      }
    }

    video {
      position: absolute;
      width: 100%;
      height: 100%;
      max-height: 100%;
    }
  }
</style>
