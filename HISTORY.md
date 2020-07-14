

1.0.0 / 2020-07-14 [text-caret]
==================

first version for renamed module `text-caret-pos`

 * module as UMD version and additional options
 * restructured project
 * transfer HTML attributes scrollLeft, scrollTop, dir to mirror DIV and added option additionalAttributes
 * FIX handle word-wrapping for INPUT by resetting word-wrap style on mirror-DIV in order to avoid inherited word-wrapping for INPUT
 * added option for allowing to transfer additional styles to shadow DIV
 * added option for returning the caret height
 * improved options for fontZoom and text

 * (manually) applied changes/bugfixes from `text-caret-position` version 3.1.0
   * use test page from from `text-caret-position` version 3.1.0
   * applied BUGFIX "Add border-box support for input type="text" "  
     https://github.com/component/textarea-caret-position/pull/50
   * added field `height` in returned measurement
     https://github.com/component/textarea-caret-position/pull/38
   * support inputs taller than line-height
     https://github.com/component/textarea-caret-position/pull/37
   * apply BUGFIX "window. bug fix"
     https://github.com/component/textarea-caret-position/pull/33
     * extracted access to `window.getComputedStyle` to helper function

3.0.0 / 2015-03-21 [text-caret-position]
==================

 * fix Firefox support
 * remove Meteor support for now
