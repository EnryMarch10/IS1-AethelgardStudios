# Aethelgard Studios

Group Enterprise Modelling project report for the course
[Enterprise Computing and ERP Systems - a.y. 2026-2027](https://www.su.se/english/education/course-catalogue/ib/ib454c)
([Department of Computer and Systems Sciences](https://www.su.se/english/divisions/department-of-computer-and-systems-sciences) -
[Stockholm University](https://www.su.se/english)).

## Author

[@EnryMarch10](https://github.com/EnryMarch10)

## Details

### Building LaTeX

It is suggested to built with [`latexmk`](https://ctan.org/pkg/latexmk), which runs LaTeX and the bibliography tool as many times
as needed.
The same command works on Linux, macOS and Windows (it is a Perl script).

#### Prerequisites

A standard LaTeX distribution that includes `latexmk` and `biber`.
Check your setup with `latexmk -v`.

#### The `.latexmkrc` file

`latexmk` automatically reads `.latexmkrc` from the repository root. Ours contains a single setting:

```perl
$bibtex = 'biber %O %B';
```

It makes `latexmk` use `biber` (required by `biblatex`) for the bibliography instead of classic BibTeX. Everything else is passed
on the command line, as described below.

### Build

As a rule, I decided to place all main `.tex` documents on the root directory of the project so as to compile them in blocks.

Run from the repository root to compile all root files as main documents:

```shell
latexmk [-pvc] -pdf -file-line-error -interaction=nonstopmode -synctex=1 -output-directory=out [*.tex]
```

What the options mean:

| Option                     | Meaning                                                                                                                           |
| -------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| `-pdf`                     | Produce a PDF with `pdflatex`.                                                                                                    |
| `-file-line-error`         | Print errors as `file:line: message`, which editors can parse.                                                                    |
| `-interaction=nonstopmode` | Tells LaTeX never to pause for input, not show `?` when error occurs.                                                             |
| `-halt-on-error`           | Stop at the first error occurred, if omitted try to keep going.                                                                   |
| `-synctex=1`               | Write SyncTeX data so editors can jump between source and PDF.                                                                    |
| `-output-directory=out`    | Put the PDF and all intermediate files (`.aux`, `.log`, `.bbl`, ...) in `out/`.                                                   |
| `-pv`                      | Build, then open the PDF **once**. The command returns when the build is finished.                                                |
| `-pvc`                     | Build, open the viewer, then **stay running** and rebuild whenever a `.tex`, `.bib` or included file changes. Stop with `Ctrl+C`. |

The PDF ends up at `out/<name>.pdf`. Chapter files in subfolders such as `sections/` and `config/` are included with `\input` and
are not built on their own.

### Clean

Because the output lives in `out/`, always pass `-output-directory=out` when cleaning, otherwise `latexmk` looks for files in
the wrong place:

```bash
latexmk -C -output-directory=out [-bibtex] [*.tex]
```

| Option    | Removes                                                    |
| --------- | ---------------------------------------------------------- |
| `-c`      | Auxiliary files created by LaTeX                           |
| `-C`      | Everything `-c` removes, plus the PDF                      |
| `-bibtex` | Removes also `.bbl` generated files                        |

Use `-C` for a fresh start, especially after changing packages or bibliography settings: stale auxiliary files are the most
common cause of errors that disappear after a clean.

### Working in an editor

- **VS Code (LaTeX Workshop), TeXstudio:** use a `latexmk` build recipe. Editors pick up `.latexmkrc`, but the command-line
  options above must be repeated in the editor's recipe settings if you want the same behavior (notably the `out` folder).
- **Chapter files:** put `% !TeX root = ../main.tex` (path relative to the chapter) in the first lines of each file in
  `sections/` so the editor builds the root document instead of the chapter.
