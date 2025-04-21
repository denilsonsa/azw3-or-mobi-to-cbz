# azw3-or-mobi-to-cbz

A converter from [AZW/AZW3][azw3] and [MOBI][] fixed-layout comic book e-books to [CBZ][].

## Motivation

I got my own library of DRM-free `*.azw3` files that I got from my Amazon
Kindle and [ComiXology][] account. (Getting such files and removing the DRM
from them is outside the scope of this repository.) Since the [AZW3][] format
is very similar to the [Mobipocket][mobi] format, many e-book readers can open
them.

Unfortunately, although most e-book readers can open such files, very few of
them support fixed-layout e-books. This means any comic book becomes basically
unreadable.

My first idea was to use [Calibre][] to convert those formats to some other
format, such as epub or PDF. Unfortunately, [Calibre also doesn't support
fixed-layout e-books][nope], and the conversion leads to files with a
completely broken layout. It seems like supporting fixed-layout e-books isn't
in the current plans for the Calibre project. (But if you now how to code and
want to implement it yourself, I believe the project will accept your
contributions.)

People on reddit [(1)][reddit1] [(2)][reddit2] suggested a workaround: use
Calibre to convert to ZIP, and then rename the ZIP to CBZ. Although it can
work, we also lose all the metadata. And we first have to figure out which of
the files are fixed-layout, and only convert those. While this manual
workaround could work on a couple of files, it requires too many manual steps
to be doable for a library of thousand(s) of files. I needed a better tool. So
I wrote it myself.

## How it works (and limitations)

The most accurate way to do a conversion from a fixed-layout e-book to
something else is by actually rendering the e-book, or by at least having a
full HTML+CSS+SVG rendering engine. Having such engine would allow for properly
rendering absolutely-positioned text on top of a background image.

This project doesn't do that.

Having a full rendering engine is a lot of work and comes with a lot of extra
challenges as well. Instead, this project takes shortcuts. Many shortcuts.

Instead of having a proper interpreter for HTML+CSS+SVG semantics, this project
just looks at the linked images from the e-book source code. This project
assumes that most comic books will have one single image per page, and thus
this project looks over each page to find the name of such image, blindly
ignoring all the other markup and styles.

This works surprisingly well. Most comic books can be correctly converted using
this naïve approach.

Some comic books have two images per page, because they include the left and
right pages together in a single page. This project also handles that.

Then, after collecting the list of the images (per page), this project creates
a zip file with the CBZ extension adding all those images, in the order they
were found, while also adding a simple [comicinfo.xml][comicinfo] generated
from the book metadata. That's all. You now understand what this project does,
what it can do, and what it can't do.

In summary:

* Reflowable e-books: not supported.
* Fixed-layout e-books:
    * With a single image per page: supported.
    * With two images per page: supported.
    * With multiple images per page: converted, but with warnings. The result is probably wrong.
    * With text overlaid on top of images: not supported. They still get converted, but the result will lack any text.

After converting the e-books, you may want to manually check those that had any
kind of warning. Those may have been converted incorrectly, and they may need
manual fixing, or they may be impossible to losslessly convert to CBZ.

## Future of this project

This is a side-project I wrote myself to scratch my own itch. I wrote it during
the rare spare time I had. I've used it once, and I don't plan on using it
again.

I have no plans on supporting this project. Feel free to use it for whatever
purposes you want, but I won't provide any support. It's unlikely I'll ever
touch this code again. In fact, I may archive this repository to reflect this
status.

That said, I used it once and it indeed worked. It successfully converted over
a thousand comic book e-books I had. It may work for you. It may also require
some tweaks before it works for you. In any case, it can be helpful to someone
else, and that's why I'm sharing it.

## Prerequisites

* You are comfortable with a little bit of coding.
* You have a recent version of [Python][] on your computer. If you are running
  Linux or Mac OS X, you probably already have.
* You can [install JupyterLab or Jupyter Notebook][jupyterinstall] and you are
  [comfortable][jupyterdocs] using it.
* If you don't know [virtual environments][venv], it may be worth learning how
  to use them before installing Jupyter or any other Python dependency.
* You have a bunch of `*.azw3` files (or `*.mobi`) without any DRM. Removing
  DRM from your own e-book library is left as an exercise to the reader.

## Getting started

Assuming you have both Python and Jupyter already installed, possibly inside a
virtual environment, please also install the dependencies of this project:

    pip install -r requirements.txt

Now open the Jupyter notebook (the file with `.ipynb` extension) and follow the
instructions over there.

## Related projects

* [mobi](https://github.com/iscc/mobi) ([at
  PyPI](https://pypi.org/project/mobi/): the library I used to extract the
  files from the e-books. Please note it will extract the files to a temporary
  directory, and that directory is deleted after the conversion is finished.
* [CbzMage](https://github.com/ToofDerling/CbzMage): Claims to convert azw and
  pdf comic books to cbz files. I have not tested this project, I cannot tell
  you anything else about it.

[ComiXology]: https://en.wikipedia.org/wiki/ComiXology
[cbz]: https://en.wikipedia.org/wiki/Comic_book_archive
[mobi]: https://en.wikipedia.org/wiki/Mobipocket
[azw3]: https://en.wikipedia.org/wiki/Kindle_File_Format
[calibre]: https://calibre-ebook.com/
[nope]: https://www.mobileread.com/forums/showthread.php?t=328681
[reddit1]: https://old.reddit.com/r/Calibre/comments/111umvj/comic_book_azw3_convert_to_pdf_weird_margins/
[reddit2]: https://old.reddit.com/r/ereader/comments/hdqip9/how_to_convert_kindle_ebook_manga_to_cbrcbz/
[comicinfo]: https://github.com/anansi-project/comicinfo
[python]: https://www.python.org/
[jupyterinstall]: https://jupyter.org/install
[jupyterdocs]: https://docs.jupyter.org/
[venv]: https://docs.python.org/3/library/venv.html
