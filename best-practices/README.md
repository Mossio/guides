Best Practices
==============

A guide for programming well.

General
-------

* Don't write code that guesses at future functionality.
* [Exceptions should be exceptional].
* [Keep the code simple].

[Exceptions should be exceptional]: http://www.readability.com/~/yichhgvu
[Keep the code simple]: http://www.readability.com/~/ko2aqda2


HTML
----

* Don't use a reset button for forms.
* Prefer cancel links to cancel buttons.
* Use HAML or ERB in development

CSS
---

* Use Sass.

Sass
----

* Use `image-url` and `font-url`, not `url`, so the asset pipeline will re-write
  the correct paths to assets.

Browsers
--------

* Don't support clients without Javascript.
* Don't support browsers more than two versions old.
