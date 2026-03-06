```bash
#### ==============================
##### 1. 基础行为设置
##### ==============================

# 修改默认前缀键（原本是 Ctrl-b）
# Ctrl-a 更符合类 screen / Vim 用户习惯
set -g prefix C-a
unbind C-b
bind C-a send-prefix

# 减少 ESC 延迟（Vim 用户必须）
# 默认 500ms，太慢
set -sg escape-time 10

# 提高命令重复响应时间（按住 resize 等）
set -g repeat-time 600

# 启用鼠标支持（选择、拖动、调整大小）
set -g mouse on

# 支持 256 色和真彩色
set -g default-terminal "tmux-256color"
set -ga terminal-overrides ",xterm-256color:Tc"

# 启用焦点事件（neovim 需要）
set -g focus-events on

#### ==============================
##### 2. 窗口 / Pane 行为优化
##### ==============================

# 新建窗口继承当前目录
bind c new-window -c "#{pane_current_path}"

# 横向分屏继承当前目录
bind % split-window -h -c "#{pane_current_path}"

# 纵向分屏继承当前目录
bind '"' split-window -v -c "#{pane_current_path}"

# 使用 hjkl 切换 pane（类 Vim）
bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R

# 调整 pane 大小（可按住）
bind -r H resize-pane -L 5
bind -r J resize-pane -D 5
bind -r K resize-pane -U 5
bind -r L resize-pane -R 5

# 最大化 / 还原 pane
bind m resize-pane -Z

##### ==============================
##### 5. 状态栏美化（极简风）
##### ==============================

# 状态栏刷新频率
set -g status-interval 5

# 长度限制
set -g status-left-length 50
set -g status-right-length 100

# 左侧显示 Session 名称 + 用户
set -g status-left "#[fg=green]#S #[fg=yellow]| #(whoami)"

# 右侧显示 日期 + 时间
set -g status-right "#[fg=cyan]%Y-%m-%d #[fg=white]%H:%M"

# 整体风格
set -g status-style bg=black,fg=white

# Pane 边框颜色
set -g pane-border-style fg=grey
set -g pane-active-border-style fg=green


##### ==============================
##### 6. 提高操作体验
##### ==============================

# 更快识别粘贴
set -g assume-paste-time 1

# 分屏后自动切换到新 pane
setw -g aggressive-resize on
```