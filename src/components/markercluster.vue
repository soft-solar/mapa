<template>
  <div>
    <slot></slot>
  </div>
</template>

<script>
import L from 'leaflet'
import EventBus from './../bus'
import 'leaflet.markercluster'

export default {
  props: [
    'bare',
    'options',
  ],
  mounted () {
    let vm = this
    this.mapa = L.markerClusterGroup(this.options)

    EventBus.$on('mounted', function(mapa) {
      if (vm.$parent._isMounted) { 
        if (mapa._leaflet_id  == vm.$parent.mapa._leaflet_id) {
          if (!vm.bare) {  
            vm.add(vm.$parent.mapa)
            vm.update()
          }
          EventBus.$emit('mounted', vm.mapa)
        }
      }
    })
  },
  beforeDestroy () {
    this.remove()
  },
  updated() {
    let vm = this
    if (!vm.bare) {
      vm.update()
    }
  },
  methods: {
    add (mapa) {
      let vm = this
      vm.mapa.addTo(mapa)
    },
    remove () {
      let vm = this
      let parent = vm.$parent.mapa

      vm.mapa.clearLayers()
      parent.removeLayer(vm.mapa)
    },
    clear() {
      this.mapa.clearLayers()
    },
    update (mapas) {
      let vm = this
      let markers = mapas

      vm.clear()
      
      if (!vm.bare) {
        for (let i = vm.$children.length; i--;) {
          for (let ii = vm.$children[i].$children.length; ii--;) {
            vm.$children[i].$children[ii].add(vm.$children[i].mapa)    
          }
        }
        markers = vm.$children.map(marker => marker.mapa)
      }
      vm.mapa.addLayers(markers)
      vm.$emit('updated')
    },
  }
}
</script>
