# Before first release

## Path

Review ellipse things. There are a few TODOs and wrong things in there.

## Image

Do we actually have a renderer that supports arbitrary outline cut ? 

* Prototype I.cut_glyphs
* Finish I.blend, remove blending operators for now but support 
  alpha group. Implement Gg.Color.blend. Specify the semantics.
* Image.raster. Accept any raster value, renderer does its best, but it 
  also accepts a 
  raster:Gg.raster -> [`Await | `Ok of Gg.raster ] function that the
  renderer user can plug to use "external" tools to improve normalisation.

## SVG renderer 

* Use dublin core for metadata ? Look into inkscape. 
* Outlines should be output to a <defs><g id="id" /></defs> for reuse. 
  Or is that impossible ? 

## Glyph api

Maybe an approach similar to raster. Define a type that allows for
quite different font specification and provide a normalizer in the
renderers for resolution.

* OTFm
* Cairo, pdf & fonts
  http://lists.cairographics.org/archives/cairo/2007-February/009452.html
  http://lists.cairographics.org/archives/cairo/2007-September/011427.html

* OpenVG, 
  VGFont, just maps indices to glyphs, applications are responsible for 
  creating the map. No metric or layout info in the font. 
  Applications responsible for text layout operations.
  Mapping defined by the app, e.g.
	  - unicode character code (for subsets) -> glyph 
	    no need for additional table.
          - native font glyph indices (as found in TT or OpenType font)
	  - other.
  A glyph is either a VGPath (vector outline) or a VGimage (raster image)

* SVG 1.2 tiny, text element can specify a line of text
  the text layout is performed by the standard.

* SVG 1.1, more control. E.g. tspan allows x, y offset as in VG api.
  +rotate. Also possible to layout text along a path. 

* Test miànjï 面积 (area, surface area), vector.
* Test font http://www.twardoch.com/fonts/ Nadeyezhda 

# Db images ideas 

* Quadratic paths.
* vgrender that takes a cmxs and outputs svg or pdf.
* Test degenerate color stops discarding.
* Test degenerate subpaths rendering. 
* Dash offset is for start of each subpath, negative dashoffsets. 
* Primitives, special cases axial with p = p', radial with c = c'. 
* prim.ml only base images (the renderer needs to crop). 
* Test geometric primitives, quadric and ellipse for pdf renderer.
* fill rules.
* gradient stops as premultiplied color value cannot blend red to blue
  while alpha 1.0 to alpha 0.0.
* The IEEE 754 double floating point line vs the real line.
* The IEEE 754 double floating point grid vs the real plane
* How many doubles between 10 and 11, 100 and 101, 1000 and 1001, etc.
  or 2 ** n and 2 ** n+1.
* slogans.
* Pies, better to eat them than use them.
  http://en.wikipedia.org/wiki/File:Piecharts.svg 
* Rectangle, area cut of w = 0 or s = 0 is nothing but outline cut
  is segment. 

#  After first release

## Path

Quite a few convience operations on paths could be added. This would
be only at the Vg level renderers wouldn't need to be extended. But
does it really belong in Vg ? Tension between general computational
geometry lib and rendering lib.

* P.mem : area -> path -> p2 -> bool
* P.length : path -> float (* arc length *)
* P.split : path -> float -> path * path (* split at given distance on path *)
* P.cubic_fold
* P.square 
* Boolean operations on paths
* Minkowski sum

## Image 

Support more blending operators. Is it really useful for the kind 
of rendering targetted by Vg ? 

* http://cairographics.org/operators/
* http://lists.cairographics.org/archives/cairo/2008-October/015362.html


# Misc pointers

* http://www.fontconfig.org/fontconfig-user.html
* http://processingjs.nihongoresources.com/bezierinfo/
* http://www.codeproject.com/Articles/226569/Drawing-polylines-by-tessellation
* http://portal.acm.org/citation.cfm?id=129906 
* http://books.google.com/books?q=vatti+clipping+agoston
* http://www.antigrain.com/research/adaptive_bezier/index.html


# Attic PDF renderer notes

* Generate PDF/A
* Rewrite to use Form XObjects. 
* Rewrite to use Gs dictionaries.
* Ellipse output. [done ?]
* Be more clever in state changes and stack push/pop. I guess
  this means maintaining the state in the renderer.
* Alpha handling.
* Pretty print dictionnary output.
* Blue color seems strange. I'm doing the right thing ?
* escape string metadata in o_pdf_str.
* ~date not very friendly and D: prefix.
* /Producer OCaml Vg module %%VERSION%%
* %F or %f ?

Page 65 of the PDF spec shows how, with an indirect object, we can
write a stream without knowing its length in advance. Instead of
buffering the stream in memory we could do that. However deflate
callback integration becomes tricky (remove deflates and use simple
compression).

The user should write the compressed stream using
the first function, we will call the second one to output the stream.

(string -> int) -> (string -> int -> unit) -> unit

The compress function should reapeatedly call the first one
to get data and call the first one to output. Hard to integrate
I think (input).





