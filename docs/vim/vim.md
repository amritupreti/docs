# Vim

Vim is a highly efficient, keyboard-driven text editor known for its modal editing approach. Unlike traditional editors, Vim uses different modes for different tasks — making navigation and editing fast once you get used to it.

---

## Getting Started

- **Exit Vim**:  
  - `:q` → Quit  
  - `:wq` → Save and quit  
  - `:q!` → Quit without saving

- **Help**:  
  - `:help` or `:h keyword` → Open Vim's built-in documentation  
  - `K` → Open the man page for the word under the cursor

- **Open Terminal**:  
  - `:terminal` → Open a terminal window within Vim

---


## Saving default settings
To save your custom settings, create or edit the `.vimrc` file in your home directory:

```vim
" Example .vimrc settings
set number          " Show line numbers
set relativenumber  " Show relative line numbers
set tabstop=4       " Set tab width to 4 spaces
set shiftwidth=4    " Set indentation width to 4 spaces
set expandtab       " Use spaces instead of tabs
set autoindent      " Enable automatic indentation
set smartindent     " Enable smart indentation
set hlsearch        " Highlight search results
set incsearch       " Incremental search
set ignorecase      " Ignore case in searches
set smartcase       " Override ignorecase if uppercase letters are used
set clipboard=unnamedplus " Use system clipboard
set mouse=a         " Enable mouse support
set cursorline      " Highlight the current line
set background=dark " Set background to dark for better contrast
syntax on           " Enable syntax highlighting
```

---
## Modes in Vim

Vim uses different **modes** for different tasks. Understanding these is key to using Vim efficiently:

| Mode                | Purpose                                  | How to Enter                          |
|---------------------|------------------------------------------|---------------------------------------|
| **Normal**          | Navigate, edit, and run commands         | `Esc`                                 |
| **Insert**          | Type and insert text                     | `i`, `I`, `a`, `A`, `o`, `O`          |
| **Visual**          | Select and manipulate text               | `v` (character), `V` (line), `Ctrl+v` (block) |
| **Command-line**    | Run commands (save, search, etc.)        | `:` (from Normal mode)                |

---

## Cursor Movement

### Basic Navigation

- `h`, `j`, `k`, `l` → Move left, down, up, right
- `gj`, `gk` → Move by screen line in wrapped text
- `0` → Start of line  
- `^` → First non-blank character  
- `$` → End of line  
- `g_` → Last non-blank character of the line  
- `H`, `M`, `L` → Top, middle, bottom of screen  
- `zz`, `zt`, `zb` → Center, top, bottom the current line on screen  

### Word Navigation

- `w`, `W` → Next word (W ignores punctuation)  
- `e`, `E` → End of word (E ignores punctuation)  
- `b`, `B` → Previous word (B ignores punctuation)  
- `ge`, `gE` → End of previous word  

### Paragraph / Block Movement

- `{`, `}` → Move to previous/next paragraph (or code block)

### Character Search

- `fx`, `Fx` → Find next/previous occurrence of character `x`  
- `tx`, `Tx` → Move right/left before character `x`  
- `;`, `,` → Repeat last `f`, `t`, `F`, `T` in same or opposite direction

### Line Navigation

- `gg` → Go to first line  
- `G` → Go to last line  
- `5gg` or `5G` → Go to line 5  
- `gd`, `gD` → Go to local/global declaration  
- `%` → Jump to matching bracket ( (), {}, [] )

### Scrolling

- `Ctrl+e`, `Ctrl+y` → Scroll screen down/up by one line (without moving cursor)
- `Ctrl+f`, `Ctrl+b` → Page forward/back
- `Ctrl+d`, `Ctrl+u` → Half-page down/up

---

## Insert Mode

To enter insert mode, use:

- `i`, `I` → Insert before cursor / at start of line  
- `a`, `A` → Append after cursor / at end of line  
- `o`, `O` → Open new line below / above  
- `ea` → Insert at end of current word  

While in insert mode:

- `Ctrl+h` → Delete character before cursor  
- `Ctrl+w` → Delete word before cursor  
- `Ctrl+j` → Line break  
- `Ctrl+t` / `Ctrl+d` → Indent / de-indent  
- `Ctrl+n`, `Ctrl+p` → Autocomplete next / previous suggestion  
- `Ctrl+r{register}` → Insert contents of a register  
- `Ctrl+o{cmd}` → Temporarily run Normal-mode command  

Exit insert mode with `Esc` or `Ctrl+c`.

---

## Editing Text

- `r`, `R` → Replace one or many characters  
- `J`, `gJ` → Join lines with or without space  
- `xp` → Swap two characters  
- `u`, `U` → Undo last change / entire line  
- `Ctrl+r` → Redo  
- `.` → Repeat last command  

### Change Commands

- `cc` → Change (replace) entire line  
- `c$` or `C` → Change to end of line  
- `ciw` → Change inner word  
- `cw`, `ce` → Change to end of word  

### Substitution

- `s`, `S` → Substitute character / line  
- `gwip` → Reformat a paragraph (like wrapping)  
- `g~`, `gu`, `gU` → Toggle / lowercase / uppercase with motion

---

## Visual Mode

Used for selecting and manipulating text.

- `v`, `V`, `Ctrl+v` → Character, line, block selection  
- `o` / `O` → Jump to other end/corner of selection  
- `>`, `<` → Indent / de-indent selected text  
- `y`, `d`, `~`, `u`, `U` → Yank, delete, toggle case, lowercase, uppercase  
- `aw`, `ab`, `aB`, `ib`, `iB` → Text objects (word, block with brackets, etc.)

Exit visual mode with `Esc` or `Ctrl+c`.

---

## Registers

- `:registers` → List all available registers  
- `"xy`, `"xp` → Copy to / paste from register x  
- `"+y`, `"+p` → Use system clipboard  
- `"0` → Last yank  
- `"*`, `"+" → X11 clipboard registers  

Special registers include:
- `"` → Unnamed
- `%` → Current file
- `/` → Last search
- `:` → Last command
- `.` → Last inserted text

---

## Marks and Jumps

- `ma` → Set mark 'a' at current location  
- `` `a `` → Jump to mark 'a'  
- `` `. `` → Jump to last change  
- `` `" `` → Jump to last edited file position  
- `:marks`, `:jumps`, `:changes` → Show history  
- `Ctrl+o`, `Ctrl+i` → Go to older/newer jump  
- `g;`, `g,` → Older/newer change  
- `Ctrl+]` → Jump to tag under cursor (requires ctags)

---

## Macros

- `qa` → Start recording macro into register `a`  
- `q` → Stop recording  
- `@a` → Run macro in register `a`  
- `@@` → Rerun last macro

---

## Copy, Cut, Paste

### Yank (Copy)

- `yy`, `2yy` → Yank 1 or 2 lines  
- `yw`, `yiw` → Yank word / inner word  
- `y$`, `Y` → Yank to end of line  
- `yaw` → Yank word with surrounding space

### Delete (Cut)

- `dd`, `2dd` → Delete 1 or 2 lines  
- `dw`, `diw`, `daw` → Delete word, inner word, or word with space  
- `x` → Delete character under cursor  
- `D` → Delete to end of line  
- `:3,5d` → Delete lines 3 to 5  
- `:g/pattern/d` → Delete all lines matching pattern  
- `:g!/pattern/d` → Delete lines NOT matching pattern

### Paste

- `p`, `P` → Paste after / before cursor  
- `gp`, `gP` → Paste and leave cursor after new text

---

## Indentation

- `>>`, `<<` → Indent / de-indent current line  
- `>%`, `<%` → Indent / de-indent code block  
- `>ib`, `>at` → Indent inside block or tag  
- `gg=G` → Re-indent entire file  
- `3==` → Re-indent 3 lines  
- `]p` → Paste and auto-indent

---

## Search and Replace

- `/pattern` → Search forward  
- `?pattern` → Search backward  
- `n`, `N` → Next / previous match  
- `:noh` → Clear highlight  
- `:%s/old/new/g` → Replace all  
- `:%s/old/new/gc` → Confirm each replacement

### Search Multiple Files

- `:vimgrep /pattern/ **/*` → Search recursively  
- `:cn`, `:cp` → Jump to next / previous match  
- `:copen`, `:cclose` → Open / close quickfix list

---

## Tabs

- `:tabnew` → Open new tab  
- `gt`, `gT` → Next / previous tab  
- `:tabm 2` → Move current tab to position 2  
- `:tabclose`, `:tabonly` → Close tabs  
- `:tabdo command` → Run command in all tabs

---

## Buffers and Files

- `:e file.txt` → Edit a file  
- `:bnext`, `:bprev` → Next / previous buffer  
- `:bd` → Close buffer  
- `:ls`, `:buffers` → List buffers  
- `:b 2` → Switch to buffer 2  
- `:b filename` → Switch by file name

---

## Windows and Splits

- `:split file` → Horizontal split  
- `:vsplit file` → Vertical split  
- `Ctrl+ws`, `Ctrl+wv` → Create horizontal / vertical split  
- `Ctrl+ww` → Switch window  
- `Ctrl+wq`, `Ctrl+wx`, `Ctrl+w=` → Close, exchange, equal size  
- `Ctrl+wh`, `Ctrl+wl`, `Ctrl+wj`, `Ctrl+wk` → Move between splits  
- `Ctrl+wH`, `Ctrl+wL`, `Ctrl+wJ`, `Ctrl+wK` → Rearrange windows

---

## Save and Exit

- `:w` → Save file  
- `:wq`, `:x`, `ZZ` → Save and quit  
- `:q` → Quit  
- `:q!`, `ZQ` → Quit without saving  
- `:wqa` → Save and quit all files  
- `:w !sudo tee %` → Save with elevated permissions
