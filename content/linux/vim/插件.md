# 插件管理器

使用 `Vim-plug`

```bash
curl -fLo ~/.vim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

然后打开`.vimrc` 文件，输入：

```vimrc
" 插件管理器配置 - Vim-plug
" 指定插件安装目录，可以自定义
call plug#begin('~/.vim/plugged') 

" 在这里添加插件

call plug#end()
```

添加完插件之后，保存重新启动，在 Vim 的普通模式下，输入以下命令并按回车：

```vim
:PlugInstall
```

Vim-plug 会自动下载并安装插件

**查看插件是否安装成功：**

```vim
:PlugStatus
```

# 代码补全

使用 `CoC.nvim` 插件

在 `call plug#begin()` 和 `call plug#end()` 之间添加配置行：

```vimrc
" 插件管理器配置 - Vim-plug
call plug#begin('~/.vim/plugged')

" CoC.nvim 插件
Plug 'neoclide/coc.nvim', {'branch': 'release'}

call plug#end()
```

然后 `:PlugInstall`

一些 `CoC.nvim` 的基本配置，放在 `call plug#end()` 后面

```vimrc
" CoC.nvim 配置
" ----------------------------------------------------------------------
" 始终显示补全菜单
set noinsertmode
set cmdheight=2 " 命令行高度
set shortmess+=c " 避免在命令行中显示冗余信息

" 启用补全菜单的弹出窗口
set completeopt=menuone,noselect

" 当光标在补全项上时，显示文档
set pumheight=10 " 补全菜单高度
set signcolumn=yes " 显示诊断信息（错误/警告）

" 映射 Tab 键用于补全和跳转
inoremap <silent><expr> <Tab>
      \ pumvisible() ? "\<C-n>" :
      \ <SID>check_back_space() ? "\<Tab>" :
      \ coc#refresh()
inoremap <expr><S-Tab> pumvisible() ? "\<C-p>" : "\<S-Tab>"

function! s:check_back_space() abort
  let col = col('.') - 1
  return !col || getline('.')[col - 1] =~ '\s'
endfunction

" 映射回车键用于选择补全项
inoremap <silent><expr> <CR> pumvisible() ? coc#_select_confirm() : "\<C-g>u\<CR>"

" 诊断信息快捷键
nnoremap <silent> [g <Plug>(coc-diagnostic-prev)
nnoremap <silent> ]g <Plug>(coc-diagnostic-next)

" 跳转到定义
nnoremap <silent> gd <Plug>(coc-definition)
" 跳转到类型定义
nnoremap <silent> gy <Plug>(coc-type-definition)
" 跳转到实现
nnoremap <silent> gi <Plug>(coc-implementation)
" 跳转到引用
nnoremap <silent> gr <Plug>(coc-references)

" 重命名
nnoremap <silent> <leader>rn <Plug>(coc-rename)

" 格式化当前缓冲区
nnoremap <silent> <leader>f <Plug>(coc-format-document)

" 悬停显示文档
nnoremap <silent> K :call <SID>show_documentation()<CR>
function! s:show_documentation()
  if (index(['vim','help'], &filetype) >= 0)
    execute 'h '.expand('<cword>')
  else
    call coc#util#do_hover()
  endif
endfunction

" ----------------------------------------------------------------------
```

**安装语言服务器：**

JavaScript/TypeScript：

```vim
:CocInstall coc-tsserver
```

Python：

```vim
:CocInstall coc-python
```

C/C++：

```vim
:CocInstall coc-clangd
```

在 CoC.nvim 的 GitHub 页面找到支持的语言服务器列表：[https://github.com/neoclide/coc.nvim/wiki/Language-servers](https://github.com/neoclide/coc.nvim/wiki/Language-servers)

**查看已安装的语言服务器：** 

```vim
:CocCommand workspace.extension
```

**查看 CoC.nvim 的日志：**

```vim
:CocOpenLog
```

