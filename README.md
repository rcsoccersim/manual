# The RoboCup Soccer Simulator Users Manual

## Setup instruction for authors

The users manual is maintaining using [Sphinx](http://www.sphinx-doc.org/), a documentation tool written in
python.
The easiest way to setup sphinx (and python) environment is to install
[Anaconda](https://www.anaconda.com/download/), which enables you to install sphinx package using anaconda
navigator.

## Build and publish the document

To build the html files, type the following command in the top of working directory:
```
  $ make html
```
The outputs are written under the `build/html` directory.
In our repository, `build/html` is a symbolic link to `doc` directory, the location published as https://rcsoccersim.github.io/manual.

If successfully built, you can browse the local html files by your favarite web browser.
Then, you can commit and push source files and html files.

Please note that the `docs` directory of master branch will be immediately published as web site. 
If your changes has to be reviewed, please create another branch and push changes to there.
