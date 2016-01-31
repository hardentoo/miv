# miv [![Build Status](https://travis-ci.org/itchyny/miv.png?branch=master)](https://travis-ci.org/itchyny/miv)
### Vim plugin manager written in Haskell

## Installation

```
 $ git clone https://github.com/itchyny/miv
 $ cd miv
 $ stack install
```

## Author
itchyny (https://github.com/itchyny)

## License
This software is released under the MIT License, see LICENSE.

## Migration guide for users of older miv
I've changed some interfaces of miv recently. Configuration files for older miv cannot be recognized correctly by the new one. I'm sorry. You can fix up your configuration with the following steps.

1. Change the field keys; `beforeScript` -> `before`, `afterScript` -> `after`, `filetypeScript` -> `filetype`.
2. The plurals are deprecated; `commands` -> `command`, `filetypes` -> `filetype`, `functions` -> `function`, `mappings` -> `mapping`, `mapmodes` -> `mapmode`.
3. The field `filetypeDetect` is deprecated. Write your own configuration in the `after` script.

## User guide
1. Add the miv plugin path to runtimepath in your vimrc.
2. Create ~/.vimrc.yaml.
3. Execute `miv install`.

Example vimrc:
```vim
filetype off
if has('vim_starting')
  set rtp^=~/.vim/miv/miv/
endif
filetype plugin indent on
```

Example `~/.vimrc.yaml`:
```yaml
plugin:

  Shougo/neocomplete.vim:
    enable: has('lua')
    before: |
      let g:neocomplete#enable_at_startup = 1
      let g:neocomplete#enable_smart_case = 1
      let g:neocomplete#max_list = 1000

  thinca/vim-quickrun:
    command: QuickRun
    mapping: <Plug>(quickrun)
    mapleader: \
    dependon: vimproc
    script: |
      noremap <Leader>r <Plug>(quickrun)
    before: |
      let g:quickrun_config = {'_': {'runner': 'vimproc', 'runner/vimproc/updatetime': 200, 'split': 'vertical', 'into': 1}}

  Shougo/vimfiler:
    mapleader: \
    command:
      - VimFiler
      - VimFilerTab
      - VimFilerBufferDir
      - VimFilerExplorer
    dependon:
      - vimproc
      - unite
    script: |
      nnoremap <silent> <Leader>f :<C-u>VimFilerBufferDir -auto-cd<CR>
    before: |
      let g:vimfiler_as_default_explorer = 1
      let g:vimfiler_sort_type = 'TIME'

  Shougo/vinarise:
    command: Vinarise
    loadbefore: vimfiler

  Shougo/unite.vim:
    command: Unite
    mapleader: ","
    function: unite
    script: |
      nnoremap <silent><C-n> :<C-u>Unite file/new directory/new<CR>
      nnoremap <silent><C-o> :<C-u>Unite file file/new<CR>
      nnoremap <silent><S-l> :<C-u>Unite line<CR>
    before: |
      let g:unite_force_overwrite_statusline = 0
    after: |
      call unite#custom#profile('default', 'context', {
            \ 'start_insert' : 1,
            \ 'prompt_direction': 'top',
            \ 'hide_icon': 0 })
    dependedby:
      - unite-build
      - unite-colorscheme
      - unite-highlight

  Shougo/unite-build:
    loadafter: unite

  ujihisa/unite-colorscheme:
    loadafter: unite

  osyo-manga/unite-highlight:
    loadafter: unite

  Shougo/vimshell.vim:
    command:
      - VimShell
      - VimShellBufferDir
      - VimShellInteractive
      - VimShellPop
      - VimShellTab
    function: vimshell
    mapleader: ;
    dependon: vimproc
    script: |
      nnoremap <silent> <Leader>s :<C-u>VimShellBufferDir<CR>
      nnoremap <silent> H :<C-u>VimShellBufferDir -popup<CR>
    before: |
      let g:vimshell_popup_command = 'top new'
      let g:vimshell_split_command = 'vsplit'

  Shougo/vimproc.vim:
    build: make
    function: vimproc
    loadbefore:
      - vimfiler
      - vimshell
      - quickrun

  Align:
    command: Align

  tyru/capture.vim:
    command: Capture

  itchyny/lightline.vim:
    before: |
      let g:lightline = {
            \   'colorscheme': 'wombat',
            \   'mode_map':{ 'c': 'NORMAL' },
            \ }

  itchyny/calendar.vim:
    mapleader: ","
    command: Calendar
    mapping: <Plug>(calendar)
    script: |
      map <silent> <Leader>z <Plug>(calendar)
    before: |
      let g:calendar_views = [ 'year', 'month', 'day_3', 'clock' ]

  elzr/vim-json:
    filetype: json

  mattn/emmet-vim:
    filetype:
      - html
      - css
    before: |
      let g:user_emmet_settings = { 'indentation' : '  ' }
    after: |
      autocmd FileType html,css imap <buffer> <tab> <plug>(emmet-expand-abbr)

  wavded/vim-stylus:
    filetype: stylus

  groenewege/vim-less:
    filetype: less

  tpope/vim-haml:
    filetype: haml

  jade.vim:
    filetype: jade

  kchmck/vim-coffee-script:
    filetype: coffee

  kana/vim-textobj-user: {}

  kana/vim-textobj-entire:
    dependon: textobj-user
    mapmode:
      - o
      - v
    mapping:
      - <Plug>(textobj-entire-a)
      - <Plug>(textobj-entire-i)
      - ie
      - ae

  kana/vim-textobj-line:
    dependon: textobj-user
    mapmode:
      - o
      - v
    mapping:
      - <Plug>(textobj-line-a)
      - <Plug>(textobj-line-i)
      - il
      - al

before: |
  if v:version < 703
    finish
  endif

  let g:is_bash = 1

  let g:loaded_2html_plugin = 1
  let g:loaded_rrhelper = 1

after: |
  let g:mapleader = ','

filetype:
  vim: |
    setlocal foldmethod=marker
  c: |
    setlocal ofu=ccomplete#Complete
  html: |
    setlocal ofu=htmlcomplete#CompleteTags
  css: |
    setlocal ofu=csscomplete#CompleteCSS
    setlocal iskeyword=37,45,48-57,95,a-z,A-Z,192-255
  make: |
    setlocal noexpandtab
  sh: |
    setlocal iskeyword=36,45,48-57,64,95,a-z,A-Z,192-255
```

## `miv` subcommands
### `miv install`
Installs all the plugins.

### `miv update`
Updates the plugins (outdated plugins are skipped).

### `miv update!`
Updates all the plugins.

### `miv install [plugins]`
Installs the specified plugins.

### `miv update [plugins]`
Updates the specified plugins.

### `miv clean`
Removes the unused directories.

### `miv generate`
Generates the miv plugin files. (`miv install` and `miv update` automatically do this task)

### `miv helptags`
Generates the helptags file. (`miv install` and `miv update` automatically do this task)

### `miv list`
Lists all the plugins.

### `miv edit`
Edits the `~/.vimrc.yaml` file.

### `miv command`
Lists the subcommands of `miv`.

### `miv path [plugins]`
Prints the paths of the plugins.

### `miv each [commands]`
Executes the commands each directory of the plugins. For example, you can execute `miv each pwd` or `miv each git gc`.

### `miv help`
Shows the help of `miv`.

### `miv version`
Shows the version of `miv`.

