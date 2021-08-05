---
title: Anaconda+VSCode+PyTorch的配置
date: 2020-12-06T16:00:00Z
tags:
- 配置环境
categories: ''

---
> 记录一次Anaconda+VSCode+PyTorch的配置
>
> 操作系统为macOS Catalina 10.15.7
>
> 更新：
>
> > 由于需要GPU，在下载Anaconda和PyTorch中，也会附带说明了在windows10的操作。
> >
> > 其他地方都是按照MacOS来记录的。

### 下载Anaconda

#### 先去官网或者清华镜像站下载Anaconda或者Miniconda

##### 官网链接

[https://www.anaconda.com/products/individual#Downloads](https://www.anaconda.com/products/individual#Downloads "https://www.anaconda.com/products/individual#Downloads")

有win、mac、linux的版本选择，操作系统是什么就选择对应的就好啦\~

**例如 Windows10：**

我直接在官网上下载的，也可以去清华镜像站找对应的版本。

**注意：**在安装过程中，有一步需要将**“添加到PATH”勾选上！**

一定要勾选上（尽管勾选之后说明文字会标红），不然后面配置稍有些麻烦。

##### 通过镜像下载

如果在官网的下载速度较慢，可以去清华镜像站下载。下面以anaconda的安装为例。（安装miniconda也是同样操作）
[https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/)￼
找自己对应的操作系统，和需要的版本，进行下载。

**例如 Mac OS：** 我用到的是Anaconda3-2020.11-MacOSX-x86_64.pkg，[https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-2020.11-MacOSX-x86_64.pkg](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-2020.11-MacOSX-x86_64.pkg)￼（点击即可下载）。也推荐下载miniconda。

macOS上安装没有什么需要注意的地方。下载好之后，双击进行安装，一直点“继续”，直到完成。

#### 检测是否安装好

输入`conda -V`，查看版本号，出现conda版本号就是安装好了。

#### 打开终端的有(base)字样

> macOS里遇到的问题

新版的Anaconda会在每一个新开的terminal里面自动进入虚拟环境base。看着有些强迫症犯了。
使用如下命令就能把默认进入base虚拟环境关掉了。
    ```
    conda config --set auto_activate_base false
    ```

另外，如果需要进入base虚拟环境

    ```
    conda activate
    ```

退出当前虚拟环境

    ```
     conda deactivate
    ```

### 为PyTorch新建虚拟环境

1. 新建虚拟环境，命名为pytorch，指定python版本为3.6。

	```
    conda create --name pytorch python=3.6
    ```

2. 安装好之后，进入pytorch虚拟环境。

	```
    conda activate pytorch
    ```

### 安装PyTorch

在pytorch虚拟环境中安装PyTorch。要注意，先进入pytorch虚拟环境。
附上官网的链接：

[https://pytorch.org](https://pytorch.org)￼

根据官网，安装PyTorch。

#### MacOS

    ```
    conda install pytorch torchvision torchaudio -c pytorch
    ```

#### Win

> 要选择对应的cuda版本的，如果没有GPU就要选择None

    ```
    conda install pytorch torchvision torchaudio cudatoolkit=11.0 -c pytorch
    ```
    
### VSCode配置python

参考官方链接
[https://code.visualstudio.com/docs/python/python-tutorial#_prerequisites](https://code.visualstudio.com/docs/python/python-tutorial#_prerequisites)￼

#### 下载安装VSCode

官网下载，附上链接
[https://code.visualstudio.com](https://code.visualstudio.com)￼

#### 下载python插件

在VSCode的应用商店中搜索Python，并下载
![vscode下载python插件](https://x.arcto.xyz/16oo5U/vscode下载python插件.jpg)￼

#### 下载Python解释器

这里的python解释器使用**pytorch虚拟环境**中的，见上文中“为PyTorch新建虚拟环境”。

验证python是否安装

    python3 --version

由于我使用pytorch环境下的python，需要先进入该环境。

    conda activate pytorch

![验证python的安装](https://x.arcto.xyz/RK3zzQ/验证python的安装.jpg)￼

> 后来换了一台电脑下载的是miniconda，无所谓啦\~

#### 设置python解释器

使用快捷键shift + command + P ，或者点击**菜单栏**中的**查看**里的**命令面板**， 打开命令面板。
输入`python:select interpreter`  选择python解释器。这里选择我要用的pytorch里的python3.6.2就好了。
![vscode设置python解释器](https://x.arcto.xyz/T-xnZd/vscode设置python解释器.jpg)￼

Note：代码补全要用IntelliCode，不要用Pylance（要卸掉）

### VSCode代码自动补全

下载插件：Visual Studio IntelliCode
注意：VS Code会在右下角推荐Pylance，不要下载

在全局的setting.json中，找到以下有关editor.suggest设置为false的代码删掉

      "editor.acceptSuggestionOnCommitCharacter": false,
        "editor.suggest.showClasses": false,
        "editor.suggest.showConstructors": false,
        "editor.suggest.showEvents": false,
        "editor.suggest.showFields": false,
        "editor.suggest.showFunctions": false,
        "editor.suggest.showVariables": false,
        "editor.suggest.showValues": false,
        "editor.suggest.showUnits": false,
        "editor.suggest.showTypeParameters": false,
        "editor.suggest.showSnippets": false,
        "editor.suggest.showReferences": false,
        "editor.suggest.showOperators": false,
        "editor.suggest.showModules": false,
        "editor.suggest.showProperties": false,
        "editor.suggest.showIssues": false,
        "editor.suggest.showMethods": false,
        "editor.suggest.showIcons": false,
        "editor.suggest.showInterfaces": false,
        "editor.suggest.showFolders": false,
        "editor.suggest.showFiles": false,
        "editor.suggest.snippetsPreventQuickSuggestions": false,
        "editor.suggest.showWords": false,
        "editor.suggest.showKeywords": false,
        "editor.suggest.showEnums": false,
        "editor.suggest.showEnumMembers": false,
        "editor.suggest.showConstants": false,
        "editor.suggest.filterGraceful": false,
        "editor.suggest.showStructs": false,
        "editor.suggest.showUsers": false,

将下面这行的null改为true，即将

    "editor.quickSuggestions": null,

改为

    "editor.quickSuggestions": true,

##### 如何打开全局的setting.json

点击 code => 首选项 => 设置，随便找一项设置，点击**在setting.json中编辑**，（再删掉新加的代码就好了），不知道有什么其他的好方法。

### 其他

当然啦，PyCharm也是很好的选择\~也推荐\~

### 用conda安装opencv

    ```
    conda install opencv3 -c anaconda
    ```
    
c是channel的意思，要指定anaconda或者menpo做channel才行。