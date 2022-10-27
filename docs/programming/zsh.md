# Zsh


### ESC + `.`
Adds the last command's last argument

### !$ substitution
`!$` substitutes the previous command's first argument

Eg. 
`> cat /path/to/my/file.json`
`> vim !$` 
will automatically expand into
`> vim /path/to/my/file.json`

