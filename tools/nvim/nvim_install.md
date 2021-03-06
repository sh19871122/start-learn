# nvim
```
另一个vim
```

## nvim install
```
neovim安装完成后，需执行
    > pip install neovim
```
### CentOS 7/RHEL 7
```sh
yum -y install epel-release
curl -o /etc/yum.repos.d/dperson-neovim-epel-7.repo https://copr.fedorainfracloud.org/coprs/dperson/neovim/repo/epel-7/dperson-neovim-epel-7.repo 
yum -y install neovim

```

### Ubuntu
```
依赖项安装：
> sudo apt-get install python-dev python-pip python3-dev python3-pip

14.04及其以上
> sudo apt-get install software-properties-common
更老版本的
> sudo apt-get install python-software-properties

安装
> sudo add-apt-repository ppa:neovim-ppa/stable
> sudo apt-get update
> sudo apt-get install neovim

"永久性工事"
sudo update-alternatives --install /usr/bin/vi vi /usr/bin/nvim 60
sudo update-alternatives --config vi
sudo update-alternatives --install /usr/bin/vim vim /usr/bin/nvim 60
sudo update-alternatives --config vim
sudo update-alternatives --install /usr/bin/editor editor /usr/bin/nvim 60
sudo update-alternatives --config editor
```

### Ubuntu Raspberry(jessie)
```
源码编译
1. 安装依赖
 apt-get install git
 apt-get install libtool libtool-bin autoconf automake cmake g++ pkg-config unzip python-dev
 git clone https://github.com/neovim/neovim.git
 cd neovim
 make CMAKE_BUILD_TYPE=RelWithDebInfo
 make install
2. 安装插件过程参见上面
3. 编译YouCompleteMe可能失败（32位只有失败）
 a. 从源中安装clang，apt-cache search clang
 b. apt-get install clang-3.9
 c. 手动编译，使用系统的clang(swap默认的100M会导致资源不足)
   > ./install.py --clang-completer --system-libclang --gocode-completer 
4. 其他参见其他章节
```

##配置nvim插件（C/C++/Python/Go程序猿版）
```
所有插件通过vim-plug插件来管理

安装vim-plug插件
> curl -fLo ~/.local/share/nvim/site/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
若用于vim执行下面的命令
> curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
>     https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

### 个人插件
```
参见本目录init.vim
```

### 安装插件
```
打开nvim并执行
:PlugInstall
```
#### YouCompleteMe说明
```
若中途失败，则需要到plugged/YouCompleteMe目录下执行依赖项的仓库重新拉取并编译
> git submodule update --init --recursive
编译，我这儿只要C家族的和Go语言
> ./install.py --clang-completer --gocode-completer
" 若编译后出现ycm_core.so找不到libtinfo.so，可以使用
> LLVM_OPTS="$LLVM_OPTS --disable-terminfo"
“ 不过我系统上并为成功，应该是编译使用clang的时候并未应用此选项的原因
” 可以使用系统的clang做编译，ycm_core.so对libtinfo的引用将会正确
> ./install.py --system-libclang --clang-completer --gocode-completer
```
#### vim-go说明 
```
自己设置好go的环境变量后，需要在nvim中执行
:GoInstallBinaries
需要注意的是国内网络环境“复杂多变”，需要将环境变量设置好
> export https_proxy=http://192.168.1.1:8118/
> export http_proxy=http://192.168.1.1:8118/

```

#### clang-format
```
# 省略安装clang,clang-format
# 下载clang-format.py
> curl -fLo $HOME/bin/clang-format.py --create-dirs https://llvm.org/svn/llvm-project/cfe/trunk/tools/clang-format/clang-format.py
> chmod +x $HOME/bin/clang-format.py
# 在所在项目目录下创建fmt格式风格
> clang-format -style=llvm -dump-config > .clang-format
# 在.vimrc或init.vim下添加映射
map <C-K> :pyf $HOME/bin/clang-format.py<cr>
imap <C-K> <c-o>:pyf $HOME/bin/clang-format.py<cr>

# 当文件保存时自动化格式化
function! Formatonsave()
  let l:formatdiff = 1
  pyf ~/llvm/tools/clang/tools/clang-format/clang-format.py
endfunction
autocmd BufWritePre *.h,*.cc,*.cpp,*.hpp,*.c call Formatonsave()
```

## reference
    [neovim install wiki](https://github.com/neovim/neovim/wiki/Installing-Neovim)
    [vim-plug](https://github.com/junegunn/vim-plug)
