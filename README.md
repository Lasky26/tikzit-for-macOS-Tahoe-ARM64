
# TikZiT

TikZiT is a graphical tool for rapidly creating graphs and string diagrams using PGF/TikZ. It was used, for example, to make all of the 2500+ diagrams in <a href="http://cambridge.org/pqp">this book</a>. This is forked from official repo to build an arm64 version on MacOS Tahoe.

## Building on MacOS

You'll need Qt6 and Poppler installed. Also a newer version of bison is required. You can install these via Homebrew with the following commands:

    brew install qt6
    brew install poppler
    brew install bison

Now link to the newer bison version:

    export PATH="/opt/homebrew/opt/bison/bin:$PATH"
    export LDFLAGS="-L/opt/homebrew/opt/bison/lib"
    
Back to Qt: Homebrew doesn't add Qt binaries to the `$PATH` by default, so you may wish to either run:

    brew link --force qt6

or add `/usr/local/opt/qt/bin` to your `$PATH`. Once this is done, TikZiT can be built from the command line via:

    qmake -r
    make

Optional: To bundle the required libraries into `tikzit.app` and create a `.dmg` file, you can additionally run:

    ./deploy-osx.sh


---


Further, you'll need a `tikzit.sty` to combine your tikz styles and drawings. To do so, create a `tikzit.sty` containing

    \usepackage{tikz}
    \usetikzlibrary{backgrounds}
    \usetikzlibrary{arrows}
    \usetikzlibrary{shapes,shapes.geometric,shapes.misc}
    
    % this style is applied by default to any tikzpicture included via \tikzfig
    \tikzstyle{tikzfig}=[baseline=-0.25em,scale=0.5]
    
    % these are dummy properties used by TikZiT, but ignored by LaTex
    \pgfkeys{/tikz/tikzit fill/.initial=0}
    \pgfkeys{/tikz/tikzit draw/.initial=0}
    \pgfkeys{/tikz/tikzit shape/.initial=0}
    \pgfkeys{/tikz/tikzit category/.initial=0}
    
    % standard layers used in .tikz files
    \pgfdeclarelayer{edgelayer}
    \pgfdeclarelayer{nodelayer}
    \pgfsetlayers{background,edgelayer,nodelayer,main}
    
    % style for blank nodes
    \tikzstyle{none}=[inner sep=0mm]
    
    % include a .tikz file
    \newcommand{\tikzfig}[1]{%
    {\tikzstyle{every picture}=[tikzfig]
    \IfFileExists{#1.tikz}
      {\input{#1.tikz}}
      {%
        \IfFileExists{./images/#1.tikz}
          {\input{./images/#1.tikz}}
          {\tikz[baseline=-0.5em]{\node[draw=red,font=\color{red},fill=red!10!white] {\textit{#1}};}}%
      }}%
    }
    
    % the same as \tikzfig, but in a {center} environment
    \newcommand{\ctikzfig}[1]{%
    \begin{center}\rm
      \tikzfig{#1}
    \end{center}}
    
    % fix strange self-loops, which are PGF/TikZ default
    \tikzstyle{every loop}=[]

Now, in yout tex-project, include the .sty and place your tikz-figures inside a folder named 'images' (or change the name above respectivly). To printout the figures, include

    \ctikzfig{NAME}

in your tex-file.
