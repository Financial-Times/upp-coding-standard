# Javascript Coding standarts

## Linters

We are going to use eslint and predefined rules from airbnb

Copy [eslintrc](./eslintrc) as `.eslintrc` in the root of your project. 
Same as [eslintingore](./eslintingore) copied as `.eslintingore`.

After:
```
npm init
```
execute:

```
npm install eslint babel-eslint --save-dev
```

Add to your `package.json` file under `scripts` property.
```
    "lint": "eslint ."
```

To lint the entire project execute:
```
npm run lint
```


### Editor integration

#### VIM

It is recommended to add this 
```
autocmd FileType javascript setlocal ts=2 sts=2 sw=2 expandtab
```
to properly indent JS code

##### vim-ale

If you use [vim-ale](https://github.com/dmerejkowsky/vim-ale) you can add this to your `.vimrc`

```
let g:ale_fixers = {
\   'javascript': ['eslint', 'remove_trailing_lines', 'trim_whitespace'],
\}

let g:ale_linters = {
\   'javascript': ['eslint'],
\}
```
