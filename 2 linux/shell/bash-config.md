## Bash 美化

首先使用 [starship](https://starship.rs/) 来美化 Bash 的提示符：

`$ curl -sS https://starship.rs/install.sh | sh`

然后在 `~/.bashrc` 文件的末尾添加以下内容：

`eval "$(starship init bash)"`

可以使用以下命令添加（注意，`>>` 是追加到文件末尾，因此这条命令只要执行一次即可）：

`$ echo 'eval "$(starship init bash)"' >> ~/.bashrc`

重新打开一个终端就可以看到效果了。更进一步的修改请参考 [starship 的配置文档](https://starship.rs/config/)。

---
