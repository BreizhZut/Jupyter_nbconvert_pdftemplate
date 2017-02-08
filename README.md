# Jupyter nbconvert PDF LaTeX templates
Contains multiple templates for creating various pdf (documentation, article, presentation) from one notebook.

A large part of what I did is inspired from [Making publication ready Python Notebooks by Juilus Shulz](http://blog.juliusschulz.de/blog/ultimate-ipython-notebook). And the main trick is edit the metadata and edit a template to use them. We go as far as to add more optional metadata making it possible to obtain a large variety of styles without editing the notebook.

## Content

All the pdf<sup id="cp1">[1](#f1)</sup> are produced from the same python notebook test example `iris.ipynb`

Except  `dbzh_chapter.tplx` all templates included here are extensions of this template `display_latex_metadata.tplx`. Using the notebook metadata template `display_latex_metadata.tplx` contains the macros to edit: 

* the title (specify a title and an optional subtitle)
* the author (specify an author and optional affiliation)
* the date (current date by default)
* the bilbliography (specify a `bibtex` file, and `bibtex` style )
 
"Stand alone " templates are the following:

1. `iris_default.pdf` is the default pdf produced without our templates, using  `jupyter nbconvert --to pdf  iris.ipynb`
1. `dbzh_doc.tplx` documentation template
	* `iris_documentation.pdf` was produced using: `jupyter nbconvert --to pdf --template dbzh_doc.tplx  iris.ipynb`
1. `dbzh_article.tplx`: [two columns] article template
	* `iris_article.pdf` was produced using: `jupyter nbconvert --to pdf --template dbzh_article.tplx  iris.ipynb`
1. `dbzh_beamer.tplx`: [16:9] presentation template
	* `iris_article.pdf` was produced using: `jupyter nbconvert --to pdf --template dbzh_beamer.tplx  iris.ipynb`
1. `dbzh_chapter.tplx` documentation template
	* is designed to be part of a larger document and cannot be directly convert ot pdf
	* `iris.tex` was produced using: `jupyter nbconvert --to latex --template dbzh_chapter.tplx  iris.ipynb`
	
	* `iris_book.pdf` was then created following:

```
pdflatex iris_book.tex
bibtex iris_book.aux
pdflatex iris_book.tex
pdflatex iris_book.tex
```

The supporting document are the following

* `biblio.bib` bibliography file needed by bibtex
* `iris_book.tex` main latex document needed to compile the chapter exemple
* `jupyter_latex.sty` contains color LaTeX definitions, environment needed by the templates
* `pygment_definitions.sty` copy of LaTeX commands defined for the python environment. it is only required by `iris_book.tex` since the commands are generated for the other template by `nbconvert`

<b id="f1">1</b> At each instance PDF output `iris.pdf` is renamed for each template for  obvious reasons. [&#8617;](#cp1)

## List of options and how to implement them 

### To be integrated into the document metadata

|  | dbzh_doc | dbzh_article |dbzh_chapter|dbzh_beamer|
|----|------|------|------|------|
| title  | &#10004;  | &#10004;  |&#10004; (chapter title) |&#10004; |
| subtitle  | &#10004;  | &#10004;  |&#10004; (chapter title)| &#10004; |
| author | &#10004;    | &#10004; |&#10004; (epigraph author) |&#10004; |
| date | &#10004; |&#10004;  ||&#10004; |
| affiliation | &#10004; (if author) | &#10004; (if author) ||&#10004; |
| abstract | &#10004; |&#10004;   |&#10004;   ||
|subject|&#10004;(PDF metadata)|&#10004;(PDF metadata)||&#10004;(PDF metadata)|
| keywords  | &#10004; | &#10004; |||
| epigraph | ||&#10004; ||
| bibfile |&#10004;|&#10004;||&#10004; |
| bibstyle|&#10004;|&#10004;||&#10004; |


In Jupyter, menu **Edit** then **Edit notebook metada** . Simply add a `"latex_metadata"` entry and fill with the required item.
We show an exemple here:

```
 "latex_metadata": {
   "affiliation": "Your affiliation",
   "title": "Title of thie document",
   "author": "Your name",
   "data":"The date ", 
   "abstract": "Not sure I want that here",
   "bibfile":"bilbio.bib",
   "bibstyle":"apalike"
  }
```

Click OK, if a `WARNING: Could not save invalid JSON.` message appear check your `,` or that you have `\\` instead of `\`.

### To be integrated into individual cells metadata

In Jupyter, menu **View** then **Cell toolbar** and  **Edit Metada** . A menu should appear above every cell.
Once you click on the **Edit Metada** it should look like that if you ran the  code cell :

```
{
  "collapsed": false,
  "trusted": true
}
```
Or that if you didn't

```
{}
```


You simply have to hide the stuff you need for exemple:

```
{
  "collapsed": false,
  "trusted": true,
  "hide":true,
  "wide":true,
  "width":"0.75\\textwidth",
  "caption":"Exemple of a table or figure",
 }
```

Click OK, if a "Invalid Json" message appear check your `,` or that you have `\\` instead of `\`.

#### Affect figures and latex outputs (tables)

|  | dbzh_doc | dbzh_article         |dbzh_chapter|dbzh_beamer|
|----|------|------|------|------|
| height  | &#10004; (max) | &#10004; (max) | &#10004; (max) |&#10004; (max) |
| width  | &#10004; (max)   | &#10004; (max) | &#10004; (max) |&#10004; (max) |
| caption  | &#10004; | &#10004;|  &#10004; |&#10004; |
| label   | &#10004; | &#10004;| &#10004; |&#10004; |
| _wide_  | | &#10004; (two columns)  | | |

#### Display or hide codes and markdown cells 

|  | dbzh_doc | dbzh_article         |dbzh_chapter|dbzh_beamer|
|----|------|------|------|------|
 _hide_            | &#10004;  | &#10004; (markdown) |&#10004; (markdown) ||
| _show_            |                 | &#10004; (code ) |&#10004; (code)  |&#10004;|
| loremipsum      | &#10004; (hide markdown ) |  &#10004; (markdown add filler) |&#10004; (markdown add filler)  |

#### Presentations

|  |dbzh_beamer|
|----|------|
|_startframe_|&#10004; (start a slide)|
|_endframe_|&#10004; (end a slide)|
| title|&#10004; (title of the slide )|
| block|&#10004; ( `"  "`  without a title)|
| alert|&#10004; ( `"  "` without a title)|
|exemple|&#10004; ( `"  "` without a title)|
|only|&#10004; (not suported with code)|

You could add the following lines to the previous example in order to make it a slide.

```
 "startframe":true,
  "frametitle": "A nice example",
  "endframe":true
```

## Set up

### Where to put your bibliography file

The issue there to make `jupyter nbconvert --to pdf ` works is to move your bilbiography in a place where `bibtex` looks for it. 

* Windows `\Documents and Settings\<user name>\texmf\bibtex\bib\`
* Windows Vista/7 `C:\Users\<user name>\texmf\bibtex\bib\`
* Linux `~/texmf/bibtex/bib/`
* Mac OS X `~/Library/texmf/bibtex/bib/` (`~/Library` may be hidden by default since Maverick if memory serves but it is there) 

You can create a giant `bibtex` file for storing your citation database, as long as you don't put a `\nocite{*}` only the papers you cite in the text will be in the references. 

### Where to store the templates

One efficient way is to move these templates (only the '.tplx') to the anaconda directory (`~/anaconda/lib/python2.7/site-packages/nbconvert/templates/latex'). However it is strongly advised not to do so. Furthermore if `anaconda` was to be reinstalled these templates - that are not part of the distribution - would be lost. As for the style file`.sty` it should be moved in a nice directory in your tex library `/texmf/tex/latex/` (see below)

The right way is to store them in a smart place independent from your anaconda install.
I suggest to put them in your latex library  (where all your custom style are found by latex)

* Windows `\Documents and Settings\<user name>\texmf\tex\latex\Jupyter\`
* Windows Vista/7 `C:\Users\<user name>\texmf\tex\latex\`
* Linux `~/texmf/tex/latex/local/`
* Mac OS X `~/Library/texmf/tex/latex/` (`~/Library` may be hidden by default since Maverick if memory serves but it is there) 

## Customizing

I suggest to copy the relevant template and customize the macros. Most of the template modification can be done by anyone familiar with LaTeX. Some useful tricks:

* Changing paper size, margins, etc... look at `geometry` (see [Wiki:Page Layout](https://en.wikibooks.org/wiki/LaTeX/Page_Layout))
* Changing lengths, some length are predefined in `jupyter_latex.sty` (search `\newlength`) and can be redefine at any place in the template using the `\setlength{}{}` command.
* Changing colors for the links, citations and cross references look search (`\hypersetup`)
* Changing the citation style, change the `bibstyle` in the notebook metadata (see [Bibliography styles](http://sites.stat.psu.edu/~surajit/present/bib.htm)), add natbib by adding  `\usepackage{natbib}` in the template  
* Article: switching to two column edit`documentclass[]{}` (line ready but commented)
* Using a specific journal template, provided you installed the template already edit the article template with the correct documentclass.  
	* Some packages would be no longer required other may cause problems (compatibility issues).   
	* Replace `add_author()`  by `split_author_affiliation() ` to use the `\affiliation` command seperatly from 	`\author`.
	* Additional bibliography styles (`.bst`) could be moved in `texmf\bibtex\bst\` (you bibliography should be in `texmf\bibtex\bib\`)
* Beamer: swithching to 16:9 display edit `documentclass[]{}` (line ready but commented)
* Beamer: changing style edit `\usetheme{}` (see [classic themes: beamer_gallery](http://deic.uab.es/~iblanes/beamer_gallery/index_by_theme.html))

## Still to do...

1. develop the poster template
2. to column option
3. solving coding environement compatibility issue within the frame environment for the beamer template
4. Improve the automated referencing labeling for beamer template
5. Integrate and test `tickz` within Jupyter
