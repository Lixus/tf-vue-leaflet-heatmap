<template>
  <div style="display: none">
    <slot v-if="ready"></slot>
  </div>
</template>

<script>
import { findRealParent, propsBinder } from "vue2-leaflet"
import { DomEvent } from "leaflet"
import "leaflet.heat"

const L = window.L

const props = {
  latLng: {
    type: Array,
    default: () => [],
    custom: false
  },
  minOpacity: {
    type: Number,
    custom: true,
    default: 0.05
  },
  maxZoom: {
    type: Number,
    custom: true,
    default: 18
  },
  radius: {
    type: Number,
    custom: true,
    default: 25
  },
  blur: {
    type: Number,
    custom: true,
    default: 15
  },
  max: {
    type: Number,
    custom: true,
    default: 1.0
  },
  visible: {
    type: Boolean,
    custom: true,
    default: true
  }
}
export default {
  name: "LHeatmap",
  props,
  data() {
    return {
      ready: false
    }
  },
  mounted() {
    const options = {}
    if (this.minOpacity) {
      options.minOpacity = this.minOpacity
    }
    if (this.maxZoom) {
      options.maxZoom = this.maxZoom
    }
    if (this.radius) {
      options.radius = this.radius
    }
    if (this.blur) {
      options.blur = this.blur
    }
    if (this.max) {
      options.max = this.max
    }
    this.mapObject = L.heatLayer(this.latLng, options)
    DomEvent.on(this.mapObject, this.$listeners)
    propsBinder(this, this.mapObject, props)
    this.ready = true
    this.parentContainer = findRealParent(this.$parent)
    this.parentContainer.addLayer(this, !this.visible)
  },
  beforeDestroy() {
    this.parentContainer.removeLayer(this)
  },
  methods: {
    setMinOpacity(newVal) {
      this.mapObject.setOptions({ minOpacity: newVal })
    },
    setMaxZoom(newVal) {
      this.mapObject.setOptions({ maxZoom: newVal })
    },
    setRadius(newVal) {
      this.mapObject.setOptions({ radius: newVal })
    },
    setBlur(newVal) {
      this.mapObject.setOptions({ blur: newVal })
    },
    setMax(newVal) {
      this.mapObject.setOptions({ max: newVal })
    },
    setVisible(newVal, oldVal) {
      if (newVal === oldVal) return
      if (newVal) {
        this.parentContainer.addLayer(this)
      } else {
        this.parentContainer.removeLayer(this)
      }
    },
    addLatLng(value) {
      this.mapObject.addLatLng(value)
    }
  }
}
</script>
