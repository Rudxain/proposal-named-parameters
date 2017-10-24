# Introduction

* Early reviewers: @adamk, @domenic, @slightlyoff, @erights, @waldemarhorwats, @dimvar

This is a [stage 0](https://tc39.github.io/process-document/) proposal to add named parameters to Javascript.

In this formulation, an **opt-in** mechanism is introduced to expose parameter names with a new keyword ```as```:

```javascript
function dostuff(b as b, c as c) {
}
```

That enables callers of your function to use named parameters:

```javascript
dostuff(b: 1, c: false);
```

## Keeping the contract

The opt-in mechanism ```as``` serves as an explicit and deliberate decision of the author of the function to expose parameter names, ***keeping*** the contract that parameter names are erased by default.

Breaking that contract can be harmful, notably with the interaction with minifiers: exposing (without consent) the parameters now forces library authors to maintain them and their backwards compatibility.

We expect (encourage, really) functions that have a large number of parameters to be the exception rather than the norm,  so an opt-in mechanism works well too.

# Use cases

## Long parameters list

The most immediate application of named parameter is with web APIs that has evolved, humm, lets say, organically. So, for example, we could make ```Date.UTC``` more ergonomic by exposing named parameters:

```javascript
// Signature:
// Date.UTC(year, month[, day[, hour[, minute[, second[, millisecond]]]]])

// Without named parameters:
var utcDate = new Date(Date.UTC(96, 11, 1, 0, 0, 0));

// With named parameters:
var utcDate = new Date(Date.UTC(
  year: 96, 
  month: 11, 
  day: 1, 
  hour: 0, 
  minute: 0, 
  millisecond: 0
));
```

WebGL is also a good example of a source of functions with 7 or more parameters (by the valid nature of that space):

```javascript
// What does this mean?
ctx.drawImage(image, 33, 71, 104, 124, 21, 20, 87, 104);

// Ah, much clearer:
ctx.drawImage(
  image: image,
  sx: 33,
  sy: 71,
  swidth: 104,
  sHeight: 124,
  dx: 21,
  dy: 20,
  dWidth: 87,
  dHeight: 104
);
```

## Documentation

It works well too even with as few as three parameters and extremely popular functions (but possibly a very unpopular parameter). For example:

```javascript
// As opposed to:
//
// el.addEventListener("mouseup", listener, true)
//
// followed by your readers googling what the third argument means you can write:

el.addEventListener("mouseup", listener, capture: false).
```

## Disambiguation

In addition to making code more readable, it also has applications when parameter types are of the same type. Example:

```javascript
// Documentation, makes reading code easier to know what to expect

// as opposed to move(100, 200) is the 100 x or y?
move(x: 100, y: 200);

// as opposed to resize(20, 30) is 20 the width or height?
resize(width: 20, height: 30); 
```

You can find here a list of web apis sorted by [number of parameters](#web-apis).

## Multiple Optional Parameters

Named parameters enable function signatures that have already declared optional parameters to have more of them after that.

```javascript
// Ah, neat, now I can make "c" optional with a default value and add a new required parameter!
function dostuff(b as b, opt_c as c, opt_d as d) {
}

// phew, i don't have to use dostuff(1, undefined, "hello") ...
dostuff(b: 1, d: "hello");
```

# Cross Cutting Considerations

Generally speaking, we would want all features (past and future) that are applied to parameters to apply to named parameters.

## Default Values

```javascript
function a(b = 123 as b, c as c) {
}
a(c: false) // b takes 123
```

## Destructuring Parameters

Destructuring works the same way, except that the parameter can be referred nominally rather than (or, in addition to) positionally. For example:

```javascript
fetch("url.txt", options: {
  method: 'GET',
  headers: myHeaders,
  mode: 'cors',
  cache: 'default'
});
```


# Areas of Investigation

* can you intermingle positional arguments with named arguments?

# Prior art

* https://esdiscuss.org/topic/named-paramters
* https://github.com/lukescott/es-named-arguments

# Annex

## Web APIs

Here are the functions on the Web platform with the most paramters (data from http://html5index.org/json/):

Notable examples:

* HTMLInputElement.setRangeText: 4
* Document.open: 4
* EventTarget.addEventListener: 4

All Web APIs with 4 or more parameters, in descending order:

* WebGLRenderingContext.texSubImage2D: 9
* CanvasRenderingContext2D.drawImage: 9
* WebGLRenderingContext.texImage2D: 9
* WebGLRenderingContext.copyTexSubImage2D: 8
* WebGLRenderingContext.copyTexImage2D: 8
* WebGLRenderingContext.compressedTexSubImage2D: 8
* CanvasRenderingContext2D.ellipse: 8
* Path2D.ellipse: 8
* WebGLRenderingContext.compressedTexImage2D: 7
* Date.UTC: 7
* CanvasRenderingContext2D.arcTo: 7
* WebGLRenderingContext.readPixels: 7
* CanvasRenderingContext2D.putImageData: 7
* SVGPathElement.createSVGPathSegArcAbs: 7
* Path2D.arcTo: 7
* SVGPathElement.createSVGPathSegArcRel: 7
* SVGPathElement.createSVGPathSegCurvetoCubicAbs: 6
* Path2D.addText: 6
* CanvasRenderingContext2D.bezierCurveTo: 6
* Path2D.addPathByStrokingText: 6
* WebGLRenderingContext.vertexAttribPointer: 6
* SVGPathElement.createSVGPathSegCurvetoCubicRel: 6
* CanvasRenderingContext2D.setTransform: 6
* Path2D.bezierCurveTo: 6
* CanvasRenderingContext2D.createRadialGradient: 6
* Path2D.arc: 6
* AudioListener.setOrientation: 6
* CanvasRenderingContext2D.transform: 6
* CanvasRenderingContext2D.arc: 6
* WorkerGlobalScope.createImageBitmap: 5
* WebGLRenderingContext.uniform4f: 5
* WebGLRenderingContext.framebufferTexture2D: 5
* WebGLRenderingContext.vertexAttrib4f: 5
* WebGLRenderingContext.uniform4i: 5
* IDBKeyRange.bound: 4
* CanvasRenderingContext2D.clearRect: 4
* Path2D.rect: 4
* Path2D.quadraticCurveTo: 4
* WebGLRenderingContext.colorMask: 4
* WebGLRenderingContext.stencilFuncSeparate: 4
* CanvasRenderingContext2D.strokeText: 4
* CanvasRenderingContext2D.strokeRect: 4
* WebGLRenderingContext.scissor: 4
* Date.setUTCHours: 4
* WebGLRenderingContext.renderbufferStorage: 4
* WebGLRenderingContext.clearColor: 4
* WebGLRenderingContext.uniform3f: 4
* CustomEvent.initCustomEvent: 4
* Date.setHours: 4
* CanvasRenderingContext2D.rect: 4
* CanvasRenderingContext2D.quadraticCurveTo: 4
* SVGPaint.setPaint: 4
* CanvasRenderingContext2D.isPointInPath: 4
* CanvasRenderingContext2D.getImageData: 4
* CanvasRenderingContext2D.fillText: 4
* CanvasRenderingContext2D.fillRect: 4
* WebGLRenderingContext.drawElements: 4
* WebGLRenderingContext.uniform3i: 4
* HTMLTextAreaElement.setRangeText: 4
* WebGLRenderingContext.blendFuncSeparate: 4
* WebGLRenderingContext.framebufferRenderbuffer: 4
* WebGLRenderingContext.blendColor: 4
* SVGPathElement.createSVGPathSegCurvetoCubicSmoothAbs: 4
* SVGPathElement.createSVGPathSegCurvetoCubicSmoothRel: 4
* Document.open: 4
* WebGLRenderingContext.vertexAttrib3f: 4
* SVGPathElement.createSVGPathSegCurvetoQuadraticAbs: 4
* HTMLInputElement.setRangeText: 4
* SVGPathElement.createSVGPathSegCurvetoQuadraticRel: 4
* CanvasRenderingContext2D.createLinearGradient: 4
* WebGLRenderingContext.viewport: 4
* WebGLRenderingContext.stencilOpSeparate: 4

And here is the same list but sorted by number of optional parameters:

* CanvasRenderingContext2D.drawImage: 6 optional [of 9]
* Date.UTC: 5 optional [of 7]
* Document.open: 4 optional [of 4]
* CanvasRenderingContext2D.putImageData: 4 optional [of 7]
* Date.setUTCHours: 3 optional [of 4]
* Date.setHours: 3 optional [of 4]
* WebGLRenderingContext.texImage2D: 3 optional [of 9]
* HTMLTextAreaElement.setRangeText: 3 optional [of 4]
* AudioBufferSourceNode.start: 3 optional [of 3]
* HTMLInputElement.setRangeText: 3 optional [of 4]
* Blob.slice: 3 optional [of 3]
* CanvasRenderingContext2D.isPointInPath: 2 optional [of 4]
* IDBIndex.openKeyCursor: 2 optional [of 2]
* IDBIndex.openCursor: 2 optional [of 2]
* CanvasRenderingContext2D.arcTo: 2 optional [of 7]
* CanvasRenderingContext2D.clip: 2 optional [of 2]
* JSON.stringify: 2 optional [of 3]
* IDBObjectStore.openCursor: 2 optional [of 2]
* HTMLMediaElement.addTextTrack: 2 optional [of 3]
* Date.setUTCMinutes: 2 optional [of 3]
* CanvasRenderingContext2D.fill: 2 optional [of 2]
* Document.createNodeIterator: 2 optional [of 3]
* WebGLRenderingContext.texSubImage2D: 2 optional [of 9]
* Date.setUTCFullYear: 2 optional [of 3]
* Date.setMinutes: 2 optional [of 3]
* Date.setFullYear: 2 optional [of 3]
* WebSocket.close: 2 optional [of 2]
* Document.execCommand: 2 optional [of 3]
* Path2D.addPathByStrokingText: 2 optional [of 6]
* Path2D.addText: 2 optional [of 6]
* Document.createTreeWalker: 2 optional [of 3]
* Path2D.arcTo: 2 optional [of 7]
* IDBKeyRange.bound: 2 optional [of 4]