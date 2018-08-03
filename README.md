# Textarea Caret Position

Get the `top` and `left` coordinates of the caret in a `<textarea>` or
`<input type="text">`, in pixels. Useful for textarea autocompletes like
GitHub or Twitter, or for single-line autocompletes like the name drop-down
in Facebook or the company dropdown on Google Finance.

How it's done: a faux `<div>` is created off-screen and styled exactly like the
`textarea` or `input`. Then, the text of the element up to the caret is copied
into the `div` and a `<span>` is inserted right after it. Then, the text content
of the span is set to the remainder of the text in the textarea, in order to
faithfully reproduce the wrapping in the faux `div`. The same is done for the
`input` to simplify the code, though it makes no difference.

**NOTE**: This fork of [textarea-caret-position](http://rawgit.com/component/textarea-caret-position/)
          wraps the functionality in an module (UMD) which exposes some
					more (helper) functions and adds some options (see API description below).


## Demo

Check out the ~~[JSFiddle](http://jsfiddle.net/dandv/aFPA7/)~~ _(original library)_
or the [test.html](http://rawgit.com/mmig/textarea-caret-position/master/test/index.html).

## Features

* supports `<textarea>`s and `<input type="text">` elements
* pixel precision
* RTL (right-to-left) support
* no dependencies whatsoever
* browser compatibility: Chrome, Safari, Firefox (despite [two](https://bugzilla.mozilla.org/show_bug.cgi?id=753662) [bugs](https://bugzilla.mozilla.org/show_bug.cgi?id=984275) it has), Opera, IE9+
* supports any font family and size, as well as text-transforms
* the text area can have arbitrary padding or borders
* not confused by horizontal or vertical scrollbars in the textarea
* supports hard returns, tabs (except on IE) and consecutive spaces in the text
* correct position on lines longer than the columns in the text area
* [no problem](http://archive.today/F4XCV#13402035) getting the correct position when the input text is scrolled (i.e. the first visible character is no longer the first in the text)
* no ["ghost" position in the empty space](https://github.com/component/textarea-caret-position/blob/06d2197f85f96405b43724e56dc56f220c0092a5/test/position_off_after_wrapping_with_whitespace_before_EOL.gif) at the end of a line when wrapping long words in a `<textarea>`


## API

Usage Example:
```js
var caretPosition = require('textarea-caret');

document.querySelector('textarea').addEventListener('input', function () {
  var coordinates = caretPosition.getCoordinates(this, this.selectionEnd);
  console.log(coordinates.top);
  console.log(coordinates.left);
})
```


> `createDiv(options) : void`
> 	create faux DIV
> 		- options (Options): [OPTIONAL] additional options (see below)
>
> `measureFontZoom() : number`
> 	calculate a "font zoom": zoom that is applied "by the environment" and not set by the CSS/HTML itself
> 	(e.g. some Android variants allow setting a font zoom in the system settings).  
> 	If not "font zoom" is detected, returns `1` by default (i.e. no scaling).
>
> `styleDiv(element, position, div, options) : void`
> 	apply styling of the target-element to the faux-DIV for accurately calculating the coordinates
> 		- element (HTMLElement): the target element (textarea or input)
> 		- position (number): the character/text index, i.e. position for which the coordinates should be calculated
> 		- div (HTMLElement): the faux DIV for calculating the coordinates
> 		- options (Options): [OPTIONAL] additional options (see below)
>
> `resetStyleDiv() : void`
> 	reset styling for faux DIV, i.e. force re-styling for next calculation (only relevant if DIV is reused)
>
> `resetDiv(options) : void`
> 	reset/remove faux DIV, if reuse or debug was enabled  
> 	NOTE if faux DIV was created with custom `options.id`,
> 			 then the options-argument must contain the same `id`
> 			 (otherwise this call will have not effect)
> 		- options (Options): [OPTIONAL] additional options (see below)
>
> `updateCoordinates(element, position, div, options) : {top: number, left: number}`
> 	recalulate the coordinates (e.g. due to changed text) without re-styling the faux DIV
> 		- element (HTMLElement): the target element (textarea or input)
> 		- position (number): the character/text index, i.e. position for which the coordinates should be calculated
> 		- div (HTMLElement): the faux DIV for calculating the coordinates
> 		- options (Options): [OPTIONAL] additional options (see below)
>
> `getCoordinates(element, position, options) : {top: number, left: number}`
> 	get coordinates in the target `element` for the text `position` (i.e. index in string)
> 		- element (HTMLElement): the target element (textarea or input)
> 		- position (number): the character/text index, i.e. position for which the coordinates should be calculated
> 		- options (Options): [OPTIONAL] additional options (see below)
>
> `Options`:
> 	optional settings for calculating the coordinates
> 		- options for calculating the caret coordinates:
> 		- options.debug	BOOLEAN: show shadow DIV that is used for calculating the caret coordinates; this will also include the created DIV in the coordinates-object in property `_div` (DEFAULT: false)
> 		- options.reuse	BOOLEAN: reuse shadow DIV that is used for calculating the caret coordinates (DEFAULT: false)
> 		- options.returnDiv	BOOLEAN: if reuse was enabled, returns the shadow DIV in the coordinates-object in property `_div` (DEFAULT: false)
> 		- options.returnHeight	BOOLEAN: returns the caret height in the returned coordinates-object in property `height` (DEFAULT: false)
> 		- options.id		STRING: the id attribute for the shadow DIV (DEFAULT: "input-textarea-caret-position-mirror-div")
> 		- options.guessIfUpdateStyle	BOOLEAN | FUNCTION: if TRUE, styling of the shadow DIV is not updated, if the current target element has the same type (Tag Name) as the previous one.
> 															If function: a callback for determining, if the shadow DIV's style should be updated (return TRUE, if it should get updated): `callback(shadowDiv) : boolean`
> 															NOTE this option is only relevant, if "reuse" is TRUE.  
> 															(DEFAULT: false)
> 		- options.forceUpdateStyle	BOOLEAN: force updating the style of the shadow DIV; only relevant, if "reuse" is TRUE (DEFAULT: false)
> 		- options.forceClearFauxStyle	BOOLEAN: force faux span to use "cleared" style (e.g. in case SPAN is globally styled) (DEFAULT: false)
> 		- options.fauxId				STRING: use ID for faux span (e.g. for styling faux span) (DEFAULT: undefined)
> 		- options.fontZoom			NUMBER | BOOLEAN: apply zoom factor to font-size.
> 															 If `true` (boolean) the zoom factor will be calculated using `measureFontZoom()`, and the option-value
> 															 (`true`) will be replaced with the measured zoom factor.
> 															 (DEFAULT: undefined)
>
> 		- options.additionalStyles	ARRAY<STRING>: transfers additional styles properties from the target element to the shadow DIV
>
> 		- options.text STRING | FUNCTION: the text value that should be used for the calculation.
> 															 If function: a callback which's return value is used as the text: `callback(element, options) : string`
>


### var coordinates = getCoordinates(element, position)

`position` is an integer indicating the location of the caret. You basically pass `this.selectionStart` or `this.selectionEnd`. This way, this library isn't opinionated about what the caret is.

`coordinates` is an object of the form `{top: , left: }`.

## Known issues

* Tab characters in `<textarea>`s aren't supported in IE9 (issue #14)

## Dependencies

None.

## TODO

* Add tests.
* Consider adding [IE-specific](http://geekswithblogs.net/svanvliet/archive/2005/03/24/textarea-cursor-position-with-javascript.aspx) [code](http://stackoverflow.com/questions/16212871/get-the-offset-position-of-the-caret-in-a-textarea-in-pixels) if it avoids the necessity of creating the mirror div and might fix #14.
* Test IE8 support with `currentStyle`.

## Implementation notes

For the same textarea of 25 rows and 40 columns, Chrome 33, Firefox 27 and IE9 return completely different values
for `computed.width`, `textarea.offsetWidth`, and `textarea.clientWidth`. Here, `computed` is `getComputedStyle(textarea)`:

Chrome 33
* `computed.width `: "240px" = the text itself, no borders, no padding, no scrollbars
* `textarea.clientWidth`: 280 = computed.width + padding-left + padding-right
* `textarea.offsetWidth`: 327 = clientWidth + scrollbar (15px) + border-left + border-right

IE 9: scrollbar looks 16px, the text itself in the text area is 224px wide
* `computed.width`: "241.37px" = text only + sub-pixel scrollbar? (1.37px)
* `textarea.clientWidth`: 264
* `textarea.offsetWidth`: 313

Firefox 27
* `computed.width`: "265.667px"
* `textarea.clientWidth`: 249 - the only browser where textarea.clientWidth < computed.width
* `textarea.offsetWidth`: 338


## Contributors

* Dan Dascalescu ([dandv](https://github.com/dandv))
* Jonathan Ong ([jonathanong](https://github.com/jonathanong))
