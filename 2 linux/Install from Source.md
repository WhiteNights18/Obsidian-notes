# "Mine of Information - Beginner's Guide to Installing from Source"

## Introduction

This document is intended for users of open-source operating systems who wish to install software direct from the original authors, rather than relying solely on the software (and versions) provided by their operating system. It is written for those who are not familiar with the concept of downloading software in source-code form, providing background information about relevant commands and the process in general.

## Concepts Discussed

- Development and distribution: independent software developers, multi-os support, releases (archives), version-control
- Distributions: binary packages and package-managers
- Downloading: http, ftp, wget, checksums and signatures
- Archive files: tar, zip, gzip
- Common files: README and INSTALL; required dependencies
- Patching with sed/awk/patch
- Building and Installing: configure, make, cmake, perl and python
- Building and Installing Documentation
- Compiler settings and stripping; handing errors
- Post-installation

## Development and Distribution

A typical operating system consists of hundreds of different applications. In a proprietary operating system, the producer/seller of that operating system typically owns and manages the development of all that software. However open-source systems are usually created by obtaining and integrating applications invented and maintained by many independent groups - or even single independent developers. In addition, an open-source software project is often used in a variety of different operating systems - eg Linux-based, BSD-based, Hurd, and sometimes even integrated into proprietary operating systems such as Solaris, Mac, even Windows (if the licence allows).

The original software developers are sometimes referred to by distribution maintainers and end-users as the *upstream source* or simply *upstream*. Similarly, the developers often refer to the distributions that use their code, or the end-users as *downstream*.

Most open-source projects store their source code in a version-control system which is accessible over the internet (read-only for anonymous users). For such projects it is possible to download the very latest “in-progress” files, or to download the set of files “tagged” with some release-id (version number). However it is often not very efficient to do this; projects usually make regular *releases* by creating an *archive file* containing all the relevant files and make it available for download. For projects that do not have a public version-control system, such periodically released *archive files* are the only way to obtain the sourcecode directly.

## Distributions, Binary Packages, and Compiling From Source

There are many *operating system distributions* which do the work of finding, downloading and tweaking all the most-commonly used software packages. Several also compile it, and make the binary-form results available. There are many benefits, including quicker installation, one location to *find* relevant software, and particularly a supply of relevant *security patches*: the distribution maintainers watch for security updates and make it easy for end users to notice and install them.

However such distributions often don’t include the very latest version of software; if you need a newer version than the one provided by your distribution then it may be necessary to build it yourself. Software is also often very customisable at install-time, particularly with the ability to omit features that are not needed. Because a distribution needs to keep a wide range of users happy, they tend to include *every* feature in the compiled applications they distribute; you as the end-user may be able to tune an application better by compiling it yourself.

Distributions generally include a *package manager* that keeps a database of which software is installed. It isn’t a good idea to go changing the software on a machine “behind the back” of the package manager; all sorts of odd behaviour could occur later. If your distribution has a package manager, then please read this document for background information - but then consult the documentation for your distribution to find out how to install software without confusing your package manager. Usually this involves writing a *package specification* that describes the software and then using the local package manager to compile and install the software. This applies to all distributions with package-managers, regardless of whether applications are distributed in binary (pre-compiled) or source-code form.

## Downloading and Security

The usual way to download an archive file is by using a web-browser and clicking on a “download” button, clicking a link, or right-clicking a link and selecting “save as”. This downloads the file using the “http” protocol. Some sites instead make files available via the older “ftp” protocol. Many web-browsers support this too, ie clicking a link can also work here. Alternately, there are ftp client applications.

The “wget” or “curl” command-line application can also be used under unix-like operating systems to fetch a file via http or ftp when the correct URL is known.

Sites which publish software that is very frequently downloaded often have “mirror” sites set up, ie helpful people keep *copies* of the files in various places around the world. The original site usually has a list of the available *mirrors*, and you should choose one that is near to you. This helps reduce the cost of network bandwidth on the original publisher, and usually also lets you download faster.

Both the “http” and “ftp” network protocols can be intercepted by criminals or other undesirable parties who can then modify data as it downloads. Data can also potentially be corrupted by accident while underway (though this is not common). And when using a mirror-site, it is possible that someone has modified the files there (ie that the files on the mirror are not the same as those from the original publisher). It is therefore a good idea to *verify* that what you downloaded is what the original publisher intended.

Many sites provide an *md5sum* file for each archive file, which holds a *checksum* of the contents of the file; sometimes a single *md5sums* file holds checksums for a number of other files. You should always obtain an md5sums file from the *original site*, never a mirror. And you should download it via the secure *https* protocol if possible. The unix `md5sum` commandline tool can then be used to compute the checksum of the big archive file, and compare it to the expected values to ensure the contents are as expected.

To compute the sum of a single file:

```
md5sum file-to-check
```

and “by hand” verify the output of this application against the expected value. If the value is in a webpage, you can open the “find” dialog on that page and copy-and-paste the value output by the md5sum program. The “find” will match only if the values are the same.

If the software provider provides an md5sums file which has a list of (filename, checksum) pairs, then you can run:

```
md5sum -c md5sums-file
```

which will look in your local system for every file listed in the md5sums-file, compute its checksum, and report an error if it is not the expected value.

Some software providers *sign* archive files instead of (or as well as) providing an md5 checksum. In this case you should:

- download the provider’s *public key* from their website (using https where possible)
- download the “signature file” for the archive-file; this will be a small file which has the same base name as the downloaded file, with suffix “.sig” or “.asc”
- perform the following steps
```
# needed only once for each key, ie each "publisher"
gpg --import {public-key}

gpg --verify {signature-file-name}
```

The verify step decrypts the signature-file, revealing a checksum; it then runs a checksum algorithm over the real file and checks that they are the same. Obviously, the “gpg” application needs to be installed locally.

## Archive Files

An *archive* is a single file that contains within it a number of other files. There are several different tools for creating such files but in all cases the process is effectively to append all the individual files together, and then add an “index” which contains the offset, length, name and other properties of the original files so that they can be extracted again.

Note: the word “archive” in English can imply something “old”, no longer used, or a “backup” copy. While archive files can be used to store backups or rarely used data, they are also convenient for passing data around a network.

The “tar” application packs multiple files into a single “tar-format” archive file, which is the most commonly-used format in open-source. Tar originally meant “tape archive”, but the format works well on disks too. Tar archives remember not only the original names of files, but also their original Unix “owner id” and file-permissions. The ownerid is seldom useful (unless the tar archive was created on the same machine it is being unpacked on), but the file-permissions are. By convention, tar-format files are usually given names ending in “.tar”. Tar-format files containing sourcecode are sometimes referred to as “tarballs”.

The tar application doesn’t compress the file contents. However source-code files do compress very well and network bandwidth is always too slow and too expensive, so tar files are usually *compressed* after creation, using a generic compression application. The most commonly used compression type is “gzip”, and the resulting file is usually given the suffix “.tar.gz” or “.tgz”. Compression with bzip2 is also common, and such files are usually given the suffix “.tar.bz2”. Occasionally “xz” compression will be used; files are usually given the suffix “.tar.xz”.

The original version of the tar application was created a long time ago, and it has been reimplemented a number of times. Sadly this means that the commandline options available vary considerably depending on which version of the application you are using. The functionality also varies depending on version; in particular some versions can auto-detect when compression has been used, and decompress automatically while others require the correct commandline parameters to be passed in order to handle compressed files, and yet others require the file to be decompressed first. Here are some example commands to extract files from a tar archive:

```
# Modern GNU tar options. This works for files compressed
# with gzip and bzip too
tar --extract --file filename

# Same as above
tar -xf filename

# Same as above. Leading "-" is optional
tar xf filename

# explicitly decompress gzip2-compressed file then
# pass uncompressed result directly into tar
gzip -cd filename.tgz | tar xf -

# same as above, but for bzip2-compressed files
bunzip2 -cd filename.tar.bz2 | tar xf -
```

Note that if the “f” is omitted, tar just appears to hang (waits for user input).

Warning: unpacking a tar file can overwrite local files. By default, files are unpacked into a subdirectory of the current directory, which should be safe as long as your current directory is something appropriate. However *do not trust any instructions that use the following options*; they may be trying to trick you into modifying important local files:

```
-C or --directory
-P or --absolute-names
--transform or --xform
```

Most tar-files are created such that unpacking them creates a *single subdirectory* within the current directory, and all other files are placed into that directory; eg unpacking a file named “foo-1.2.tar.gz” would create a subdirectory named “foo-1.2” with files inside that directory. Sadly not everybody that packages source-code for download follows this convention; sometimes the tarfile expands its contents directly into the current directory - which can create a big mess if that directory already has files in it. It is therefore best to check the contents of the tarfile before unpacking, using the following command:

```
# Modern GNU tar
tar --list --file filename

# Same as above
tar -tf filename

# Same as above. Leading "-" is optional
tar tf filename

gzip -cd filename | tar tf -
bunzip -cd filename | tar tf -
```

Occasionally, archive files are distributed in “zip format”. Zip-archives are most common in the DOS and Windows world, or in relation to Java, but are occasionally encountered elsewhere. The Zip format works like a combination of tar and gzip (it uses the same compression as gzip but its own kind of internal “indexing”). Zipfiles do not preserve the original unix ownerid or file-permissions. The contents of such a file can be extracted with the `unzip` command (assuming it is installed locally).

Where possible, archive files should be unpacked when logged in as a normal user, not the “root” user. This is an extra safety-measure to prevent any unintended file overwrites. However the file-owner settings recorded in the tar-file are only preserved when the user unpacking the archive is the root user. If these are important (which is not common) then the archive must be unpacked “as root”.

## Common Files

Unpacked source-code archives usually contain a file named README or INSTALL (or both) in the top-level directory. You should always read these documents first, as they give important information about how to compile, install and configure the rest of the source-code in the downloaded archive.

One important piece of information usually found in the README or INSTALL is a list of other software that must be installed *first*. Any program you download will require some local header-files, library-files and/or helper tools to build or to run. If you don’t get the prerequisites right, then the application may not compile, or may compile but not run, or may get built without certain optional features that you want.

Another important piece of information is the set of parameters that can be passed to the build-process; see later.

## Patching

Sometimes the downloaded software is known not to work in your environment, and somebody has already figured out how to tweak it to resolve the problem. The person who solved the problem may publish their “tweak” in the form of a `sed` or `awk` command, a shell-script containing multiple sed/awk commands, or a *patch file*. Obviously, you need to be cautious about such changes, applying them only if you trust the source or have the ability to double-check the changes.

The `sed` tool applies a regular-expression to each line in a textfile, and replaces matching text with something else. It’s a fairly limited tool, but easy to use and widely available.

`awk` does something similar, but is capable of performing more complex transformations of textfiles.

A patchfile is created by using the “diff” tool to compare two versions of the same file and output the differences. The `patch` tool can take the output of “diff” and apply it to one of the files to “convert it” to the other version. The nice thing about patchfiles (ie the output of diff) is that it is quite human-readable, ie it is reasonably easy to see what changes will be made.

## Build Systems: configure, make, cmake, etc

The software developers who created the software you just downloaded obviously need some way of compiling and installing that software on their own machines. Whatever config files are necessary for the tools they use are almost always included in the archive file. As open-source developers *want* as many people as possible to use their software, they also go to some effort to make it easy to build and install the software on a range of different systems. However they cannot support every possible configuration in the world.

In the end, the point of the installation process is to convert the original source-code into a form that the local computer can execute, and then to place all the necessary files into directories that are listed in the `$PATH` variable for all users (so they can execute those files). Installing modules for interpreted languages is slightly different; those files get installed where the interpreter (eg python or perl) can find them, rather than in $PATH directly.

## Configure and Make

The most widely-spread tool used to manage the compilation and installation of source-code is `make`. Make is an application which takes a configuration-file (called a `makefile`) that contains a list of rules, most of the form:

- when TARGET-FILE is older than SOURCE-FILE then SOME-ACTION

The target-file is the end product, or some intermediate stage. The source-file is the hand-written source-code, or some intermediate artifact. The action is usually the invocation of a compiler, a linker, or similar which recreates the target-file. This document is far too short to go into the details of the very complex and powerful `make` command; fortunately it is usually not necessary to understand makefiles to compile software - unless something goes wrong. A brief explanation of makefile syntax and behavior can be found in Appendix A.

Unfortunately, although makefile syntax is very powerful it still isn’t enough to be able to handle all the possible ways that computers can be configured, and all the possible ways that the person installing the software may wish to compile the application. Many software packages therefore come with a shell-script named “configure” and a template makefile named “Makefile.in”. The configure script takes a list of configuration options on the commandline, and converts the template makefile into a real makefile customised for the local computer and the installer’s wishes. The installation sequence therefore usually goes like:

```
# unpack and read documentation
tar xf filename
cd {directory created by above step}
less README
less INSTALL

# generate customised makefile
./configure {some options ...}

# compile everything in the local directory
make

# update global directories
sudo make install
```

By the way: the “configure” script is auto-generated by software called [autotools](https://en.wikipedia.org/wiki/GNU_build_system), but that doesn’t matter to the person using it.

Configure is usually invoked as “./configure” to avoid two possible problems:

- Some users don’t have “.” in their `$PATH` variable. In particular, the root user doesn’t have this for security reasons
- Some users don’t have “.” as the *first* entry in their `$PATH`, meaning that the *wrong* configuration script might get run

In general, it is best to perform all steps except “make install” as a normal system user, not root. This avoids mistakes and possibly some attacks (though as the install step is done as root that isn’t much protection). However installing software into the global `/bin` or `/usr` directories usually requires administration privileges (unless you’re using a “user-based package manager” or similar rare setup).

Some projects provide a makefile but no “configure” script; in this case the “configure” step above can be omitted. Either the application is simple enough that it doesn’t need it, or the software developers have built more logic into the hand-written makefile.

Not all systems are set up with “sudo” enabled. In this case, use the following instead:

```
su  # must then enter root password
make install
exit
```

For most software, the configure and make commands can be run in the same directory as the source-code, as shown above. The result is that new files generated during compilation get mixed together with the originals, which is somewhat messy, but the “make clean” command can be used to tidy that up later. However for some software it is necessary to create a temporary directory, change the current-working-directory to that directory and then perform the build-steps there; the project documentation should indicate if this is necessary. Some people consider it better to *always* build from a temporary directory. An example of building using a separate directory next to the original source-code, which is a common convention:

```
# unpack into a directory {packagename}
tar xf filename

# create separate build directory
mkdir {packagename}-build

# compile everything in the separate build directory
cd {packagename}-build
../{packagename}/configure {some options}
make

# update global directories
sudo make install
```

## Other Build Tools

Some projects use `cmake` as their build-tool. `cmake` works somewhat like `configure` (see above); it *generates* a makefile whose content depends on the options passed to the cmake command, and on features of the local system. The steps require to build a cmake-based package are identical to the “configure/make” examples above except that the configure step is replaced by:

```
cmake . -DCMAKE_BUILD_TYPE=Release {some options ...}
```

As always, check the project’s documentation for instructions on how to build.

Some projects use build-tools based on python or perl rather than `make`. The principles are still fairly similar.

Software which does not need to be compiled usually has a fairly simple and quick installation process. In particular, applications written in the Perl or Python interpreted languages can be installed just by copying the files into the relevant location. These projects nevertheless include a program or script in the archive-file which performs this task, rather that requiring the installer to do this manually.

## Environment Variables

Options to configure the compilation and installation of an application are usually passed as command-line parameters to the “configure” script or the make program. However sometimes configuration options are passed via `environment variables` instead. These can be specified by placing the definitions on the start of the command, eg

```
NAME=tom ENABLE_FOO=no ./configure
```

Environment variables can also be defined *before* running the command:

```
export NAME=tom
export ENABLE_FOO=no
./configure
```

Which options are available is normally described in the archive’s README or INSTALL files, or on the project website. Sometimes the available options can be seen by running `./configure --help`.

## Building and Installing Documentation

Some projects provide documentation which can be “installed” so it is accessible via normal system documentation viewers such as “man” or “info”. Some provide documentation in HTML form, which usually gets installed under `/usr/share/doc`. Sometimes this documentation is included in the “standard” archive file, and sometimes it is a separate (optional) download. Sometimes the documentation is installed as part of the standard `make install` command, and sometimes a separate command must be used if you wish to install it. And sometimes the documentation is delivered in a “ready-to-use” form, but sometimes it is delivered in a kind of “raw form” that must be processed before being installed - rather like source-code needs to be compiled.

As should be clear by now, the variety of approaches to delivering documentation is so wide that no really useful advice can be given here. See the README and INSTALL files in the downloaded archive, and the project website for guidance.

## Other Build Targets

As well as commands to compile everything (“make”) and to install the previously-compiled programs (“make install”) or documentation, there are a few other common possibilities.

`make clean` usually removes all generated files, ie leaves the directory as it was after the files were unpacked from the archive.

## Invoking a Compiler

As noted above, the most common step performed by “make” or “cmake” is to invoke a compiler. The local system must have the appropriate compilers installed.

This is also the step that is most likely to fail (along with linking).

If the compilation step fails with an error-message about not being able to find a header-file, or not being able to find a library-file then you have probably not installed all of the pre-requisites - reread the README and INSTALL files. In some cases, the missing prerequisite is optional, in which case there will be a parameter that can be passed to configure or an environment variable which can be set to allow the software to be installed without that prerequisite. Double-check the parameters you specified, and if they seem correct then the project documentation is the best resource for resolving such issues.

Compilers have a range of options that can potentially improve performance. However you should only mess with these if you have plenty of experience. If you need this document, then just leave compiler options at their defaults!

The output of compiling and linking (the “executables” that you actually wanted) usually have significant amounts of data in them which are useful for debugging the programs, but not useful for “normal end users”. This information can be removed from the executable files by running `strip {filename}` on them. Smaller programs will save disk-space and also load slightly faster. Unless you are intending to debug the program, using `strip` is a good idea.

## Post-Installation Configuration

Some applications can have their behaviour customised via *configuration files* read on application startup. Often applications install *default versions* of the configuration files somewhere under `/usr` or in the `/etc` directory. Inspect the output of the `make install` command to see which config files have been installed. Configuration options should also be documented in the program’s README or INSTALL, or on their website.

## Appendix A: An example Makefile

Unfortunately, when building packages from source-code, it is not unusual for compilation errors to occur. It is sometimes possible to diagnose and fix the problem by inspecting the makefile, ie a basic understanding of makefile syntax can be helpful. Here is a very brief example of the basic syntax and functionality; for further details see the `make` documentation or one of the many tutorials available online.

A sample makefile to build an executable called ‘prog’ which has one ‘c’ sourcefile, one headerfile and uses one library (which it also builds from a single ‘c’ sourcefile) could look like the following:

```
prog: prog.c prog.h libmylib.a
  gcc -o prog prog.c -L. -lmylib

libmylib.a: libmylib.o
  ar -rcs libmylib.a libmylib.o

libmylib.o: libmylib.c libmylib.h
  gcc -c -o libmylib.o libmylib.c
```

The form of the entries (rules) are:

```
target: dependency1 [dependency-n ...]
<tab> command to execute
...
```

For each “rule”, if the target is missing or older than any of the dependencies (based on file timestamps), then the command(s) are run to (re)create the target. However each dependency is first tested to see if there is a rule that has *it* as a target. If so, that target is recursively evaluated, ie (re-)built if it is missing or older than its dependencies.

Thus in the above set of rules, a change in libmylib.c will cause libmylib.o to be rebuilt. Then libmylib.a is regenerated and finally prog is rebuilt.

Makefiles can get very complicated and many are automatically generated, but the above principles always apply.

# "信息矿井 - 从源代码安装入门指南"

## 引言

此文档旨在为希望直接从原始作者安装软件的开源操作系统用户服务，而不是完全依赖操作系统提供的软件（及其版本）。本文档是为那些不熟悉从源代码下载软件概念的用户而写的，提供了相关命令和一般流程的背景信息。

## 所讨论的概念

- 开发和分发：独立软件开发者，多系统支持，发布（存档），版本控制
- 分发：二进制软件包和包管理器
- 下载：http，ftp，wget，校验和与签名
- 存档文件：tar，zip，gzip
- 常见文件：README 和 INSTALL；必需的依赖项
- 使用 sed/awk/patch 进行修补
- 构建和安装：configure，make，cmake，perl 和 python
- 构建和安装文档
- 编译器设置和剥离；处理错误
- 安装后配置

## 开发和分发

一个典型的操作系统由数百个不同的应用程序组成。在专有操作系统中，该操作系统生产商/销售商通常拥有并管理所有这些软件的开发。然而开源系统通常是通过获取并整合由多个独立团体或甚至单个独立开发者发明并维护的应用程序创建的。此外，开源软件项目往往在各种不同的操作系统中使用，例如基于 Linux，基于 BSD，Hurd，有时甚至被整合到 Solaris，Mac，甚至 Windows（如果许可允许）等专有操作系统中。

原始软件开发者有时被称为分发维护者或最终用户所称的 *上游源码* 或简称 *上游*。同样，开发者通常将使用其代码的分发或最终用户称作 *下游*。

大多数开源项目将他们的源代码存储在一个版本控制系统中，该系统可通过互联网访问（匿名用户只读）。对于这些项目，可以下载最新“进行中”的文件，或者下载带有某个发布ID（版本号）标记的文件集合。然而，这样做通常并不高效；项目通常会通过创建包含所有相关文件的 *存档文件*，定期进行 *发布*，使其可供下载。对于没有公共版本控制系统的项目，定期发布的 *存档文件* 是唯一的方式直接获取源代码。

## 下载与安全性

通常通过网络浏览器下载存档文件，点击“下载”按钮、点击链接或者右键链接然后选择“另存为”。这使用了“http”协议来下载文件。有些网站则通过较旧的“ftp”协议来分发文件。许多网络浏览器也支持这个协议，即点击链接同样可以工作。或者可以使用 ftp 客户端应用程序。

wget 或 curl 命令行工具还可以用于 Unix-like 操作系统，通过 http 或 ftp 协议下载文件，前提是知道正确的 URL。

那些经常下载软件的网站通常会设置“镜像”站点，即有人将文件复制到世界各地的不同位置，以便帮助下载。原始网站通常会提供可用的 *镜像列表*，你应该选择离你近的镜像站点。这样有助于减少原始发布者网络带宽的成本，通常也能让你下载得更快。

http 和 ftp 网络协议都可以被罪犯或其他不良分子拦截，然后修改下载过程中数据。数据也可能会在下载过程中因意外而被损坏（虽然这种情况并不常见）。如果你使用的是镜像站点，有可能有人修改了该站点上的文件（即该站点上的文件与原始发布者提供的不一致）。因此，最好 *验证* 你下载的文件是否是原始发布者想要发布的。

许多网站会为每个存档文件提供一个 md5sum 文件，其中包含该文件内容的 *校验和*；有时一个单独的 md5sums 文件会包含多个其他文件的校验和。你应该从 *原始站点* 获取 md5sums 文件，而不是镜像站点。你尽可能使用安全的 https 协议下载它。使用 unix 命令行工具 md5sum 可以计算大存档文件的校验和，并对比预期值以确保内容如预期。

计算单个文件的校验和：

```
md5sum file-to-check
```

然后通过“手动”方式将该命令的输出结果与预期值进行对比，以验证文件的正确性。如果校验和值是在网页上提供，你可以在该网页上打开“查找”对话框，将 md5sum 程序输出的值复制粘贴到其中即可。查找只有在校验和值相同时才会匹配。

如果软件提供者提供了包含（文件名，校验和）列表的 md5sums 文件，那么你可以运行：

```
md5sum -c md5sums-file
```

这会在你本地系统中寻找 md5sums 文件中列出的所有文件，计算其校验和，并在与预期值不一致时报告错误。

有些软件提供者会 *签名* 存档文件而不是（或同时）提供 md5 校验和。在这种情况下你应该：

- 从提供者的网站上下载 *公钥*（使用 https 尽可能）
- 下载存档文件的“签名文件”；这个文件与下载下来的文件有相同的基本名称，后缀是 .sig 或 .asc 
- 执行以下步骤
```
# 每个公钥只一次需要，即每个 "发布者"
gpg --import {public-key}

gpg --verify {signature-file-name}
```

验证步骤会解密签名文件，显示一个校验和；它会在真实文件上运行校验和算法，并检查它们是否相同。显然，“gpg”应用程序需要在本地安装。

## 存档文件

一个 *存档* 是一个包含多个其他文件的单个文件。虽然有多种创建这样的文件的工具，但其过程基本上是将所有个别文件合并在一起，然后添加一个“索引”文件，包含原始文件的偏移量、长度、名称和其他属性，以便将来提取这些文件。

注意：“archive”这个词在英语中有时暗示着“过时的”东西或者“备份”副本。虽然存档文件可用于存储备份或很少使用的数据，但它们也非常便于在局域网上传输数据。

“tar”应用程序将多个文件打包成一个称为 "tar 文件格式" 的存档文件，这是开源项目中最常用的一种格式。Tar 最初意思是“磁带存档”，但此格式在磁盘上也运行良好。Tar 存档包含原始文件的名字，以及它们的 Unix “拥有者 ID”和文件权限。拥有者 ID 在大多数情况下用处不大（除非该 tar 存档文件是打包在同一台机器上），但文件权限是有用的。按照惯例，tar 文件格式的文件通常以 .tar 结尾。包含源代码的 tar 文件有时被称为“tarballs”。

Tar 应用程序本身并不压缩文件内容。然而，源代码压缩效果非常好，而网络带宽总是很慢并且成本很高，因此大多数 tar 文件在创建之后会使用一种通用压缩程序进行压缩。最常见的压缩类型是“gzip”，产生的文件通常以 .tar.gz 或 .tgz 为后缀。使用 bzip2 压缩也很常见，这类文件通常以 .tar.bz2 为后缀。偶尔使用 xz 压缩，这类文件通常以 .tar.xz 为后缀。

最初的 tar 应用程序是在很久之前创建的，后又被多次重新实现。不幸的是，这意味着不同版本的 tar 应用程序可用的命令行参数可能会有很大差异。此外，功能也因版本不同有所变化；有些版本可以自动检测是否使用了压缩，并在解压时自动处理，而其他版本则需要传递正确的命令行参数来处理压缩文件，还有的版本则要求先解压文件。以下是一些从 tar 存档中提取文件的示例命令：

```
# 现代 GNU tar 选项。这可以用于以 gzip 和 bzip 压缩的文件
tar --extract --file filename

# 同上
tar -xf filename

# 同上。前面的“-”是可选的
tar xf filename

# 显式解压 gzip 压缩的文件，然后将解压后的内容直接直接传递给 tar
gzip -cd filename.tgz | tar xf -

# 与上文相同，但用于 bzip 压缩的文件
bunzip2 -cd filename.tar.bz2 | tar xf -
```

需要注意的是，如果没有“f”选项，tar 似乎会挂起（等待用户输入）。

警告：解压 tar 文件可能会覆盖本地文件。默认情况下，文件会解压到当前目录的一个子目录中，只要当前目录适合作为工作目录，这一般是安全的。然而，请*不要信任使用以下选项的任何指令*；它们可能试图诱使你修改重要文件：

```
-C 或 --directory 
-P 或 --absolute-names
--transform 或 --xform
```

大多数 tar 文件被创建成解压后在当前目录中创建一个 *单个子目录*，并将所有其他文件放入该目录。例如，解压名为“foo-1.2.tar.gz”文件将创建一个名为“foo-1.2”的子目录，并包含该目录中的文件。但不幸的是，并不是每个人在为下载的源代码打包时都遵守这个惯例；有时候 tar 文件直接将内容展开到当前目录，这样如果当前目录已经有文件时就会导致严重的混乱。因此，最好在解压之前检查 tar 文件的内容，使用以下命令：

```
# 现代 GNU tar
tar --list --file filename

# 同上
tar -tf filename

# 同上。前面的“-”是可选的
tar tf filename

gzip -cd filename | tar tf -
bunzip -cd filename | tar tf -
```

偶尔，存档文件会以“zip 格式”分发。Zip 存档在 DOS 和 Windows 世界中最为常见，或是与 Java 相关，但有时也会出现在其他地方。Zip 格式类似于 tar 和 gzip 的结合（它使用与 gzip 相同的压缩方式，但拥有其自己的“索引”机制）。Zip 文件不保留原始 Unix 文件拥有者 ID 和文件权限。使用 unzip 命令（假设已安装）可以提取这种格式的文件内容。

尽可能在普通用户身份下解压存档文件，而不是 root 用户。这样能提供额外的安全措施，以防出现未预期的文件覆盖。然而，tar 文件中记录的文件拥有者设置只在使用 root 用户解压存档文件时保留。如果这些设置很重要（这并不常见），则必须“以 root 身份”解压存档文件。

有些项目会在存档文件中提供 makefile，但并不包含 “configure” 脚本。在这种情况下，可以跳过上面的 configure 步骤。要么应用程序足够简单，不需要 configure，要么软件开发者已经在手写的 makefile 中添加了更多的逻辑。

并不是所有系统都启用了 sudo 命令。在这种情况下，可以使用以下命令：

```
su # 这时必须输入 root 密码
make install
exit
```

大多数软件的 configure 和 make 命令都可以在源代码所在的目录中运行，如上述所示。结果是，编译过程中生成的新文件会与原始文件混合在一起，这有点混乱，但你可以使用“make clean”命令在之后清理一次。然而，一些软件可能需要创建一个临时目录，将当前工作目录更改为该目录，并在该目录中进行构建步骤；文档应该会说明是否需要这样。有些人认为 *始终* 在临时目录中进行构建更好。下面是一个使用与源代码文件相邻的单独目录构建的示例，这在许多项目中是常见的做法：

```
# 解压到 {packagename} 目录
tar xf filename

# 创建单独的构建目录
mkdir {packagename}-build

# 在单独的构建目录中编译所有内容
cd {packagename}-build
../{packagename}/configure {一些选项...}
make

# 更新全局目录
sudo make install
```

## 其他构建工具

一些项目使用 cmake 作为构建工具。cmake 的运作方式与 configure（见上文）相当类似；它 *生成* 一个 makefile，其内容取决于 cmake 命令传入的选项和本地系统的特征。构建基于 cmake 的项目的步骤与上面的 configure/make 示例相同，只是 configure 的步骤被替换为：

```
cmake . -DCMAKE_BUILD_TYPE=Release {一些选项...}
```

始终检查项目的文档以获取具体构建说明。

一些项目使用基于 python 或 perl 的构建工具而不是 make。其基本原则仍然非常相似。

不需要编译的软件通常有相当简单和快速的安装过程。特别是用 Perl 或 Python 解释语言编写的程序，只需要将文件复制到相关位置即可安装。这些项目仍然包含一个程序或脚本在存档文件中，为安装执行该任务，而不是需要用户手动完成。

## 环境变量

配置编译和安装应用程序的选项通常作为命令行参数传递给 configure 脚本或 make 程序。然而有时配置选项是通过 *环境变量* 传递的。这些变量可以被指定在命令的开始处，例如：

```
NAME=tom ENABLE_FOO=no ./configure
```

环境变量也可以在运行命令之前定义：

```
export NAME=tom
export ENABLE_FOO=no
./configure
```

哪些选项可用通常在下载的存档的 README 或 INSTALL 文件中描述，或者在项目网站上。有时通过运行 `./configure --help` 可以看到可用选项。

## 构建和安装文档

一些项目提供文档，可以在安装后通过正常的系统文档查看器（例如“man”或“info”）访问。一些项目提供 HTML 格式的文档，通常将这种文档安装在 `/usr/share/doc` 目录下。有时候该文档会在“标准”的存档文件中，有时则会作为单独的（可选）下载。有时候文档会作为标准 `make install` 命令的一部分进行安装，有时候则需要使用单独命令才能安装。有时候文档会以“现成”格式提供，但有时候则会以“原始格式”提供，需要进行一些处理后才能够安装，这与源代码需要编译类似。

综上所述，提供文档的多种方法足以让文档变得复杂，以至于无法在此处给出有用建议。请查看下载存档中的 README 和 INSTALL 文件，以及该项目的网站以获取指导。

## 其他构建目标

除了“编译所有内容”的命令（make）和“安装先前编译好的程序”（make install）或文档之外，还有一些其他常见的可能性。

make clean 通常会删除所有生成文件，即，它会将目录恢复到从存档中解压后的状态。

## 调用编译器

如上所述，make 或 cmake 执行的最常见步骤就是调用编译器。本地系统必须已安装相应的编译器。

这也是最容易出错的步骤（与链接有关）。

如果编译过程中出现错误信息，告诉你找不到头文件或库文件，那么你可能没有安装所有预置的依赖项，请重新阅读 README 和 INSTALL 文件。有些情况下，缺失的预置项是可选的，这时会有参数可以通过 configure 传递，或者设置环境变量，以允许软件在缺少该预置条件下被安装。仔细检查你输入的参数，如果它们看起来正确，则项目文档是解决此类问题的最佳资源。

编译器有许多选项，可以潜在地提高性能。但只有你有一定的经验才可对其进行调整。如果你需要这份文档，那么就保留编译器选项的默认值！

编译和链接的输出（也就是你实际想要的“可执行文件”）通常包含大量对调试程序有用但对普通终端用户不重要的信息。这些信息可通过 `strip {filename}` 命令从可执行文件中删除。体积较小的程序会节省磁盘空间，并且加载稍快。除非你打算调试程序，否则使用 strip 是一个好主意。

## 安装后配置

一些应用程序可以通过在应用程序启动时读取的 *配置文件* 来自定义其行为。经常，应用程序在 `/usr` 或 `/etc` 目录下安装配置文件的 *默认版本*。查看 `make install` 命令的输出以找到安装了哪些配置文件。配置选项也应在程序的 README 或 INSTALL 文件或网站中予以说明。

## 附录 A：一个示例 Makefile

不幸的是，从源代码构建包时，编译错误并不罕见。有时可以通过检查 makefile 来诊断并修复问题，即对 makefile 的基本语法有所了解可能有助于问题解决。以下是关于 makefile 基本语法和功能的简短示例，如需更多详情，请查看 make 的文档或在线提供的许多教程。

一个用于构建名为“prog”的可执行文件的 makefile 示例，它包含一个“c”源文件、一个头文件，并使用一个库（该库也是由一个“c”源文件构建）如下所示：

```
prog: prog.c prog.h libmylib.a
  gcc -o prog prog.c -L. -lmylib

libmylib.a: libmylib.o
  ar -rcs libmylib.a libmylib.o

libmylib.o: libmylib.c libmylib.h
  gcc -c -o libmylib.o libmylib.c
```

这些规则项的格式是：

```
目标: 依赖项1 [依赖项-n ...]
<tab> 要执行的命令
...
```

对于每一项“规则”，如果目标文件缺失或比任一依赖项旧（基于文件时间戳判断），则执行相应的命令（re）创建目标。然而，每个依赖项首先会被检查，是否有一个规则将它作为目标。如果存在该规则，则目标会被递归评估，即，如果它缺失或比其依赖旧则重新构建。

因此，在上述规则中，对 libmylib.c 的更改会导致 libmylib.o 重新生成。之后 libmylib.a 也被重新生成，并且最终 prog 也被重新生成。

makefile 可能相当复杂，而且大多数都会被自动创建，但上述原则总是适用。