# Trouble shooting


## Question answered here

* I run  `jupyter nbconvert --to pdf` but I get **[?]** instead of citations, how do I fix this?
* How do I tell `nbconvert` to search my template directory?
* I installed `nbextensions` now `nbconvert` doesn't work, how can I fix  the following issue?

```
module = __import__(package, fromlist=[obj]) 
TypeError: Item in ``from list'' must be str, not unicode
```
* `nbconvert` doesn't produce a pdf for the beamer template, how can i find what is wrong?
* Can I put [animations](#anim) in the beamer presentation? 
* Can I make a poster?
	* You could change the geometry for the beamer or article template. 
	* I plan to create a template using `ba_poster` in the near future, you're encouraged to beat me to it. 		 


## Usage 

Before doing anything fancy as is described later. You may test these templates, and even  create our own copies to tune. 

### Direct pdf conversion

```
> jupyter nbconvert --to pdf --template <template_name>.tplx <notebook_name>.ipynb
```

By the way  it works without extensions, too:

```
> jupyter nbconvert --to pdf --template <template_name> <notebook_name>
```

That should work if the template is either:

* in the working directory with the notebook
* in a directory searched by `nbconvert`  (see **Set up**)
	
The reference may be missing and you have the message:

```
WARNING | bibtex had problems, most likely because there were no citations
```

You may have the correct bibliography file in the working directory, but it cannot be found by `bibtex` use the **Indirect pdf convertion**. 

If the problem persists check the name of the bibliography file.
If it work you can apply the very simple solution indicated in **Set up**. 
	
### Indirect pdf conversion <a id=indirect></a>

Prefered for [debugging](#debug) 

```
> jupyter nbconvert --to latex --template <template_name> <notebook_name>
> pdflatex <notebook_name>
> bibtex <notebook_name>
> pdflatex <notebook_name>
> pdflatex <notebook_name>
```

More precisely with the provided exemple  (specified all extension but it works wihtout too):

```
> jupyter nbconvert --to latex --template codedoc_template.tplx iris.ipynb
> pdflatex iris.tex
> bibtex iris.aux
> pdflatex iris.tex
> pdflatex iris.tex
```

That basically what `> jupyter nbconvert --to pdf <notebook_name>` but the tex file is moved as `notebook.tex` and compiled in another directory,

That should work if:

* The template is either:
	* in the working directory with the notebook
	* in a directory searched by `nbconvert`  
* The reference are not missing if either:
	* You don't have a bilbiography
	* Your bibliography file is in your latex library, and is named `biblio.bib`
	* Your bibliography file is the working directory, and is named `biblio.bib`

### Setting `nbconvert` to find the template (requires `nbextension`)

You simply need to find the variable that describe the default path for templates. 
I didn't find it with a clean version of `jupyter`, but I could with one using `nbextension`. 

If you have nbextension installed you can edit the template path and add the path to your template directory. Edit `~/anaconda/etc/jupyter/jupyter_nbconvert_config.json`. Could be also  `~/.jupyter/jupyter_nbconvert_config.json`.

```
{
  "Exporter": {
    "template_path": [
      ".",
      "~/anaconda/lib/python2.7/site-packages/jupyter_contrib_nbextensions/templates",
      "~/Library/texmf/tex/Jupyter/"
    ],
    "preprocessors": [
      "jupyter_contrib_nbextensions.nbconvert_support.CodeFoldingPreprocessor",
      "jupyter_contrib_nbextensions.nbconvert_support.PyMarkdownPreprocessor"
    ]
  },
  "version": 1
}
```
### About `nbextension`

Installing is not mandatory, but it is practical to have `spellchecking` and `Latex_env`. 
By using `Latex_env` citations also appear in the notebook, it requires to have the bib entries in fine  `biblio.bib` in the working directory. Extension `cite2c` seems to be a widely used alternative.


#### Installing [See nbextension documentation](http://jupyter-contrib-nbextensions.readthedocs.io/en/latest/install.html)

After a clean installation of anaconda I simply followed the procedure:

```
conda install -c conda-forge jupyter_contrib_nbextensions
```

```
jupyter contrib nbextension install --sys-prefix
```
#### Using the bibliography within Jupyter

Since you have `nbextension` you can activate `latex_env`, which can deal with citations and bibtex. You need to have a local copy of your bibliography file named `biblio.bib` by default. It creates (eventually) a markdown cell with the references, use `"hide":true` in this cell metadata so that it doesn't appear in the pdf (otherwise your reference would appear twice). 

#### Troubleshooting with `nbconvert` after installing `nbextension`

After installing `nbextension` as previously described, `nbconvert` stoped working. Probably because `nbextension` edit some files in anaconda. I found the solution online some time ago, so I am sorry not to give credit.

```
   module = __import__(package, fromlist=[obj])
TypeError: Item in ``from list'' must be str, not unicode
```
The bug is in 

```
~/anaconda/lib/python2.7/site-packages/ipython_genutils/importstring.py
```
Correct the line 

```python
module = __import__(package, fromlist=[obj])
```

As

```python
module = __import__(package, fromlist=str([obj]))
``` 

## `nbconvert` doesn't produce a pdf file <a id=debug></a>

Well two things can occur

1. there is a bug with the template
2. the latex file produced by `nbconvert` cannot be compiled

The trick is simple go through the   "Indirect pdf conversion"  [&#8617;](#indirect)

If you get stuck at the `jupyter nbconvert` stage  the template is at fault.
If you get stuck at the `pdflatex` stage, that is a latex problem, simply identify the bug from the tex file, the bug is likely to be corrected in some cell metadata. In the very worst case scenario the latex could be modified so that the pdf could be produced. But the correction would have to repeated each time the notebook is modified (changing and rerunning the code cells).

This is likely to be the case with the `dbzh_beamer.tplx`, a few advise to make it work.

* When starting a frame with `"startframe":true`
	* Make sure you have `"show":true`
	* Close the frame as well `"endframe":true` if a different cell check `"show":true`
* `nbconvert` tend not to pass lines when needed I added some "%" (comment) to avoid some issues, check the `.tex` for 
	* commented `\begin{` or `\end{` statements
	* multiple  `\begin{` or `\end{` statements in a sigle line
	* `Verbatim` environment: there are issues with this enviroment (here `coding` or `stream`) comment from `\begin{coding}` to `\end{coding}` this could help locating the problem.

## Beamer: basic animations <a id=anim></a>

	
Animations are applied to one cell at a time with command `"only":"[..]"`

* require to contain a cell without animation  	
* can take on number `"only":2` (appear on first clik)
* or multiple `"only":"2-3" (appear on first clik, remain on the second)
* To have a sequence of 3 cell appearing:
	* base cell (not options of `"only":"1-4")
	* first appearing cell:`"only":"2-4"
	* second appearing cell:`"only":"3-4"
	* third appearing cell:`"only":"4"
* Replacing cells 
	* base cell with option  `"only":"1"` 
	* replacement cell  `"only":"2"`  	

Normally one can also add animation in the ordered and unordered list in beamer. We haven't found a way to integrate that but the tex could be edited. 

```
% within an itemize, description or enumerate environment
\item<1-4> Is there from the begining
\item<2> Appear at second click, will disappear at the next
	\begin{%another itemize, description or enumerate environment%}
	\item Subitem appears at second click, will disappear at the next
	\item Subitem appears  at second click, will disappear at the next
	\end{%another itemize, description or enumerate environment%}
\item<3-4>  Appear at second click, and remain at the next
\item<4> Appear at the fourth click
```