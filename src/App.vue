<template>
  <v-app dark>
    <v-app-bar class="app-bar" app color="primary" dark>
      <v-icon class="pr-2">mdi-video-box</v-icon>

      <v-toolbar-title class="font-weight-bold">
        RTMP Restreamer
      </v-toolbar-title>

      <v-spacer></v-spacer>

      <span class="pr-4 caption" style="max-width: 250px">
        <v-progress-circular v-if="$store.state.loading && !$store.state.error"
                             size="12" width="1" class="mb-1 mr-1" indeterminate />
        <span v-if="$store.state.error" class="error--text">{{$store.state.error}}</span>
        <span v-else>{{$store.state.log[$store.state.log.length - 1]}}</span>
      </span>

      <ControlPanel />

      <v-btn class="ml-2 mr-1" small icon @click="close">
        <v-icon>mdi-window-close</v-icon>
      </v-btn>
    </v-app-bar>

    <v-content>
      <StreamDestinations class="pb-0" />
      <StreamInput />
      <SystemLog v-if="$store.state.logVisible" />
    </v-content>

    <v-footer app>
      <div class="footer-text" @dblclick="$store.commit('toggleLog')">v{{version}}</div>
      <v-spacer></v-spacer>
      <div class="footer-text">&copy; Vitaly Emelyanov, {{ new Date().getFullYear() }}</div>
    </v-footer>
  </v-app>
</template>

<script>
import StreamInput from './components/StreamInput';
import StreamDestinations from './components/StreamDestinations';
import SystemLog from './components/SystemLog';
import ControlPanel from './components/ControlPanel';
import {remote} from 'electron';

export default {
  name: 'App',

  components: {
    ControlPanel,
    StreamInput,
    SystemLog,
    StreamDestinations,
  },

  mounted() {
    this.$store.dispatch('init')
    this.$store.dispatch('stats/initStats', this.version)
  },

  computed: {
    version() {
      return remote.app.getVersion()
    }
  },

  methods: {
    minimize() {
      remote.getCurrentWindow().minimize()
    },
    async close() {
      await this.$store.dispatch('stop').catch(() => {})
      remote.getCurrentWindow().close()
    }
  }
};
</script>

<style lang="sass">
.app-bar
  -webkit-app-region: drag

  button
    -webkit-app-region: no-drag

*, *::after, *::before
  -webkit-user-select: none
  -webkit-user-drag: none
  cursor: default

.footer-text
  font-size: 12px
  color: rgba(255, 255, 255, 0.4)

input
  cursor: text
</style>
