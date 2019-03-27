# Format

<https://github.com/rbrito/epub-example>

The .epub file is just a zip file that has:

- A certain directory strucure (see below)
- A requirement that the first file, mimetype, to be stored uncompressed, have
  exactly the contents application/epub+zip with no trailing CR, LF, NL etc.,
  and have no special attributes or extra fields.
- The tree hierarchy looks approximately like this:

	 .
	 ├── mimetype
	 ├── META-INF
	 │   └── container.xml
	 └── OEBPS
	     ├── book.ncx
	     ├── book.opf
	     └── chapter01.xhtml

The file container.xml serves as a "bootstrap" for the book.

The .opf file contains at a minimum, metadata of the book, the list of all
files (except for mimetype, container.xml, and the .opf) that compose the book,
with their full path, IDs and media-types, and, lastly, the "linear" list of
topics in which the book should be read.

The content of the dc:identifier element of the .opf file has to match the
property of the meta element with name dtb:uid. Otherwise,
http://www.threepress.org/document/epub-validate/ does not validate the
document.

# Wikipedia

<https://en.wikipedia.org/wiki/EPUB>

## Container

A single zip file with single common root directory for all contents. EPUB does
not mandate specific file system structure for non-remote resources.

Mandatory file: `mimetype`, must be the first file. Unencrypted and
uncompressed ASCII file that contains the string `application/epub+zip`

Mandatory directory: `/META-INF` to store `container.xml`. Example of the file:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<container version="1.0" xmlns="urn:oasis:names:tc:opendocument:xmlns:container">
  <rootfiles>
    <rootfile full-path="OEBPS/content.opf" media-type="application/oebps-package+xml"/>
  </rootfiles>
</container>
```

Example structure:

	mimetype
	META-INF/
	  container.xml
	OEBPS/
	  content.opf
	  chapter1.xhtml
	  ch1-pic.png
	  css/
	    style.css
	    myfont.otf
	  toc.ncx

EPUB file must contain

- At least one content document (`*.xhtml`)
- One navigation document (`*.ncx`)
- One package document listing all publication resources  (`*.opf`).
  It contains metadata, a manifest, fallback chains, bindings, and a spine.
  This is an ordered sequence of ID references defining the default reading
  order.

may contain:

- style sheets.
- PLS Documents.
- media overlay documents.

Example `toc.ncx`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE ncx PUBLIC "-//NISO//DTD ncx 2005-1//EN"
"http://www.daisy.org/z3986/2005/ncx-2005-1.dtd">

<ncx version="2005-1" xml:lang="en" xmlns="http://www.daisy.org/z3986/2005/ncx/">

  <head>
<!-- The following four metadata items are required for all NCX documents,
including those that conform to the relaxed constraints of OPS 2.0 -->

    <meta name="dtb:uid" content="123456789X"/> <!-- same as in .opf -->
    <meta name="dtb:depth" content="1"/> <!-- 1 or higher -->
    <meta name="dtb:totalPageCount" content="0"/> <!-- must be 0 -->
    <meta name="dtb:maxPageNumber" content="0"/> <!-- must be 0 -->
  </head>

  <docTitle>
    <text>Pride and Prejudice</text>
  </docTitle>

  <docAuthor>
    <text>Austen, Jane</text>
  </docAuthor>

  <navMap>
    <navPoint class="chapter" id="chapter1" playOrder="1">
      <navLabel><text>Chapter 1</text></navLabel>
      <content src="chapter1.xhtml"/>
    </navPoint>
  </navMap>

</ncx>
```

Example `content.opf`:

```xml
<?xml version="1.0"?>
<package version="2.0" xmlns="http://www.idpf.org/2007/opf" unique-identifier="BookId">

  <metadata xmlns:dc="http://purl.org/dc/elements/1.1/" xmlns:opf="http://www.idpf.org/2007/opf">
    <dc:title>Pride and Prejudice</dc:title>
    <dc:language>en</dc:language>
    <dc:identifier id="BookId" opf:scheme="ISBN">123456789X</dc:identifier>
    <dc:creator opf:file-as="Austen, Jane" opf:role="aut">Jane Austen</dc:creator>
  </metadata>

  <manifest>
    <item id="chapter1" href="chapter1.xhtml" media-type="application/xhtml+xml"/>
    <item id="stylesheet" href="style.css" media-type="text/css"/>
    <item id="ch1-pic" href="ch1-pic.png" media-type="image/png"/>
    <item id="myfont" href="css/myfont.otf" media-type="application/x-font-opentype"/>
    <item id="ncx" href="toc.ncx" media-type="application/x-dtbncx+xml"/>
  </manifest>

  <spine toc="ncx">
    <itemref idref="chapter1" />
  </spine>

  <guide>
    <reference type="loi" title="List Of Illustrations" href="appendix.html#figures" />
  </guide>

</package>
```
