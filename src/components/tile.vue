<template>
  <div></div>
</template>

<script>
  import L from 'leaflet'
  import EventBus from './../bus.js'

  const props = {
    url: String,
    bare: Boolean,
    attribution: {
      type: String,
      custom: true
    },
    detectRetina: {
      type: Boolean,
      custom: false,
      default: false
    },
    token: {
      type: String,
      custom: true
    },
    opacity: {
      type: Number,
      custom: false,
      default: 1.0
    },
    zIndex: {
      type: Number,
      default: 1
    },
    options: {
      type: Object,
      default: function() {
        return {};
      }
    },
    tileLayerClass: {
      type: Function,
      default: L.tileLayer
    }
  };

  export default {
    props: props,
    mounted() {
      let vm = this
      vm.mapa = L.tileLayer(vm.url, vm.options)

      EventBus.$on('mounted', function(mapa) {
        if (vm.$parent._isMounted) { 
          if (mapa._leaflet_id  == vm.$parent.mapa._leaflet_id) {
            if (!vm.bare) {
              vm.mapa.addTo(vm.$parent.mapa)
            }
            EventBus.$emit('mounted', vm.mapa)
          }
        }
      })
    },
    methods: {
      add(mapa) {
        let vm = this
        vm.mapa.addTo(mapa)
      },
    }
  }
</script>
