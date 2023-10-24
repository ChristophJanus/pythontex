# Extension of PythonTeX

This fork of the PythonTeX package adds a command to immediately restart pythontex sessions: \restartpythontexsessionnow as a bachelor project at the proglang chair at the University of Freiburg.
To see the original README see READMEpythontex.rst or https://github.com/gpoore/pythontex

## Overview

The PythonTex package provides the means to run Python code from within LaTeX documents, automatically perform syntax highlighting of Python code, define custom environments and commands that execute Python code, and more.\
\py[\<session>]{python code} for example executes the given Python code and prints the result in the document.\
Sessions can be used to execute python code in separate files. Providing different session names will cause the code to be executed in different files.\
PythonTeX creates a "file_name.pytxcode" file for each "file_name.tex" file that contains the python code to be executed. There we can see what code will be executed in what file.\
Looking at an example:
    
```latex
\begin{pycode}[my_session]
print("Set x as", 0)
x = 0
\end{pycode}

\begin{pycode}[other_session]
try:
    print("Found x with value", x)
except NameError:
    print("x not found")
\end{pycode}
```

This will create the following "file_name.pytxcode" file:

```code
=>PYTHONTEX#py#my_session#default#0#code#####1#
print("Set x as", 0)
x = 0
=>PYTHONTEX#py#other_session#default#0#code#####6#
try:
    print("Found x with value", x)
except NameError:
    print("x not found")
```
As the sessions have different names, the code will be executed in different files. And the print("x not found") will be brought in the original tex-document.\

## Restarting sessions

PythonTeX provides the command \restartpythontexsession to restart sessions. This will cause the sessions to be executed in a new file. It is used with counter variables e.g. \restartpythontexsession{\thesection} to reset all sessions in every new section.\

```latex
\usepackage{pythontex}

\restartpythontexsession{\thesection}
\section{Section 1}
\begin{pycode}[same_session]
print("Set x as", 0)
x = 0
\end{pycode}

\section{Section 2}
\begin{pycode}[same_session]
try:
    print("Found x with value", x)
except NameError:
    print("x not found")
\end{pycode}
```


This will create the following "file_name.pytxcode" file:

```code
=>PYTHONTEX#py#same_session#1#0#code#####5#
print("Set x as", 0)
x = 0
=>PYTHONTEX#py#same_session#2#0#code#####11#
try:
    print("Found x with value", x)
except NameError:
    print("x not found")
```

This will still throw the exception although the code was executed in the same session. This is because the session was restarted in the new section. Note the number after same_session in the .pytxcode file. This is the current group. Groups subdivide sessions. Code in the same session but in different groups will still be executed in different files.\
Note that groups will cause all sessions to be reset.

## Restarting sessions immediately

This is the newly added feature. \restartpythontexsessionnow will restart the sessions immediately. This is useful if you want to restart sessions without having to increase the counter variable.\
\restartpythontexsessionnow can be called as often as desired. It will override the group set by \restartpythontexsession.\
It is backwardscompatible and simultaneously useable with \restartpythontexsession. If the counter value of \restartpythontexsession changes, the overritten group will be reset.\

```latex
\begin{pycode}[same_session]
print("Set x as", 0)
x = 0
\end{pycode}

\restartpythontexsessionnow
\begin{pycode}[same_session]
try:
    print("Found x with value", x)
except NameError:
    print("x not found")
\end{pycode}
```

This will create the following "file_name.pytxcode" file:

```code
=>PYTHONTEX#py#same_session#1#0#code#####19#
print("Set x as", 0)
x = 0
=>PYTHONTEX#py#same_session#manual_group1#0#code#####25#
try:
    print("Found x with value", x)
except NameError:
    print("x not found")
```
