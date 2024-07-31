# pandoc commands quick reference

## to convert from md to text document (docx)

pandoc -f markdown -t docx -o output_file_name.docx input_file_name.md

## to convert form markdown to html

pandoc -f markdown -t html -o output_file_name.html input_file_name.md

## to convert form markdown to pdf

pandoc -s -o output_file_name.pdf input_file_name.md

pandoc --pdf-engine=xelatex input_file_name.md -o output_file_name.pdf

> Notes: for pdf conversion, pandoc uses the latex engine by default; some characters, however, cannot be reproduced with the default engine, thus the need to use the xelatex engine (downloaded separately as `'texlive-xetex'`). Many characters still cannot be printed with xelatex, unless they are added to the default pandoc template. Pdf output files from pandoc look horrible anyway, so the best way to get a pdf from .md is to convert the .md to .docx with pandoc and then export the docx file to pdf with the default document reader: it looks much better.
> I'll later consider to remove `texlive-xetex` (it may be useful for other conversions).

