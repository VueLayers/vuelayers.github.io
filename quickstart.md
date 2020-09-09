# Quick start

VueLayers is distributed as a set of separated components and mixins built as **ES2015** modules.
For in-browser usage, there is a full standalone **UMD** bundle provided.

## Installation

!> VueLayers works with Vue.js **^2.3** and OpenLayers **^6.0**

### NPM

Install current **stable version**

```bash
npm install vuelayers
``` 

The upcoming **next version** can be installed by `next` tag

```bash
npm install vuelayers@next
```  

### CDN

Recommended: [unpkg](https://unpkg.com/)  
You can browse the source of the npm package at [unpkg.com/vuelayers/](https://unpkg.com/vuelayers/).

```html
<!-- include Vue -->
<script src="https://unpkg.com/vue/dist/vue.js"></script>
<!-- include OpenLayers -->
<script src="https://cdn.jsdelivr.net/gh/openlayers/openlayers.github.io@master/en/v6.3.1/build/ol.js"></script>
<!-- include UMD VueLayers build -->
<link rel="stylesheet" href="https://unpkg.com/vuelayers/dist/vuelayers.css">
<script src="https://unpkg.com/vuelayers/dist/vuelayers.umd.min.js"></script>

<script>
  // All VueLayers components now globally installed
  // ...
</script>
```

### Nuxt.js

Create a plugin file (ex: `vuelayers.js`) in the `plugins/` directory, with this content

```js
import Vue from 'vue'
import VueLayers from 'vuelayers'
import 'vuelayers/dist/vuelayers.css' // needs css-loader

Vue.use(VueLayers)
```

Create a file in `modules/` or `shared/` directory named `vuelayers.js`

```js
export default function (moduleOptions) {
	this.options.css.push('vuelayers/dist/vuelayers.css')
}
```

Then in the `nuxt.config.js` file, fill in the plugins and modules section properly. Note that `ssr: false` is mandatory if you use universal mode in Nuxt.

```
plugins: [{
		src: '@/plugins/vuelayers.js',
		ssr: false
	}, { ... }],
modules: [
                ...,
		'~/shared/vueLayers',
	],
```

### Build from source

!> Node **v8** is required

```bash
# clone the repo
git clone --recursive -j8 https://github.com/ghettovoice/vuelayers.git

# install dependencies
cd vuelayers
npm install

# build all targets
npm run build

# check dist dir, there are all ready to use components
ls -l dist
```

## Usage

To use VueLayers in your application, you can import all components or just what you really need.

```js
import Vue from 'vue'
import VueLayers from 'vuelayers'
import 'vuelayers/dist/vuelayers.css' // needs css-loader

Vue.use(VueLayers)

// or individual components

import Vue from 'vue'
import { Map, TileLayer, OsmSource, Geoloc } from 'vuelayers'
import 'vuelayers/dist/vuelayers.css' // needs css-loader

Vue.use(Map)
Vue.use(TileLayer)
Vue.use(OsmSource)
Vue.use(Geoloc)
```

### Global data projection

By default all components accept coordinates in map view projection (**EPSG:3857** by default)
but you can bind all components to accept and return coordinates in another projection with
`dataProjection` global option or with [`data-projection`](component/map.md#data-projection) property on the `vl-map` 
component. 

This rule applies only for plain coordinates, GeoJSON encoded features or 
geometries. It works only as a thin projection transform layer between Vue and OpenLayers therefore
internally OpenLayers objects would still use current projection of the map view.

```js
// all input/output coordinates, GeoJSON features in EPSG:4326 projection
Vue.use(VueLayers, {
  dataProjection: 'EPSG:4326',
})
```

Now you are ready to build your awesome GIS application. Check the [VueLayers official demo](demo.md) to get 
more ideas on what you can make with VueLayers.

Simple map with OSM layer example (editable)

<vuep template="#example"></vuep>

<script v-pre type="text/x-template" id="example">
<template>
  <div>
    <vl-map :load-tiles-while-animating="true" :load-tiles-while-interacting="true"
             data-projection="EPSG:4326" style="height: 400px">
      <vl-view :zoom.sync="zoom" :center.sync="center" :rotation.sync="rotation"></vl-view>

      <vl-geoloc @update:position="geolocPosition = $event">
        <template slot-scope="geoloc">
          <vl-feature v-if="geoloc.position" id="position-feature">
            <vl-geom-point :coordinates="geoloc.position"></vl-geom-point>
            <vl-style-box>
              <vl-style-icon src="_media/marker.png" :scale="0.4" :anchor="[0.5, 1]"></vl-style-icon>
            </vl-style-box>
          </vl-feature>
        </template>
      </vl-geoloc>

      <vl-layer-tile id="osm">
        <vl-source-osm></vl-source-osm>
      </vl-layer-tile>
    </vl-map>
    <div style="padding: 20px">
      Zoom: {{ zoom }}<br>
      Center: {{ center }}<br>
      Rotation: {{ rotation }}<br>
      My geolocation: {{ geolocPosition }}
    </div>
  </div>
</template>

<script>
  export default {
    data () {
      return { 
        zoom: 2,
        center: [0, 0],
        rotation: 0,
        geolocPosition: undefined,
      }
    },
  }
</script>
</script>
