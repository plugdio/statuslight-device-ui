<template>
  <p>
  <span v-if="!isHttp">
    <div class="notification is-warning" >
      This site is served via secure TSL/SSL. Unfortunately this prevents your browser from detecting your device.
      Please use the following link to setup your device: <br/> <a v-bind:href="httpUrl"> {{ httpUrl }}</a>
    </div>
  </span>
    Connect to your device Wi-Fi AP. It should be named something like <strong>SL-123456abcdef</strong>.
  </p>
</template>

<script>
import axios from 'axios'
import {API_URL, HEARTBEAT_ATTEMPT_INTERVAL} from '../../constants'

export default {
  data () {
    return {
      requestOnGoing: false,
      interval: null,
      isHttp: this.isHttpFunc(),
      httpUrl: this.getHttpUrl()
    }
  },
  mounted () {
    this.$emit('loading', 'Waiting for the device...')
    this.interval = setInterval(this.sendRequest, HEARTBEAT_ATTEMPT_INTERVAL)
    this.isHttp = this.isHttpFunc();

  },
  methods: {
    isHttpFunc: function() {
      return window.location.protocol === 'http:';
    },
    getHttpUrl: function() {
      var url = document.createElement('a');
      url.href = window.location.href;
      url.protocol = 'http:';
      return url.href;
    },

    sendRequest: function () {
      if (this.requestOnGoing) return

      this.requestOnGoing = true
      axios.get(`${API_URL}/heart`).then((res) => {
        clearInterval(this.interval)
        this.$emit('loaded')
        this.$emit('done')
      }).catch(() => {
        this.requestOnGoing = false
      })
    }
  }
}
</script>
