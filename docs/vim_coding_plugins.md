
## [Abbreviations](https://davidxmoody.com/2014/better-vim-abbreviations/)

In order to reduce the amount of typing and fix common typos, I use the Vim
abbreviations support. Those are split into two files,
`~/.vim/abbreviations.vim` for abbreviations that can be used in every type of
format and `~/.vim/markdown-abbreviations.vim` for the ones that can interfere
with programming typing.

Those files are sourced in my `.vimrc`

```vim
" Abbreviations
source ~/.vim/abbreviations.vim
autocmd BufNewFile,BufReadPost *.md source ~/.vim/markdown-abbreviations.vim
```

To avoid getting worse in grammar, I don't add abbreviations for words that
I doubt their spelling or that I usually mistake. Instead I use it for common
typos such as `teh`.

The process has it's inconveniences:

* You need different abbreviations for the capitalized versions, so you'd need
    two abbreviations for `iab cant can't` and `iab Cant Can't`
* It's not user friendly to add new words, as you need to open a file.

The [Vim Abolish plugin](https://github.com/tpope/vim-abolish) solves that. For
example:

```vim
" Typing the following:
Abolish seperate separate

" Is equivalent to:
iabbrev seperate separate
iabbrev Seperate Separate
iabbrev SEPERATE SEPARATE
```

Or create more complex rules, were each `{}` gets captured and expanded with
different caps

```vim
:Abolish {despa,sepe}rat{e,es,ed,ing,ely,ion,ions,or}  {despe,sepa}rat{}
```

With a bang (`:Abolish!`) the abbreviation is also appended to the file in
`g:abolish_save_file`. By default `after/plugin/abolish.vim` which is loaded by
default.

Typing `:Abolish! im I'm` will append the following to the end of this file:

```vim
Abolish im I'm
```

To make it quicker I've added a mapping for `<leader>s`.

```vim
nnoremap <leader>s :Abolish!<Space>
```

Check the
[README](https://github.com/tpope/vim-abolish/blob/master/doc/abolish.txt) for
more details.

### Troubleshooting

Abbreviations with dashes or if you only want the first letter in capital need
to be specified with the first letter in capital letters as stated in [this
issue](https://github.com/tpope/vim-abolish/issues/30).

```vim
Abolish knobas knowledge-based
Abolish w what
```

Will yield `KnowledgeBased` if invoked with `Knobas`, and `WHAT` if invoked with
`W`. Therefore the following definitions are preferred:

```vim
Abolish Knobas Knowledge-based
Abolish W What
```

## Auto complete prose text

Tools like [YouCompleteMe](https://github.com/ycm-core/YouCompleteMe) allow you
to auto complete variables and functions. If you want the same functionality for
prose, you need to enable it for markdown and text, as it's disabled by default.

```vim
let g:ycm_filetype_blacklist = {
      \ 'tagbar' : 1,
      \ 'qf' : 1,
      \ 'notes' : 1,
      \ 'unite' : 1,
      \ 'vimwiki' : 1,
      \ 'pandoc' : 1,
      \ 'infolog' : 1
  \}
```

When writing prose you don't need all possible suggestions, as navigating the
options is slower than keep on typing. So I'm limiting the results just to one,
to avoid unnecessary distractions.

```vim
" Limit the results for markdown files to 1
au FileType markdown let g:ycm_max_num_candidates = 1
au FileType markdown let g:ycm_max_num_identifier_candidates = 1
```

## Find synonyms

Sometimes the prose linters tell you that a word is wordy or too complex, or you may be
repeating a word too much. The [thesaurus query
plugin](https://github.com/Ron89/thesaurus_query.vim) allows you to search
synonyms of the word under the cursor. Assuming you use Vundle, add the
following lines to your config.

!!! note "File: ~/.vimrc"

    ```vim
    Plugin 'ron89/thesaurus_query.vim'

    " Thesaurus
    let g:tq_enabled_backends=["mthesaur_txt"]
    let g:tq_mthesaur_file="~/.vim/thesaurus"
    nnoremap <leader>r :ThesaurusQueryReplaceCurrentWord<CR>
    inoremap <leader>r <esc>:ThesaurusQueryReplaceCurrentWord<CR>
    ```

Run `:PluginInstall` and download the thesaurus text from [gutenberg.org](http://www.gutenberg.org/files/3202/files/)

Next time you find a word like `therefore` you can press
`:ThesaurusQueryReplaceCurrentWord
` and you'll get a window with the following:

```
In line: ... therefore ...
Candidates for therefore, found by backend: mthesaur_txt
Synonyms: (0)accordingly (1)according to circumstances (2)and so (3)appropriately (4)as a consequence
          (5)as a result (6)as it is (7)as matters stand (8)at that rate (9)because of that (10)because of this
          (11)compliantly (12)conformably (13)consequently (14)equally (15)ergo (16)finally (17)for that
          (18)for that cause (19)for that reason (20)for this cause (21)for this reason (22)for which reason
          (23)hence (24)hereat (25)in that case (26)in that event (27)inconsequence (28)inevitably
          (29)it follows that (30)naturally (31)naturellement (32)necessarily (33)of course (34)of necessity
          (35)on that account (36)on that ground (37)on this account (38)propter hoc (39)suitably
          (40)that being so (41)then (42)thence (43)thereat (44)therefor (45)thus (46)thusly (47)thuswise
          (48)under the circumstances (49)whence (50)wherefore (51)wherefrom
Type number and <Enter> (empty cancels; 'n': use next backend; 'p' use previous backend):
```

If for example you type `45` and hit enter, it will change it for `thus`.


