<template>
<div class="columns is-gapless gap">
  <div class="column">
  </div>
  <div class="column is-full-mobile is-11-tablet is-10-desktop is-three-fifths-widescreen is-7-fullhd">
    <p v-if="cacheAge">数据缓存于: <span class="tag is-rounded is-info smallMargin">{{cacheAge}}</span></p>
    <progress class="progress" max="100" v-if="!currentVtbs.length"></progress>
    <transition-group name="flip-list">
      <card v-for="vtb in rankLimit" :vtb="vtb" hover :key="vtb.mid" class="card"></card>
    </transition-group>
  </div>
  <div class="column"></div>
</div>
</template>

<script>
import { mapState, mapGetters } from 'vuex'
import card from '@/components/card'
import moment from 'moment'

export default {
  name: 'home',
  data() {
    return {
      show: 32,
    }
  },
  components: {
    card,
  },
  methods: {},
  mounted() {
    this.$nextTick(function() {
      document.onscroll = () => {
        if (document.body.clientHeight - window.scrollY - window.innerHeight < (document.body.clientHeight / this.show * 20) && this.vtbs.length) {
          if (!this.allDisplay) {
            this.show += 15
          } else {
            document.onscroll = null
          }
        }
      }
    })
  },
  destroyed() {
    document.onscroll = null
  },
  computed: {
    ...mapState(['currentVtbs', 'cachedTime']),
    ...mapGetters(['vtbs', 'followerRank', 'liveRank', 'riseRank']),
    cacheAge() {
      if (!this.currentVtbs.length && this.cachedTime) {
        return moment(this.cachedTime).fromNow()
      } else {
        return false
      }
    },
    rank: function() {
      if (this.$route.path.includes('live')) {
        return this.liveRank
      }
      if (this.$route.path.includes('rise')) {
        return this.riseRank
      }
      if (this.$route.path.includes('drop')) {
        return [...this.riseRank].reverse()
      }
      return this.followerRank
    },
    rankLimit: function() {
      return this.rank
        .filter((info, index) => index < this.show)
    },
    allDisplay() {
      return this.show >= this.vtbs.length
    },
  },
}
</script>

<style scoped>
.smallMargin {
  margin-bottom: 6px;
}

.flip-list-move {
  transition: transform 0.5s;
}

.flip-list-enter {
  opacity: 0;
}

.flip-list-enter-active {
  transition: opacity 0.5s;
}

.flip-list-leave {
  display: none;
}

.flip-list-leave-active {
  display: none;
}

.card {
  margin-bottom: 32px;
}

.gap {
  padding: 16px;
  padding-left: 32px;
  padding-right: 32px;
}
</style>
