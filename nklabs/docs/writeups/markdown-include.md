

markdown-include is a pip package that can be use with mkdocs.


- Install the python package markdown-include per https://github.com/cmacmackin/markdown-include

- Add the following lines in your mkdocs. Set the base_path to your choice.

```
markdown_extensions:
  - markdown_include.include:
      base_path: .
``` 

- And finally add the contents of one file into another by just doing 

\{!yourfile!\}
 
 
For real example, check out the this writeup in github



