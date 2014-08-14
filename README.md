Angular-and-IE8-guide
=====================

This is a collection of gotchas and pitfalls which I have come across while developing web apps for IE8, for both my own reference and for others.

- First of all, make sure to go through [Angular's IE guide](https://docs.angularjs.org/guide/ie).

- jQuery might need to be included conditionally on IE8 (it's usually not needed with Angular, as jQLite is sufficient), to make Angular work. See [StackOverflow thread](http://stackoverflow.com/questions/20556102/angular-and-ie8-html5-elements-not-styled-inside-ng-view).
  Also keep in mind that jQuery [dropped support for IE8 since 2.x](http://stackoverflow.com/questions/18327225/object-doesnt-support-addeventlistener-ie8-in-jquery),
  so use the latest 1.x version.

- An HTML5 shim needs to be included to support styling HTML5 elements. This alone
  is not enough however, as [HTML5 elements inside `ng-repeat` will still be
  rendered incorrectly](https://github.com/angular/angular.js/issues/1381). This
  can be fixed by adding `\:section` style elements to the HTML5 CSS shim where new
  elements are set to `display: block`, because on IE8 inside `ng-repeat`, unknown
  tags will be prefixed with a colon. See [Problem 3 in this blog post](http://blog-it.hypoport.de/2013/08/24/how-to-make-your-angularjs-app-work-in-ie-8/)
  for details.

- CORS is not supported on IE8, so any setup where a static site consumes a separate
  API will need some sort of proxying or other trick to make it look like requests
  are going out to the same domain. See also [IE8 and CORS](http://mcgivery.com/ie8-and-cors/).

- SVG is not supported on IE8, which is why [Raphael.js](http://raphaeljs.com/) is a much nicer
  alternative to [D3.js](http://d3js.org/).

- When encountering weird rendering issues or missing content on IE8, I suggest
  first looking for any mismatches in HTML tags. Modern browsers are pretty
  forgiving if your `<span>` tags close with an `</a>` by accident, but IE8
  will break horribly and give little indication as to why.

- If there are no unclosed tags, search for trailing commas in the javascript;
  for example, IE8 will break if it sees the following:

  ```js
  angular.module("some.widget", [
  "dependency.one",
  "dependency.two",
  ])
  ```

- <http://modern.ie> provides free VMs for testing on IE. When testing in IE8
  mode on IE10 or IE11, you don't need any special setup for the stylesheets,
  however when testing on a Windows XP VM with an actual native IE8 instead of
  just IE8 mode, the browser will be a lot stricter, enforcing (limits to selectors
  and imports](http://blogs.msdn.com/b/ieinternals/archive/2011/05/14/10164546.aspx). 
  Therefore, when testing, it's easiest to use a version of the build which 
  minifies styles.

  Also, you *should* test the site on actual IE8, not just IE8 mode, as several
  other appear on the actual browser that don't show up in IE8 mode, such as the `\:selector`
  rule for `ng-repeat`. If not for anything else, opening your app on IE8 will at least
  make you aware of just *how* slow the browser actually is.
  
### Styling workarounds

- Gradients on IE8 are doable with filters, however when any of the colours used
  in the gradient is transparent, mouse clicks will not be registered on the gradient.
  See [No transparency click bug](http://haslayout.net/css/No-Transparency-Click-Bug).
  I guess the browser thinks since the element is half-transparent, clicks should go "through" it.

  The solution is to add a fake background image to make the browser think the element
  has a solid background which captures clicks, for example `background: url(#)`.
  
  Have a look at the Sass mixin I wrote which encapsulates all of the gradient
  workarounds: [Sass mixin for IE transparency](https://gist.github.com/elisehein/b65a653b7ad277a6bde6)

