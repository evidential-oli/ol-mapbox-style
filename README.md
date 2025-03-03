# ol-mapbox-style

Create [OpenLayers](https://openlayers.org/) maps from [Mapbox Style Specification](https://docs.mapbox.com/mapbox-gl-js/style-spec/) objects.

## Getting started

### Installation

To use the library in an application with an npm based dev environment, install it with

    npm install ol-mapbox-style

When installed this way, just import the ol-mapbox-style module, like in the usage example below. To use a standalone build of ol-mapbox-style, just include 'dist/olms.js' on your HTML page, and access the global `olms` object.

**ol-mapbox-style requires [OpenLayers](https://npmjs.com/package/ol) version >= 6.1.0 < 7**.

### Usage example

The code below creates an OpenLayers map from Mapbox's Bright v9 style:

```js
import olms from 'ol-mapbox-style';

var key = 'Your Mapbox Access Token here';
olms('map', 'https://api.mapbox.com/styles/v1/mapbox/bright-v9?access_token=' + key);
```

Only commonly available system fonts and [Google Fonts](https://developers.google.com/fonts/) will automatically be available for any `text-font` defined in the Mapbox Style object. It is the responsibility of the application to load other fonts. Because `ol-mapbox-style` uses system and web fonts instead of PBF/SDF glyphs, the [font stack](https://www.mapbox.com/help/manage-fontstacks/) is treated a little different: style and weight are taken from the primary font (i.e. the first one in the font stack). Subsequent fonts in the font stack are only used if the primary font is not available/loaded, and they will be used with the style and weight of the primary font.

To apply a subset of the layers defined in the Mapbox Style layer to a custom OpenLayers layer, use the `applyStyle()` function.

To apply the properties of the Mapbox Style's `background` layer to the map or a `VectorTile` layer, use the `applyBackground()` function.

To create a style function for individual OpenLayers vector or vector tile layers, use the `stylefunction` module:

```js
import stylefunction from 'ol-mapbox-style/dist/stylefunction';
// OpenLayers imports from https://npmjs.com/package/ol
import VectorLayer from 'ol/layer/Vector';
import VectorSource from 'ol/source/Vector';
import GeoJSON from 'ol/format/GeoJSON';

var layer = new VectorLayer({
  source: new VectorSource({
    format: new GeoJSON(),
    url: 'data/states.geojson'
  })
});

fetch('data/states.json').then(function(response) {
  response.json().then(function(glStyle) {
    stylefunction(layer, glStyle, 'states');
  });
});
```

## Compatibility notes

### Support for old browsers

Internet Explorer (version 11) and other old browsers (Android 4.x) are supported when polyfills for the following features are loaded:

*   `fetch` (including `Promise`)

## API

<!-- Generated by documentation.js. Update this documentation by updating the source code. -->

#### Table of Contents

*   [applyStyle](#applystyle)
    *   [Parameters](#parameters)
*   [renderTransparent](#rendertransparent)
    *   [Parameters](#parameters-1)
*   [recordStyleLayer](#recordstylelayer)
    *   [Parameters](#parameters-2)
*   [stylefunction](#stylefunction)
    *   [Parameters](#parameters-3)
*   [applyBackground](#applybackground)
    *   [Parameters](#parameters-4)
*   [olms](#olms)
    *   [Parameters](#parameters-5)
*   [apply](#apply)
    *   [Parameters](#parameters-6)
*   [getLayer](#getlayer)
    *   [Parameters](#parameters-7)
*   [getLayers](#getlayers)
    *   [Parameters](#parameters-8)
*   [getSource](#getsource)
    *   [Parameters](#parameters-9)

### applyStyle

```js
import {applyStyle} from 'ol-mapbox-style';
```

Applies a style function to an `ol.layer.VectorTile` or `ol.layer.Vector`
with an `ol.source.VectorTile` or an `ol.source.Vector`. The style function
will render all layers from the `glStyle` object that use the specified
`source`, or a subset of layers from the same source. The source needs to be
a `"type": "vector"` or `"type": "geojson"` source.

Two additional properties will be set on the provided layer:

*   `mapbox-source`: The `id` of the Mapbox Style document's source that the
    OpenLayers layer was created from. Usually `apply()` creates one
    OpenLayers layer per Mapbox Style source, unless the layer stack has
    layers from different sources in between.
*   `mapbox-layers`: The `id`s of the Mapbox Style document's layers that are
    included in the OpenLayers layer.

#### Parameters

*   `layer` **(VectorTileLayer | VectorLayer)** OpenLayers layer.
*   `glStyle` **([string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String) | [Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object))** Mapbox Style object.
*   `source` **([string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String) | [Array](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array)<[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)>)** `source` key or an array of layer `id`s from the
    Mapbox Style object. When a `source` key is provided, all layers for the
    specified source will be included in the style function. When layer `id`s
    are provided, they must be from layers that use the same source.
*   `path` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** Path of the style file. Only required when
    a relative path is used with the `"sprite"` property of the style. (optional, default `undefined`)
*   `resolutions` **[Array](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array)<[number](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number)>** Resolutions for mapping resolution to zoom level. (optional, default `undefined`)

Returns **[Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)** Promise which will be resolved when the style can be used
for rendering.

### renderTransparent

```js
import {renderTransparent} from 'ol-mapbox-style/dist/stylefunction';
```

Configure whether features with a transparent style should be rendered. When
set to `true`, it will be possible to hit detect content that is not visible,
like transparent fills of polygons, using `ol/layer/Layer#getFeatures()` or
`ol/Map#getFeaturesAtPixel()`

#### Parameters

*   `enabled` **[boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)** Rendering of transparent elements is enabled.
    Default is `false`.

### recordStyleLayer

```js
import {recordStyleLayer} from 'ol-mapbox-style/dist/stylefunction';
```

Turns recording of the Mapbox Style's `layer` on and off. When turned on,
the layer that a rendered feature belongs to will be set as the feature's
`mapbox-layer` property.

#### Parameters

*   `record` **[boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)** Recording of the style layer is on. (optional, default `false`)

### stylefunction

```js
import stylefunction from 'ol-mapbox-style/dist/stylefunction';
```

Creates a style function from the `glStyle` object for all layers that use
the specified `source`, which needs to be a `"type": "vector"` or
`"type": "geojson"` source and applies it to the specified OpenLayers layer.

Two additional properties will be set on the provided layer:

*   `mapbox-source`: The `id` of the Mapbox Style document's source that the
    OpenLayers layer was created from. Usually `apply()` creates one
    OpenLayers layer per Mapbox Style source, unless the layer stack has
    layers from different sources in between.
*   `mapbox-layers`: The `id`s of the Mapbox Style document's layers that are
    included in the OpenLayers layer.

This function also works in a web worker. In worker mode, the main thread needs
to listen to messages from the worker and respond with another message to make
sure that sprite image loading works:

```js
 worker.addEventListener('message', event => {
  if (event.data.action === 'loadImage') {
    const image = new Image();
    image.crossOrigin = 'anonymous';
    image.addEventListener('load', function() {
      createImageBitmap(image, 0, 0, image.width, image.height).then(imageBitmap => {
        worker.postMessage({
          action: 'imageLoaded',
          image: imageBitmap,
          src: event.data.src
        }, [imageBitmap]);
      });
    });
    image.src = event.data.src;
  }
});
```

#### Parameters

*   `olLayer` **(VectorLayer | VectorTileLayer)** OpenLayers layer to
    apply the style to. In addition to the style, the layer will get two
    properties: `mapbox-source` will be the `id` of the `glStyle`'s source used
    for the layer, and `mapbox-layers` will be an array of the `id`s of the
    `glStyle`'s layers.
*   `glStyle` **([string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String) | [Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object))** Mapbox Style object.
*   `source` **([string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String) | [Array](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array)<[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)>)** `source` key or an array of layer `id`s
    from the Mapbox Style object. When a `source` key is provided, all layers for
    the specified source will be included in the style function. When layer `id`s
    are provided, they must be from layers that use the same source.
*   `resolutions` **[Array](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array)<[number](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number)>** Resolutions for mapping resolution to zoom level. (optional, default `[78271.51696402048,39135.75848201024,19567.87924100512,9783.93962050256,4891.96981025128,2445.98490512564,1222.99245256282,611.49622628141,305.748113140705,152.8740565703525,76.43702828517625,38.21851414258813,19.109257071294063,9.554628535647032,4.777314267823516,2.388657133911758,1.194328566955879,0.5971642834779395,0.29858214173896974,0.14929107086948487,0.07464553543474244]`)
*   `spriteData` **[Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)** Sprite data from the url specified in
    the Mapbox Style object's `sprite` property. Only required if a `sprite`
    property is specified in the Mapbox Style object. (optional, default `undefined`)
*   `spriteImageUrl` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** Sprite image url for the sprite
    specified in the Mapbox Style object's `sprite` property. Only required if a
    `sprite` property is specified in the Mapbox Style object. (optional, default `undefined`)
*   `getFonts` **function ([Array](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array)<[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)>): [Array](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array)<[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)>** Function that
    receives a font stack as arguments, and returns a (modified) font stack that
    is available. Font names are the names used in the Mapbox Style object. If
    not provided, the font stack will be used as-is. This function can also be
    used for loading web fonts. (optional, default `undefined`)

Returns **StyleFunction** Style function for use in
`ol.layer.Vector` or `ol.layer.VectorTile`.

### applyBackground

```js
import {applyBackground} from 'ol-mapbox-style';
```

Applies properties of the Mapbox Style's first `background` layer to the
provided map or VectorTile layer.

#### Parameters

*   `mapOrLayer` **(PluggableMap | VectorTileLayer)** OpenLayers Map or VectorTile layer.
*   `glStyle` **[Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)** Mapbox Style object.

### olms

```js
import olms from 'ol-mapbox-style';
```

Loads and applies a Mapbox Style object to an OpenLayers Map. This includes
the map background, the layers, the center and the zoom.

The center and zoom will only be set if present in the Mapbox Style document,
and if not already set on the OpenLayers map.

Layers will be added to the OpenLayers map, without affecting any layers that
might already be set on the map.

Layers added by `apply()` will have two additional properties:

*   `mapbox-source`: The `id` of the Mapbox Style document's source that the
    OpenLayers layer was created from. Usually `apply()` creates one
    OpenLayers layer per Mapbox Style source, unless the layer stack has
    layers from different sources in between.
*   `mapbox-layers`: The `id`s of the Mapbox Style document's layers that are
    included in the OpenLayers layer.

This function sets an additional `mapbox-style` property on the OpenLayers
map instance, which holds the Mapbox Style object.

#### Parameters

*   `map` **(PluggableMap | [HTMLElement](https://developer.mozilla.org/docs/Web/HTML/Element) | [string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String))** Either an existing OpenLayers Map
    instance, or a HTML element, or the id of a HTML element that will be the
    target of a new OpenLayers Map.
*   `style` **([string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String) | [Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object))** JSON style object or style url pointing to a
    Mapbox Style object. When using Mapbox APIs, the url must contain an access
    token and look like
    `https://api.mapbox.com/styles/v1/mapbox/bright-v9?access_token=[your_access_token_here]`.
    When passed as JSON style object, all OpenLayers layers created by `apply()`
    will be immediately available, but they may not have a source yet (i.e. when
    they are defined by a TileJSON url in the Mapbox Style document). When passed
    as style url, layers will be added to the map when the Mapbox Style document
    is loaded and parsed.

Returns **[Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)** A promise that resolves after all layers have been added to
the OpenLayers Map instance, their sources set, and their styles applied. the
`resolve` callback will be called with the OpenLayers Map instance as
argument.

### apply

```js
import {apply} from 'ol-mapbox-style';
```

Like `olms`, but returns an `ol/Map` instance instead of a `Promise`.

#### Parameters

*   `map` **(PluggableMap | [HTMLElement](https://developer.mozilla.org/docs/Web/HTML/Element) | [string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String))** Either an existing OpenLayers Map
    instance, or a HTML element, or the id of a HTML element that will be the
    target of a new OpenLayers Map.
*   `style` **([string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String) | [Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object))** JSON style object or style url pointing to a
    Mapbox Style object. When using Mapbox APIs, the url must contain an access
    token and look like
    `https://api.mapbox.com/styles/v1/mapbox/bright-v9?access_token=[your_access_token_here]`.
    When passed as JSON style object, all OpenLayers layers created by `apply()`
    will be immediately available, but they may not have a source yet (i.e. when
    they are defined by a TileJSON url in the Mapbox Style document). When passed
    as style url, layers will be added to the map when the Mapbox Style document
    is loaded and parsed.

Returns **PluggableMap** The OpenLayers Map instance that will be populated with the
contents described in the Mapbox Style object.

### getLayer

```js
import {getLayer} from 'ol-mapbox-style';
```

Get the OpenLayers layer instance that contains the provided Mapbox Style
`layer`. Note that multiple Mapbox Style layers are combined in a single
OpenLayers layer instance when they use the same Mapbox Style `source`.

#### Parameters

*   `map` **PluggableMap** OpenLayers Map.
*   `layerId` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** Mapbox Style layer id.

Returns **Layer** OpenLayers layer instance.

### getLayers

```js
import {getLayers} from 'ol-mapbox-style';
```

Get the OpenLayers layer instances for the provided Mapbox Style `source`.

#### Parameters

*   `map` **PluggableMap** OpenLayers Map.
*   `sourceId` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** Mapbox Style source id.

Returns **[Array](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array)\<Layer>** OpenLayers layer instances.

### getSource

```js
import {getSource} from 'ol-mapbox-style';
```

Get the OpenLayers source instance for the provided Mapbox Style `source`.

#### Parameters

*   `map` **PluggableMap** OpenLayers Map.
*   `sourceId` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** Mapbox Style source id.

Returns **Source** OpenLayers source instance.

## Building the library

    npm run build

The resulting distribution files will be in the `dist/` folder. To see the library in action, navigate to `dist/index.html`.

To run test locally, run

    npm test

For debugging tests in the browser, run

    npm run karma

and open a browser on the host and port indicated in the console output (usually <http://localhost:9876/>) and click the 'DEBUG' button to go to the debug environment.

[![CircleCI](https://circleci.com/gh/openlayers/ol-mapbox-style.svg?style=svg)](https://circleci.com/gh/openlayers/ol-mapbox-style)
