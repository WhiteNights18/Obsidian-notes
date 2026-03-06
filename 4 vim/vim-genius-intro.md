## vim motion

`h` Move cursor left
`l` Move cursor right
`j` Move cursor down
`k` Move cursor up

`w` Next word
`2w` Go two words forward
`W` Next WORD (skip special characters)

`$` Go to end of text on current line
`^` Go to beginning of text on current line
`0` Go to beginning of current line
`}` Next paragraph
`(` Previous sentence
`)` Next sentence

`e` Go to end of word
`2e` Go to end of next word
`3e` Go to end of third word ahead
`b` Beginning of word

`ge` End of previous word
`gg` Go to first line in file
`50G` or `:50` Go to line 50
`G` Go to last line in file

`/waldo` Search for "waldo"
`?carmen` Search backwards for "carmen"

`n` Go to next search result
`N` Go to previous search result

`ctrl+o` Jump to previous location (jump back)
`ctrl+i` Jump to next location (jump forward)

`%` Go to matching parentheses or brackets

---

## vim copy and paste

`yw` Yank word
`vwy` Visual select word, then yank
`y$` Yank to end of current line
`yy` Yank line
`p` Paste after cursor
`P` Paste before cursor
`:reg` or `:register` Show yank register
`"0p` Paste from 0 register
`"ap` Paste from "a" register

`ctrl+r "` Paste from default register in insert mode
`ctrl+r 0` Paste from 0 register in insert mode
`ctrl+r a` Paste from "a" register in insert mode

---

## vim mode

`:q` Close file
`:q!` Close file, don't save changes
`:w` Save changes to file
`:wq` or `:x` or `ZZ` Save changes and close file
`:w play.rb` Save current file as "play.rb"
`:r hat.rb` Read in file "hat.rb"

`x` Delete character at cursor
`i` Insert at cursor
`I` Insert at beginning of line
`a` Append at cursor
`A` Append at end of line
`R` Enter replace mode
`r` Replace character under cursor
`cw` Change word
`c$` or `C` Change to end of line
`c2w` Change two words

`o` Open new line below
`O` Open new line above

`v` Open visual mode
`vw` Visual select word
`vwd` or `vwx` Visual select word, then delete word

`escape` or `ctrl+[` Exit insert mode

---

`dw` Delete word
`d$` or `D` Delete to end of line
`d2w` Delete two words
`dd` Delete entire line
`2dd` Delete two lines

`u` Undo last change
`U` Undo changes on entire line
`ctrl+r` Redo changes

---

`:%s/bad/good` Replace bad with good in current line
`:%s/hi/bye/g` Replace hi with bye in entire file
`:%s/x/y/gc` Replace x with y in entire file, prompt for changes

---

`:!ls` Run shell command ls
`set ignorecase` or `set ic` Change search settings to ignore case
`set noignorecase` or `set noic` Change search settings to use case

`:e sun.rb` Open file "sun.rb"
`:help w` Get help for "w" command
`:help e` Get help for "e" command

---