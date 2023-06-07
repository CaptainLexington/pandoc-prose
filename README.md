pandoc-novel
=============

This is a template project for generating a novel or story collection from markdown files and building an ePub file, a SFFMS format [manuscript](http://www.sfwa.org/wp-content/uploads/2009/06/Mssprep.pdf) PDF, and an electronic-submission-friedly DOCX file using [Pandoc](https://pandoc.org), [LaTeX](https://www.latex-project.org/get/), and [GNU Make](https://www.gnu.org/software/make/).   This project is fork of [pandoc-novel](https://github.com/jp-fosterson/pandoc-novel), upated for the needs of modern writers who stil seek traditional publication. For the time being I have deprecated print-ready PDF support because the createspace package on which it depends is weird and old and hard to set up.

Well formatted ePub should be enough for publishing ebooks books with [Amazon KDP](https://kdp.amazon.com), [Barnes and Noble Press](https://press.barnesandnoble.com), [Smashwords.com](https://smashwords.com), and other self-publishing outlets.

You can see example output for [ePub](doc/example.epub), [DOCX](doc/example.docx) and [manuscript PDF](doc/example-ms.pdf).

Set Up
-------

### Install prequisites

* [Pandoc](https://pandoc.org)
* [LaTeX](https://www.latex-project.org/get/)
* the [`sffms` package](https://texdoc.org/serve/sffms_manual.pdf/0) for formatting a manuscript-format PDF.

### Clone this repo

    git clone git@github.com:jp-fosterson/pandoc-novel.git

...or fork the repo (it's a template), and clone your own copy.

### Try it out

    cd pandoc-novel
    make

This will build three targets `out/example.epub`, `out/example.docx`, and `example-ms.pdf`.  Open them in your favorite viewers. 

If you're on macOS  `make view-epub` will attempt to build the `epub` target and open the file in [Calibre's](https://calibre-ebook.com) ebook viewer component (without actually adding the book to your library).  Likewise `make view-pdf` will build the `pdf` target and open it with the default PDF viewer (probably `Preview`).  If you're not on macOS, you can edit these targets in the `Makefile` to open the documents in your favorite viewers.

Writing
--------

### `text/`

The most important part of the novel, the text, goes in markdown-format chapter files in the `text/` directory.  Aside from the `.md` extension, there are no particular requirements about the file names, chapter ordering will be defined in the [`Makefile`](/Makefile) (see below).  Chapter titles should be indicated with a `H1`-level header.  If you wish your chapters to be unnumbered, tag them with `{.unnumbered}` after the title.   Backmatter such as acknowledgements and an author bio should just be included as `{.unnumbered}` chapters; they will be indicated in the [`Makefile`](/Makefile).

### `Makefile`

Set the `SLUG` variable in the Makefile to change the prefix used on the output files

```
#
# Slug: filename prefix that will be used for the generated
# files. e.g. example.epub and example.pdf, plus intermediate
# files like example.tex
#
SLUG=example
```


To define the chapter ordering add the chapter files, in order, in the `CONTENTS` and `BACKMATTER` variables in [`Makefile`](/Makefile):

```
#
# The main text of the book. This sequence determines the
# chapter order.
#
CONTENTS = \
	text/beginning.md\
	text/middle.md\
	text/end.md\
	\
#
# The stuff in the back, not part of the story.
#
BACKMATTER = \
	text/ack.md \
	text/about.md\
	\
```

### `metadata.yaml`

The title, author, copyright notice, ePub cover image, and other important metadata are defined in [`metadata.yaml`](/metadata.yaml).

###  `make` targets

* `make` or `make all` --- makes the main ePub and PDF targets in `out/`.
* `make docx` -- builds the submission-ready book DOCX.
* `make epub` --- builds the epub book.
* `make ms` --- builds the manuscript-format PDF.
* `make unzip` --- Builds the ePub document, which really just a zip archive full of files, and then unzips it into `out/$(SLUG).unzip` so that you can examine the contents.  This is fun for the curious, or if you need to understand the style classes used in the document when modifying the stylesheet (see below).
* `make clean` --- cleans up everything, including the output and all the LaTeX shrapnel left in the directory after building.
* `make tkcheck` --- searches for "TK" in the text and fails if it finds any.  [Editors use "TK" to indicate more [to come](https://en.wikipedia.org/wiki/To_come_(publishing)), i.e. unfinished writing.  You can do `make tkcheck all` to build the documents only when all TKs are removed.]


Customization
--------------

You can customize the style of the generated ePub via CSS stylesheets, and the PDF via the LaTeX document template. The style of the DOCX requires a reference doc.

### DOCX Reference Doc
A default reference doc is provided in the `templates` directory, but this can be edited with Microsoft Word or Google Docs to suit your needs. **It is currently in beta.**

### ePub Stylesheets

The `STYLESHEET` variable in the [`Makefile`](/Makefile) selects the stylesheet to use when building the ePub document.  Two stylesheets are provided in the [`/css`](/css) directory, one with an indented paragraph style and one with a block-paragraph style.  Either can be used as a starting point for a new style.  If you're not sure what style classes you need to modify, do `make unzip` then examine the `chXXX.xhtml` files in `out/$(SLUG).unzip/EPUB/text`.


### LaTeX Template

The LaTeX files that generate the book and manuscript PDF are built from templates in `templates/book.tex` and `templates/sffms.tex`. They use [Pandoc template syntax](https://pandoc.org/MANUAL.html#templates).  

For the book PDF, the title page design for the book template was taken from [Peter Wilson's great collection of LaTeX title pages](http://tug.ctan.org/info/latex-samples/TitlePages/titlepages.pdf), feel free to replace it with a design you like better.   The page header and numbering are controlled by the [fancyhdr package](https://texblog.org/2007/11/07/headerfooter-in-latex-with-fancyhdr/).

For the manuscript PDF, I had to define or override a handful of LaTeX commands to get some things to work (e.g. sections).  Inputs that deviate consierably from the example text may require some LaTeX tinkering to get right.  By default, the manuscript omits the backmatter like "Acknowledgements" and "About the Author" sections.  To include them, add $(BACKMATTER) to the `$(SLUG)-ms.tex` target in the `Makefile`.


Publishing
-----------

### Ebooks

[Amazon KDP](https://kdp.amazon.com) will take an ePub doc and covert it to a Kindle book, and it does a fairly good job.  If you're curious what it looks like, you can get [Kindle Previewer](https://www.amazon.com/gp/feature.html?docId=1000765261&ie=UTF8), and other ebook sites take ePubs directly.

### Print

I have only tried KDP, but the KDP site does a pretty good job of showing how your PDF will fit in different page trim sizes.  I found that despite using the "pocket" size parameter in the `createspace` package, it fits well in 6x9\" with good size margins (IMO).
