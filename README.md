# A New Standard for Website Icons

[Read the proposed standard](standard.md)!

## Motivation for a website icon standard

The world is rather used to favicons now, that they are part of the web for
over 20 years.
While the good old favicon.ico itself can be used with no additional
configuration by placing it in the docroot of a web server, the same is not
true anymore for the lots of new icon formats, that newer web features
require.

To grasp a hold of the ever-changing landscape of icons, that are needed to
represent a web site (or single web page) on all devices and in all situations,
one needs to do extensive research.
[Audrey Feldroy](https://github.com/audreyfeldroy/favicon-cheat-sheet) did that
and in the course a well-researched but very long article emerged for people
to learn the dark art of the website icons.
On the other end of the spectrum are great tools like [Real Favicon
Generator](https://realfavicongenerator.net/), that try to minimize the need
for a single developer to understand the technical details.

[Andrey
Sitnik](https://evilmartians.com/chronicles/how-to-favicon-in-2021-six-files-that-fit-most-needs)
tried to boil down icon markup to the absolute minimum in order to support only
the most popular devices.
This is only marginally similar to what [Mathias
Bynens](https://mathiasbynens.be/notes/touch-icons) suggested 10 years ago on
the same issue, with many changes rather subtle for people not into the
research.
And it is far from the humble beginnings of favicons, as [the W3C
suggested](https://www.w3.org/2005/10/howto-favicon) in 2005.

In a nutshell, it’s a mess.

In this situation Jeff Starr presents the need for a favicon standard
eloquently in a [blog post](https://perishablepress.com/favicon-standard/).
This was the starting point for creating this proposed standard.

The target is to get back to the sweet spot between configuration and
convention, where the most common cases can be handled with zero extra
markup.

Therefore we piggyback on RFC 8615, that standardizes the `/.well-known/`
folder as something, that hosts files by convention.

## How does it work?

The basic idea is simple: If a vendor needs a specifically sized icon for their
platform, they register a name for it.
This registry serves as central place to find out, which icons we need.

For example, imagine PearGlyph Inc. introduces a new device, the TouchMagic
tablet. For progressive web apps it needs icons in a pixel format, that are
exactly 272 pixels wide. PearGlyph Inc. would then register the following
icon names:

    pearglyph-touchmagic-272.jpg
    pearglyph-touchmagic-272.png

If we want to support the TouchMagic device, we put an icon with such a name
in the folder `/.well-known/icons/` in our docroot.

The standard adds a short list of default icon names, too.
Most prominently, it introduces

    /.well-known/icons/favicon.svg

as default favicon to be used.
The reasoning is, that using a vector format like SVG satisfies the most needs
in parallel.

How does a client find icons? In order not to fish in muddy waters and send
requests for potentially hundreds of matching icon names the standard demands
a file `index.txt`, that is a plain-text list of supported icons.

Such a list for sysadmins can be compiled rather quickly by running the command

    find -L . -type f -printf '%P\n' > index.txt

in a shell in the `/.well-known/icons` folder (on systems, that understand the
`-printf` flag of `find`).

## How to change icons quickly?

This is something the standard also strives to make as painless as possible.
You can place a _set_ of different icons in a sub-folder of
`/.well-known/icons`.

Let’s take a common use case: You want a different favicon on the development
installation of your site from your live site.
The solution with the website icon standard is to create a folder

    /.well-known/icons/dev/

and place there a development version of your favicon.
In the HTML files you activate the set with a single line,

    <meta name="icon-set" content="dev">

This is all.
Now your dev server reminds you reliably by means of a favicon.

## Who are we?

We are developers and designers who want to change the status quo of website
icons. We want to make all our lives easier by adding opt-in conventions for
the ever-expanding use of favicons.

The standard was initiated by [Manuel Strehl](https://manuel-strehl.de).

Come aboard! Make your opinion and experience heard by [opening a new
issue](https://github.com/website-icon/standard/issues/new/choose) or [joining
a discussion](https://github.com/website-icon/standard/discussions).

### Websites that already support the website icon standard

| icon | website |
| ---- | ------- |
| ![](https://hyperform.js.org/.well-known/icons/icon-144.png) | hyperform.js.org ([`index.txt`](https://hyperform.js.org/.well-known/icons/index.txt)) |
| ![](https://manuel-strehl.de/.well-known/icons/icon-144.png) | manuel-strehl.de ([`index.txt`](https://manuel-strehl.de/.well-known/icons/index.txt)) |

Add your site: Open an issue or send a pull request with the link to your
website and to the icon.

<!--
https://css-tricks.com/favicon-quiz/
https://stackoverflow.com/questions/48956465/favicon-standard-2021-svg-ico-png-and-dimensions
-->
