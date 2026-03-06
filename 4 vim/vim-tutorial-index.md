## Vim Intro

[[vim-genius-intro]]

---

## Vim Arglist

- `:arg` to show the files in your current arglist
- `:argdo` to run a command on your arglist
- `:argadd` or `:arga` to add files to your arglist
- `:argdelete` or `:argd` to remove files from your arglist
- `:argdo update` to save all changes to your arglist
- `:argdo undo` to undo changes to your arglist

`:arg` View arglist
`:argdo %s/sam/bob/g` Replace "sam" with "bob" in arglist
`:arga names.rb` or `:argadd names.rb` Add names.rb to arglist
`:argd *` or `:argdelete *` Clear arglist
`:argdo update` Save files in arglist
`:argdo undo` Undo changes to arglist

---

## Vim Macros

- `qq` to start recording
- `q` to stop recording
- `@q` to play the macro
- `.` to repeat last command 

`qq` Start recording macro in "q" register
`q` Stop recording macro
`@q` Play "q" macro
`:normal @q` Play "q" macro across visual selection

---

## Rails Vim

[rails.vim参考](http://www.vimgenius.com/lessons/rails-vim)