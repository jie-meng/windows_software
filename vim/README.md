在用户目录下

放置. vimrc
vim/autoload下放置plug.vim （从vim-plug GitHub下载源码）
vim/plugged下放置各插件，主要有: fzf.vim, fzf, gv.vim, nerdtree, vim-colorschemes, vim-easymotion, vim-fugitive, vim-polyglot（从各插件GitHub下载源码）

将ag，ctags，fzf这几个exe下载后放到一个bin目录下，配好path可被命令行发现。

修改fzf-vim/autoload/fzf/vim/vim.vim:
function! fzf#vim#with_preview(...)里的
let window = arg[0] 改成 let window = 'up'，让弹出cmd显示的内容更宽

将function! fzf#vim#ag(query, ...)里的
let command = ag_opts . ' -- ' . fzf#shellescape(query) 改成 let command = ag_opts . ' -- ' . s: strreplace(fzf#shellescape(query), "'", "\\""
单引号转双引号

Google 搜索 vim strreplace，找到stack overflow
 using-substitute-on-variable
文章，复制 strfind 和 strreplace 两个函数到这个文件里。

https://stackoverflow.com/questions/4864073/using-substitute-on-a-variable

    func! s:strfind(s,find,start)
            if type(a:find)==1
                    let l:i = a:start
                    while l:i<len(a:s)
                            if strpart(a:s,l:i,len(a:find))==a:find
                                    return l:i
                            endif
                            let l:i+=1
                    endwhile
                    return -1
            elseif type(a:find)==3
                    " a:find is a list
                    let l:i = a:start
                    while l:i<len(a:s)
                            let l:j=0
                            while l:j<len(a:find)
                                    if strpart(a:s,l:i,len(a:find[l:j]))==a:find[l:j]
                                            return [l:i,l:j]
                                    endif
                                    let l:j+=1
                            endwhile
                            let l:i+=1
                    endwhile
                    return [-1,-1]
            endif
    endfunc


    func! s:strreplace(s,find,replace)
            if len(a:find)==0
                    return a:s
            endif
            if type(a:find)==1 && type(a:replace)==1
                    let l:ret = a:s
                    let l:i = s:strfind(l:ret,a:find,0)
                    while l:i!=-1
                            let l:ret = strpart(l:ret,0,l:i).a:replace.strpart(l:ret,l:i+len(a:find))
                            let l:i = s:strfind(l:ret,a:find,l:i+len(a:replace))
                    endwhile
                    return l:ret
            elseif  type(a:find)==3 && type(a:replace)==3 && len(a:find)==len(a:replace)
                    let l:ret = a:s
                    let [l:i,l:j] = s:strfind(l:ret,a:find,0)
                    while l:i!=-1
                            let l:ret = strpart(l:ret,0,l:i).a:replace[l:j].strpart(l:ret,l:i+len(a:find[l:j]))
                            let [l:i,l:j] = s:strfind(l:ret,a:find,l:i+len(a:replace[l:j]))
                    endwhile
                    return l:ret
            endif


    endfunc
