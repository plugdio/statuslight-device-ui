<template>
  <span>
    <p>
      Enter the device PIN that you got after you've logged in at https://statuslight.online
    </p>

    <form @submit.prevent="sendDone">

      <label class="label" for="mqtt_password">Device PIN</label>
      <p class="control is-grouped">
        <!-- v-model does not support dynamic :type -->
        <input type="text" pattern="\d*" inputmode="numeric" class="input" v-model.trim="password" id="mqtt_password" placeholder="PIN" />
      </p>
      <span class="help">Required.</span>

        <br/>

      <p class="control">
        <button type="submit" :disabled="!formIsValid" class="button is-primary" style="float: right;">Next</button>
      </p>
    </form>
  </span>
</template>

<script>
import axios from 'axios'
import {API_URL} from '../../constants'
import {MQTT_HOST} from '../../constants'
import {MQTT_PORT} from '../../constants'
import {MQTT_BASE_TOPIC} from '../../constants'

export default {
  data () {
    return {
      passwordClearText: false,
      host: MQTT_HOST,
      port: MQTT_PORT,
      baseTopic: null,
      auth: true,
      username: this.deviceInformation.hardware_device_id,
      password: null
    }
  },
  props: ['deviceInformation'],
  computed: {
    formIsValid: function () {
      if (!this.password) return false

      return true
    }
  },
  methods: {
    sendDone: function () {
      const mqtt = {}
      mqtt.host = MQTT_HOST
      mqtt.port = MQTT_PORT
      mqtt.base_topic = MQTT_BASE_TOPIC

      mqtt.auth = true
      mqtt.username = this.deviceInformation.hardware_device_id
      mqtt.password = this.password

      this.$emit('mqttConfig', mqtt)
      this.$emit('done')
    }
  }
}
</script>
