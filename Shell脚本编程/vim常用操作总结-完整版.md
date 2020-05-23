### 技术交流QQ群:1027579432，欢迎你的加入！

### 技术交流QQ群:1027579432，欢迎你的加入！
#### 1.vim介绍
- vim的三种工作模式
    - 命令模式：shell默认情况下打开的是命令模式，对源文件进行部分的修改、编辑
    - 编辑模式：对源文件进行编辑，书写代码
    - 末行模式：对源文件进行查询、替换、行跳转、保存与退出
- vim向导：shell命令行界面中输入：vimtutor
#### 2.vim命令模式下的相关操作
- 保存退出：ZZ
- 代码格式化：gg=G
- 光标的移动：
    - 向上：K
    - 向下：J
    - 向左：H
    - 向右：L
    - 光标移动到行首：0
    - 光标移动到行尾：$
    - 光标移动到当前文件的首行：gg
    - 光标移动到当前文件的末尾：G
    - 光标快速定位到某一行：行号+G
    - 当前行向下移动n行：行号n + 回车
#### 3.vim命令模式下的删除操作
- 删除当前光标后面位置的字符：x
- 删除当前光标前面位置的字符：X
- 撤销删除：u
- 反撤销删除：ctr+r
- 删除当前光标所在位置的单词(**一定要将光标移动到单词的第一个字符位置**)：dw
- 删除当前光标后面的所有字符：D或d$
- 删除当前光标前面的所有字符：d0
- 删除当前光标所在的整行：dd
- 删除当前光标所在行的连续多个整行：3dd(连续删除3整行)
- 删除当前文件中的所有行：先将光标移动到文件的首行，输入dG；或者 先将光标移动到文件的末尾，输入dgg
#### 4.vim命令模式下的复制和粘贴操作
- 复制光标所在的当前行：yy
- 移动光标至目标行：p(**粘贴的内容会复制到目标行的下一行**)、P(**粘贴的内容会复制到目标行的上一行**)
- 复制连续的n行：nyy
- 剪切后粘贴：dd删除后，再按p进行粘贴
- **可视模式**：复制光标所在的当前行部分内容：先按下v进入可视模式，按H、J、k、L选择你需要复制的内容，再按下y完成复制操作。将光标移动到你想要粘贴的目标行，按下p后你所选择要复制的内容就会**粘贴至目标行**。
    - 移动光标：H、J、K、L
    - 复制：y
    - 删除：d
    - 粘贴：
        - p:粘贴到光标的后面
        - P:粘贴的光标的前面
- 替换操作：
    - 替换光标当前所在位置的字符：r
    - 替换从光标当前所在位置开始之后的字符：R
#### 5.vim命令模式下的查找操作
- /xxx:查找关键字xxx
- ?xxx:查找关键字xxx
- 关键字xxx切换：n/N
- \#:光标移动到待搜索的关键字xxx上，再按下#键
- 查看man手册：章节号 + K
- man手册使用示例：man 3 printf
- man手册章节号说明：
    - 1:可执行程序或 shell 命令
    - 2:系统调用(内核提供的函数)
    - 3:库调用(程序库中的函数)
    - 4:特殊文件(通常位于 /dev)
    - 5:文件格式和规范，如/etc/passwd
    - 6:游戏
    - 7:杂项(包括宏包和规范，如man(7)，groff(7))
    - 8:系统管理命令(通常只针对 root用户)
    - 9:内核例程 [非标准]
#### 6.vim下的编辑模式
- 从命令模式下切换到编辑模式：
    - **a**：从当前光标的后面开始插入新的内容
    - A: 行尾开始插入新的内容
    - **i**: 从当前光标的前面插入新的内容
    - I: 行首开始插入新的内容
    - **o**: 从当前光标下面创建新的一行
    - **O**: 从当前光标上面创建新的一行
    - s: 删除光标盖住的字符
    - S: 删除光标所在行
#### 7.vim末行模式与命令模式切换、退出与保存
- 编辑模式切换回命令模式：按一次ESC
- 末行模式切换回命令模式：按两次ESC或者末行模式下执行一个命令
- 命令模式切换回末行模式：输入：即可进入末行模式
- **末行模式下**：
    - 行跳转：:行号+回车
    - 保存退出: :wq或者:x
    - 不保存退出: :q!
    - 退出: :q
    - 保存不退出: :w(**此时进入命令模式**)
#### 8.vim末行模式的替换操作
- 替换光标所在行的旧字符：:s/旧字符/新字符/gc
    - g: 替换当前行所有的旧字符；
    - c: 替换当前行所有的旧字符并在替换时添加提示信息；
- 替换一个范围内[x,y]（x、y表示行号）的旧字符: :x,ys/旧字符/新字符/gc
- 替换当前文本内的所有旧字符: :%s/旧字符/新字符/gc
#### 9.vim分屏操作
- 当前文件分屏：
    - 水平分屏：:sp
    - 垂直分屏: :vsp
    - 退出分屏：:q
- 两个屏幕显示不同的文件：
    - :sp 文件名
    - :vsp 文件名
- 打开文件时分屏：
    - 水平分屏: vim -o 文件名1 文件名2
    - 垂直分屏: vim -O 文件名1 文件名2
- 屏幕的切换：ctrl + w + w
- 屏幕的关闭： 
    - 关闭所有: :qall
    - 保存关闭所有: :wqall
    - 保存所有： :wall
- vim末行模式下执行shell命令： :!具体的shell命令
#### 10.vim配置文件
- 用户级别：~/.vimrc
    ```
    inoremap ' ''<ESC>i
    inoremap " ""<ESC>i
    inoremap ( ()<ESC>i
    inoremap [ []<ESC>i
    inoremap { {<CR>}<ESC>O
    "设置跳出自动补全的括号
    func SkipPair()  
        if getline('.')[col('.') - 1] == ')' || getline('.')[col('.') - 1] == ']' || getline('.')[col('.') - 1] == '"' || getline('.')[col('.') - 1] == "'" || getline('.')[col('.') - 1] == '}'  
                return "\<ESC>la"  
        else  
            return "\t"  
        endif  
    endfunc  
    " 将tab键绑定为跳出括号  
    inoremap <TAB> <c-r>=SkipPair()<CR>

    "打开文件类型检测, 加了这句才可以用智能补全
    set completeopt=longest,menu
    :set nu
    set shortmess=atI " 启动的时候不显示那个援助乌干达儿童的提示 
    set showcmd "输出的命令显示出来

    autocmd InsertLeave * se nocul " 用浅色高亮当前行
    autocmd InsertEnter * se cul " 用浅色高亮当前行 

    set foldenable " 允许折叠
    set foldmethod=manual " 手动折叠 

    set nocompatible "去掉讨厌的有关vi一致性模式,避免以前版本的一些bug和局限 

    " 映射全选+复制 ctrl+a
    map <C-A> ggVGY
    map! <C-A> <Esc>ggVGY
    map <F12> gg=G
    " 选中状态下 Ctrl+c 复制
    vmap <C-c> "+y
    "去空行
    nnoremap <F2> :g/^/s*$/d<CR> 

    "代码补全
    set completeopt=preview,menu 

    "共享剪贴板
    set clipboard+=unnamed 

    " Tab键的宽度
    set tabstop=4
    " 统一缩进为4
    set softtabstop=4
    set shiftwidth=4
    " 不要用空格代替制表符
    set noexpandtab
    " 在行和段开始处使用制表符
    set smarttab

    "禁止生成临时文件
    set nobackup
    set noswapfile
    "搜索忽略大小写
    set ignorecase
    "搜索逐字符高亮
    set hlsearch
    set incsearch

    set gdefault "行内替换
    set encoding=utf-8
    set fileencodings=utf-8,ucs-bom,shift-jis,gb18030,gbk,gb2312,cp936,utf-16,big5,euc-jp,latin1 "

    "编码设置

    set guifont=Menlo:h16:cANSI "设置字体
    set langmenu=zn_CN.UTF-8
    set helplang=cn  "语言设置

    set ruler "在编辑过程中，在右下角显示光标位置的状态行

    set laststatus=1  "总是显示状态行

    set showcmd "在状态行显示目前所执行的命令，未完成的指令片段也会显示出来

    set scrolloff=3 "光标移动到buffer的顶部和底部时保持3行的距离"""""""

    set autowrite "在切换buffer时自动保存当前文件"

    set showmatch
    set selection=exclusive
    set selectmode=mouse,key

    set wildmenu  "增强模式中的命令行自动完成操作

    set linespace=2 "字符间插入的像素行数目
    set whichwrap=b,s,<,>,[,] "开启normal 或visual模式下的backspace键空格键，左右方向键,insert或replace模式下的左方向键，右方向键的跳行功能

    filetype plugin indent on
    "分为三部分命令:file on,file plugin on,file indent on 分别是自动识别文件类型, 用用文件类型脚本,使用缩进定义文件""]""

    filetype on "打开文件类型检测功能

    autocmd BufNewFile *.cpp,*.[ch] exec ":call SetTitle()"
    func SetTitle()
        call setline(1,"/************************************************************************")
        call append(line("."), "    > File Name: ".expand("%")) 
        call append(line(".")+1, "    > Author: CurryCoder") 
        call append(line(".")+2, "    > Mail: 1217096231@qq.com ") 
        call append(line(".")+3, "    > Created Time: ".strftime("%c")) 
        call append(line(".")+4, "************************************************************************/") 
        call append(line(".")+5, "")
        if &filetype == 'cpp'
            call append(line(".")+6, "#include<iostream>")
            call append(line(".")+7, "using namespace std;")
            call append(line(".")+8, "")
        endif
        if &filetype == 'c'
            call append(line(".")+6, "#include<stdio.h>")
            call append(line(".")+7, "")
        endif
        "新建文件后，自动定位到文件末尾（这个功能实际没有被实现，即下面的语句无效，暂不知道原因）
        autocmd BufNewFile * normal G
    endfunc

    "C,C++ 按F5编译运行
    map <F5> :call CompileRunGcc()<CR>
    func! CompileRunGcc()
        exec "w"
        if &filetype == 'c'
            exec "!g++ % -o %<"
            exec "!time ./%<"
        elseif &filetype == 'cpp'
            exec "!g++ % -o %<"
            exec "!time ./%<"
        endif    
    endfunc

    "C,C++的调试
    map <F8> :call Rungdb()<CR>
    func! Rungdb()
    exec "w"
    exec "!g++ % -g -o %<"
    exec "!gdb ./%<"
    endfunc
    "
    set tags=/home/lsh/files/tags

    let Tlist_Auto_Open = 1
    let Tlist_Ctags_Cmd = '/usr/local/bin/ctags'
    let Tlist_Show_One_File = 1
    let Tlist_Exit_OnlyWindow = 1
    """"""""""""""""""""""""
    ```
- 系统级别：/etc/vim/vimrc


