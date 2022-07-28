<template>
  <div>
    <main-timer />
    <main-calendar />
  </div>
</template>

<script>
import Timer from '../components/Status/Timer.vue'
import calender from '../components/Calendar'

export default {
  name: 'main-page',
  components: {
    'main-timer': Timer,
    'main-calendar': calender
  },
  mounted() {
    this.setIgnore()
    this.disableIgnore()
    const { ipcRenderer } = window.require('electron')
    ipcRenderer.on('setOption', (event, message) => {
      this.$store.commit('setOptions', message)
    })
  }

}

</script>

<style lang="scss"></style>