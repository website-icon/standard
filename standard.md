# Website Icon Standard

_Version: 0.0.1, 2021-04-21_

Authors:

* Manuel Strehl, Kinetiqa GmbH

## Abstract

The use of icons to represent websites expanded in many different ways since
the introduction of the favicon in 1999.
This created the need to announce supported icons by various means, most
famously with `<link>` elements in the head of HTML files.

The aim of this standard is to reduce the need for markup for website icons as
far as possible.
Therefore it provides conventions that base on the proven `/favicon.ico`
mechanism but extend it for current and future multi-platform needs.

## Definitions

The key words “MUST”, “MUST NOT”, “REQUIRED”, “SHALL”, “SHALL NOT”, “SHOULD”,
“SHOULD NOT”, “RECOMMENDED”,  “MAY”, and “OPTIONAL” in this document are to be
interpreted as described in [RFC 2119](https://tools.ietf.org/html/rfc2119).

The path prefix `/.well-known/` is defined in [RFC
8615](https://tools.ietf.org/html/rfc8615).
The website icon standard strives to register a normative “Well-Known URI”
`icons`.

A **website icon** is a visual representation of a website.
It SHOULD be used by the user agent when representing the page in the user
interface.
This definition matches the one from the [HTML 5
standard](https://html.spec.whatwg.org/multipage/links.html#rel-icon).

An **icon set** is a matching set of icons to together represent a specific
resource.

## Location of Website Icons

Website icons are located in the `/.well-known/icons` folder in the docroot of
a web server.

### Conventions for File Names

The file names are relative to the `/.well-known/icons/` folder.

* the default favicon in the format SVG is named `favicon.svg`.
* the default favicon in the format ICO for backwards compatibility is named
    `favicon.ico`.
* icons in a pixel-based image format are named `icon-SIZE.EXT`.
* icons in a scaling image format are named `icon.EXT`.
* a platform-specific icon is named `VENDOR-PLATFORM[-SIZE].EXT`.
    The components `VENDOR`, `PLATFORM`, `SIZE` and `EXT` are defined in the
    registry such that the possible combinations of `SIZE` and `EXT` for a
    given `VENDOR`-`PLATFORM` set do not exceed 24.
* the special values `default` and `icon` for the `VENDOR` component are
    reserved for future extensions.
* the plain-text list of available icons is named `index.txt`.
    Every line in this file that doesn’t start with the character `#`
    represents a URL with base `/.well-known/icons/`.
* the server MAY provide an `index.html` file with human-readable information
    about the icons.
* the folder MAY contain other files, provided that their file names do
    not conflict with registered file names of the website icon standard.

Sizes MUST match `WIDTH "x" HEIGHT`, where `WIDTH` and `HEIGHT` are integers.
In the case of square images the second part `"x" HEIGHT` MUST be omitted.

File extensions MUST be representative of their content type.
A file ending in `.png` must be of type `image/png` and so on.
[TODO #3]

If several names can be satisfied by the same content, a single file with this
content MAY be symlinked or otherwise made available by all those names.

### Formal Syntax

The formal ABNF syntax for file names defined in this standard is as follows:

```
FILENAME = favicon / icon / vendor-icon

favicon = "favicon." EXT

icon = "icon" [ "-" SIZE ] "." EXT

vendor-icon = VENDOR "-" PLATFORM [ "-" SIZE ] "." EXT

EXT = 1*( ALPHA / DIGIT )

SIZE = WIDTH [ "x" HEIGHT ]
; where HEIGHT must be different from WIDTH

VENDOR = 1*( ALPHA / DIGIT / "_" )

PLATFORM = 1*( ALPHA / DIGIT / "_" )

WIDTH = 1*DIGIT

HEIGHT = 1*DIGIT
```

`ALPHA` and `DIGIT` match the respective rules from [RFC
5234](https://tools.ietf.org/html/rfc5234).

## Icon Sets

Icon sets differing from the default set are bundled in sub-folders.
The file names as specified above are relative to the sub-folder inside
`/.well-known/icons/` with the same name as the set.

For example, a set of icons for a photo gallery page can reside in
`/.well-known/icons/gallery/`. The icon set is named `gallery`.

Every icon set folder MUST at least contain its own `index.txt` file with URLs
relative to itself and either a `favicon.svg` or `favicon.ico` image.

### Choosing a Set

An HTML page can choose to select an icon set by using the following line in
the `<head>` of the document:

    <meta name="icon-set" content="SET-IDENTIFIER">

The value for `SET-IDENTIFIER` MUST be a valid, case-sensitive folder name,
that exists under `/.well-known/icons/`.
If `SET-IDENTIFIER` is the empty value or a single U+002E dot, the default set
MUST be used.
Two U+002E dots in row MUST NOT appear.
U+002F slashes MAY appear, if the resulting `SET-IDENTIFIER` is still a valid
path under `/.well-known/icons/`.

If the value for `SET-IDENTIFIER` contains characters, that are not valid in
a URI, the client MUST use the percent-escaped UTF-8 encoded representation of
the identifier string for requests in accordance with [RFC
3987](https://tools.ietf.org/html/rfc3987).

If an icon set is chosen in this way, auto-detected URLs as described in the
next chapter are searched in `/.well-known/icons/SET-IDENTIFIER` instead of
`/.well-known/icons/`.

## Auto-Detection

A user agent SHOULD load the file `/.well-known/icons/favicon.svg`, if it needs a
visual representation, e. g. as favicon, for the current website.
If this request returns a HTTP 404 status, it MAY load the file
`/.well-known/icons/favicon.ico`.

A user agent or other client MAY try to load the file
`/.well-known/icons/index.txt` for a list of available icons.

A user agent or other client MAY try to load a specific icon for a
`VENDOR-PLATFORM[-SIZE].EXT` combination without looking up `index.txt` first,
but MUST stop, if it finds no suitable candidate after three tries.

Clients MUST follow HTTP redirects from URLs within the
`/.well-known/icons/` folder to URLs with the same origin.
Clients SHOULD follow redirects to a resource with a different origin, if this
resource is not deemed a security or privacy threat.

If a client requests `/favicon.ico` from the docroot and is redirected to
either `/.well-known/icons/favicon.svg` or `/.well-known/icons/favicon.ico`,
it SHOULD assume that the server supports the website icon standard.

## Requirements for Conformity

A **server** conforming to this specification MUST provide either a
`favicon.svg` or a `favicon.ico` file and a file `index.txt` under the path
`/.well-known/icons/`.
The server MUST provide these files also for every icon set in use (i.e.,
sub-folders of `/.well-known/icons/`).

Lines in `index.txt` MUST NOT start with a U+002F slash.

All other features of this specification are optional.

A **client** conforming to this specification MUST adhere to the limits and orders
specified in the auto-detection section above, if it opts into loading icons.

It MUST ignore entries in `index.txt` that contain a U+002F slash anywhere.
This includes in particular the icon selection, when a matching icon is found
by its base name in a sub-folder of `/.well-known/icons/` but no matching icon
set is requested.
This icon MUST be ignored.

## Using Icons

### Backwards Compatibility

Currently, user agents automatically load a file `/favicon.ico`.
This request can be symlinked or redirected to
`/.well-known/icons/favicon.ico`.

Special icons, that are currently loaded automatically, can be handled in the
same way.
For example, `/apple-touch-icon.png` can be symlinked to
`/.well-known/icons/apple-touch-180.png`.

HTML files can point directly to any icon by including the URL as `<link>`
element in the `<head>`:

    <link rel=icon href="/.well-known/icon/special_icon.png" sizes="57x57" type="image/png">
    <link rel=icon href="/.well-known/icon/my_logo.svg" type="image/svg+xml">
    <link rel="shortcut icon" href="/.well-known/icon/favicon.svg" type="image/svg+xml">

The same mechanism also allows backwards-compatibility with the icon set
feature:

    <link rel="shortcut icon" href="/.well-known/icon/gallery/favicon.ico">

### Versioning Icons

Icons SHOULD be cached by user agents independently from the actual HTTP
headers.
To force re-downloads, the icon set feature can be used.
By incrementing a counter the user agent knows, that the icon might have
changed:

    <meta name="icon-set" content="v3.2.0">

The server can then be instructed to ignore such set prefixes when serving the
request for `/.well-known/icons/v3.2.0/favicon.svg` and instead serve the
file under `/.well-known/icons/favicon.svg`.
Icon sets used in this way resemble the use of so-called cache-busting strings
in URLs.

### Dynamic Icons

This standard does not impose any restrictions on user agents with regard to
dynamically changing icons.
This includes setting a `<link rel=icon>` or, in conforming user agents, a
`<meta name=icon-set>` via JavaScript.

User agents MAY allow to change favicons or other icons by these means.

Servers MAY serve dynamic icons based on request details, e.g. user
sessions.
Caution must be exercised to handle client caching of icons correctly.

### Folder-Based Shared Hosting

Some institutions, like universities, offer private web space under
certain paths, e.g., `/~user-id/`. The website icon standard itself offers no
special mechanism to address differing icons based on sub-paths.

There are several options, though, for path-based adaption of icons.
The most straight-forward is to explicitly add icon paths to the mark-up, as
would already be necessary today.
In the absence of such mark-up today’s behaviour is already to load
`favicon.ico` from the docroot.

One other option can be enabled by the server admin by leveraging the icon set
feature.
A redirect from `/.well-known/icons/user-id/` to, e.g., `/~user-id/icons/`
allows users to switch to their own set of icons with a single line in the HTML
`<head>`:

    <meta name="icon-set" content="user-id">

## Interaction with Other Standards

### Icons in Web Manifests

The [Web Application Manifest Specification](https://www.w3.org/TR/appmanifest/#icons-member-0)
also allows defining a list of icons.
Icons can be classified by their size and their
[purpose](https://www.w3.org/TR/appmanifest/#dfn-icon-purposes).

The website icon standard does not specify explicit meta-documents, that serve
the same purpose.
However, icons named after the scheme layed out above can be referenced in a
web application manifest.
Size and purpose can furthermore be coded in the `PLATFORM` and `SIZE`
identifiers of the naming scheme.

### Setting a Color

This standard explicitly excludes other meta-data about websites than icons.
One closely related item is a theme color, that must sometimes be chosen
together with a matching icon.

For defining a theme color, we refer to the [Web Application
Manifest Specification](https://www.w3.org/TR/appmanifest/#theme_color-member),
that already offers this possibility.
By referencing different manifests from different HTML pages the theme colors
can also be adapted to different icon sets as defined in this document.

### The HTML Living Standard

The [HTML Standard](https://html.spec.whatwg.org/multipage/links.html#rel-icon)
specifies loading `/favicon.ico` as fallback in the case, where an
HTML document contains no `link@rel=icon` element.
Given reasonable browser support for the website icon standard eventually, we
suggest updating the HTML Standard to consider the behaviour laid out in this
document instead.

HTML defines the `sizes` attribute to denote an icon’s dimensions.
The website icon standard deviates deliberately from the definition in two
major ways.
For one, HTML allows both lower-case “x” and upper-case “X” as separators
between width and height. Then, the declaration of the height is always
mandatory.

These properties are not adopted here in order to make URLs both as concise and
predictable as possible.

### RFC 8288 “Web Linking”

[RFC 8288](https://tools.ietf.org/html/rfc8288) defines possibilities to link
resources on the web.
The website icon standard has no effect on this mechanism but provides a
supplemental means of receiving a specific piece of meta-data for a site by
leaning on convention instead of explicit linkage.
In this way both standards complement one another.

## Registering Vendor Tokens

The registry of known token combinations for `VENDOR`, `PLATFORM`, `SIZE`, and
`EXT` is located at [TODO #1].
A new registration request can be issued by following the instructions on that
page.

A successful registration request MUST contain at least the following
information: [TODO #2]

* **contact address**: person or company who is registering and will be allowed
    to request changes later on.
* **`VENDOR`** token, that matches the syntax from the chapter Formal Syntax.
* **`PLATFORM`** token, that matches the syntax from the chapter Formal Syntax.
* supported **`EXT`** extensions.
* if `EXT` contains size-dependent formats, supported **`SIZE`** parameters.
    The number of possible `SIZE` and `EXT` combinations MUST NOT exceed 24.
* any geometric restrictions or graphic alteration, that will be applied.
    Examples are “will be rendered in a circle shape” or “will have a gloss
    effect applied”.

    Complex mask shapes MUST be given in the form of an SVG graphic, that can
    be applied as mask.

Additional information, like developer documentation or references, can be
submitted.

The registry will also contain the date of the registration request.

## Security Considerations

Malicious actors can dynamically alter favicons in such a way, that users are
tricked into believing they are on a different site.
Examples are changing favicon and page title after some idle time, when the
user might be distracted.

A similar attack can be executed by using icons, that are not immediately
visible to the user.
If a Progressive Web App offers a desktop icon and label that suggest a
different site, the user might not notice the installation.
They will in a later session click the desktop icon oblivious to the fact that
the content is served by another site.

Long-lasting favicon cache entries can be used to finger-print user agents.
If cache entries are re-used in private browsing sessions, this can lead to
inadvertently exposing users.

Some image formats include extended scripting or animation features.
These features might be unnecessary when used in icons.
Disabling them reduces attack surfaces by removing entry points for script
execution, e.g. JavaScript in SVG, or timing attacks, e.g. by high-fidelity
animation timing.

If a server redirects an icon request to another origin, loading the redirected
URL might expose details of the current user.
Malicious actors could also try to exploit a bug in a specific user agent’s
image handling by redirecting to a server under their control.
User agents need to take care, that as little information as possible is handed
on to the external icon server.

## References

### Normative References

* [RFC2119] Bradner, S., “Key words for use in RFCs to Indicate Requirement
    Levels”, [BCP 14](https://tools.ietf.org/html/bcp14),
    [RFC 2119](https://tools.ietf.org/html/rfc2119), March 1997.
* [RFC8615] Nottingham, M., “Well-Known Uniform Resource Identifiers (URIs)”,
    [RFC 8615](https://tools.ietf.org/html/rfc8615),
    May 2019.
* [HTML] van Kesteren, A., Denicola, D., Hickson, I., Jägenstedt, P.,
    Pieters, S., “[HTML standard](https://html.spec.whatwg.org/)”,
    WHATWG, Living Standard.
* [RFC5234] Crocker, D., Ed., Overell, P., “Augmented BNF for Syntax
    Specifications: ABNF”, [RFC 5234](https://tools.ietf.org/html/rfc5234),
    January 2008.
* [RFC3987] Duerst, M., Suignard, M., “Internationalized Resource Identifiers
    (IRIs)”, [RFC 3987](https://tools.ietf.org/html/rfc3987), January 2005.
* [RFC8288] Nottingham, M., “Web Linking”, [RFC
    8288](https://tools.ietf.org/html/rfc8288), October 2017.
* [WebApp] Cáceres, M. _et al._, “[Web Application Manifest Specification](https://www.w3.org/TR/appmanifest/)”,
    W3C Working Draft, April 2021.

### Informative References

## Appendix

### Example Structure

The following diagram shows a minimal folder structure for a valid server
set-up:

    .well-known/
        icons/
            favicon.svg
            index.txt

The file `index.txt` contains the single line `favicon.svg`.

Here is a more complete example with some commonly needed vendor versions:

    favicon.ico -> .well-known/icons/favicon.ico
    apple-touch-icon.png -> .well-known/icons/apple-touch-180.png
    .well-known/
        icons/
            favicon.ico
            favicon.svg
            index.txt
            index.html
            icon-192.png
            icon-310x150.png
            android-icon-192.png -> icon-192.png
            apple-touch-180.png
            ms-square_tile-150.png
            ms-wide_tile-310x150.png -> icon-310x150.png
            safari-mask.svg
            webapp-icon-192.png -> icon-192.png
            webapp-splash-512.png
            webapp-splash.svg

The file `favicon.svg` will be loaded by conforming clients.
Fallback behaviour is implemented with symlinks.
The vendor prefix `webapp` denotes icons used in the web app manifest of the
site.

Note, that all vendor and platform strings in this example are still
preliminary and likely to change.

A third example uses the icon set feature:

    .well-known/
        icons/
            favicon.svg
            index.txt
            tulips/
                favicon.svg
                index.txt
            roses/
                favicon.svg
                index.txt
                webapp-icon-192.png
            hyacinths/
                favicon.ico
                index.txt

This structure offers one default favicon and three distinct icon sets `tulips`,
`roses`, and `hyacinths`.
Each set is in itself a complete implementation of the website icon standard.
