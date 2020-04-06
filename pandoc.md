pandoc
======

## markdown conversions

```shell
pandoc -s -V geometry:margin=1in -o test.pdf test.md # markdown to pdf
pandoc -s -V geometry:margin=1in -o test.pdf test1.md test2.md test3.md # multi-part markdown to pdf
pandoc -s -o test.html test.md # markdown to html
```
