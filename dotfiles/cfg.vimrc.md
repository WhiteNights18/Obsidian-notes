
```bash
" 显示行号 - 方便定位代码
set number
 
" 制表符宽度为 4 个空格
set tabstop=4
 
" 自动缩进 - 写代码时自动保持正确的缩进
set autoindent
 
" 启用鼠标支持 - 可以用鼠标选择、滚动等
set mouse=a
 
" 语法高亮 - 让代码五彩斑斓
syntax on

" 设置配色方案（需要先安装相应主题）
colorscheme desert
 
" 光标行高亮 - 快速定位当前行
set cursorline
 
" 显示当前模式 - 清楚知道处于哪种状态
set showmode
 
" 总是显示状态行
set laststatus=2
 
" 设置状态行显示内容
set statusline=%F%m%r%h%w\ [FORMAT=%{&ff}]\ [TYPE=%Y]\ [POS=%l,%v][%p%%]\ %{strftime(\"%d/%m/%y\ -\ %H:%M\")}
```

