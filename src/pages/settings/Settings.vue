<template>
  <page-container :show-bottom-nav="false">
    <template slot="toolbar">
      <v-btn icon @click="$router.go(-1)">
        <v-icon>fa-arrow-left</v-icon>
      </v-btn>
      <v-toolbar-title>设置</v-toolbar-title>
    </template>
    <v-list>
      <v-list-tile to="/settings/tabs">
        <v-list-tile-action>
          <v-icon>fa-th-large</v-icon>
        </v-list-tile-action>
        <v-list-tile-content>首页板块</v-list-tile-content>
      </v-list-tile>
      <v-list-tile @click="clearCacheSheet = true">
        <v-list-tile-action>
          <v-icon>fa-hdd</v-icon>
        </v-list-tile-action>
        <v-list-tile-content>清除缓存</v-list-tile-content>
      </v-list-tile>
      <v-list-tile v-if="accessToken" @click="logoutSheet = true">
        <v-list-tile-action>
          <v-icon>fa-sign-out-alt</v-icon>
        </v-list-tile-action>
        <v-list-tile-content>退出</v-list-tile-content>
      </v-list-tile>
      <v-list-tile to="/about">
        <v-list-tile-action>
          <v-icon>fa-info-circle</v-icon>
        </v-list-tile-action>
        <v-list-tile-content>关于</v-list-tile-content>
      </v-list-tile>
    </v-list>

    <v-bottom-sheet v-model="clearCacheSheet">
      <v-list>
        <v-subheader>清除缓存</v-subheader>
        <v-list-tile v-if="accessToken" @click="clearCache(false)">
          <v-list-tile-title>保留登录数据</v-list-tile-title>
        </v-list-tile>
        <v-list-tile @click="clearCache(true)">
          <v-list-tile-title>全部清除</v-list-tile-title>
        </v-list-tile>
      </v-list>
    </v-bottom-sheet>

    <v-bottom-sheet v-model="logoutSheet">
      <v-list>
        <v-subheader>确定退出登录</v-subheader>
        <v-list-tile @click="logout" color="red">
          <v-list-tile-action>
            <v-icon color="red">fa-sign-out-alt</v-icon>
          </v-list-tile-action>
          <v-list-tile-title>退出登录</v-list-tile-title>
        </v-list-tile>
      </v-list>
    </v-bottom-sheet>

    <v-dialog 
      fullscreen
      hide-overlay
      transition="dialog-bottom-transition"
      v-model="chooseTabs">
      <v-card flat>
        <settings-tabs></settings-tabs>
      </v-card>
    </v-dialog>
  </page-container>
</template>

<script>
import SettingsTabs from './SettingsTabs'

export default {
  props: {
    chooseTabs: {
      default: false,
      type: Boolean
    }
  },
  data () {
    return {
      accessToken: null,
      clearCacheSheet: false,
      logoutSheet: false
    }
  },
  components: {
    SettingsTabs
  },
  created () {
    this.accessToken = this.$localStorage.get('accessToken')
  },
  methods: {
    logout () {
      this.logoutSheet = false
      this.accessToken = null
      this.$localStorage.remove('accessToken')
      this.$localStorage.remove('loginUserId')
      this.$localStorage.remove('loginUser')

      this.$router.replace('/')
    },
    clearCache (clearAll) {
      this.clearCacheSheet = false
      if (clearAll) {
        this.accessToken = null
        window.localStorage.clear()
      } else {
        let token = this.$localStorage.get('accessToken')
        let id = this.$localStorage.get('loginUserId')
        let data = this.$localStorage.get('loginUser')
        window.localStorage.clear()
        this.$localStorage.set('accessToken', token)
        this.$localStorage.set('loginUserId', id)
        this.$localStorage.set('loginUser', data)
      }
    }
  }
}
</script>

