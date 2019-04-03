# virtualenv

## 什么是virtualenv？

python程序要实现有用的功能，往往会用到各种标准库或者第三方库的包。我们把这种关系称为依赖，而如果我们用到的包有调用到了其他的包，那么一个程序员需要提供的信息就太多了，在用一个包的时候还要知道这个包的完整依赖，未免也太麻烦了。但是人生苦短，我用python的口号也不是吹出来的，python提供了一个管理依赖的工具pip。

举个简单的例子，假设你在python街开了一个电子产品的维修店，但是这个街规定，你如果想用一把螺丝刀，你需要给管理员说清楚你要螺丝刀。这还不够，python街管理员还要问清楚，这把螺丝刀需要什么原料生产，这些原料又要怎么制作。把所有这些东西问清楚之后，python街的管理员才会给你一把螺丝刀。那么问题来了，如果有一天，你想在python街用电脑办点事情，你得知道多少东西？

这时候正在你要抓狂的时候，你隔壁开水果店的老板告诉你，不用那么麻烦，我认识一个人，专门管理这些工具的，你要啥，给他说一声，他直接给你准备好了你直接用就行。这就大大的简化了项目的依赖关系，每个项目只需要在项目中准备好它的直接依赖就可以了，其他的事情pip可以帮你做好。

但是用了一段时间，你又发现一个问题。你在修电脑的时候要用梅花口的螺丝刀，在修手机的时候又需要用十字口的螺丝刀。可是偏偏python解释器只认螺丝刀的名字，并不认它是什么版本的。所以你每次修电脑的时候，就需要先把十字口的螺丝刀扔掉，然后再让pip给你准备梅花口的螺丝刀，修手机的时候也是这么麻烦。不过还好，你又想出来一个办法，找管理员多租一个门面不就好了，一个房间用来修手机，一个房间用来修电脑，不同房间里面放不同的工具，就算是两个房间里面要用不同型号的螺丝刀，也不用那么麻烦了，换螺丝刀麻烦，换房间总简单一点吧。

这里面的不同的房间在python里面就叫做virtualenv（虚拟环境）。不同的virtualenv在不同的文件夹下，设置某个virtualenv之后，pip就会将对第三方包安装在对应的目录下。python的解释器也会存放在这个virtualenv的目录下，这样的话，对于python程序来说，它的解释运行环境就被隔离开了，如果需要使用某个包，他会在自己的虚拟环境中去找到对应的包。这样不同运行环境就不会互相影响，造成一些问题。

## virtualenv的用法

### 使用virtualenv命令

* 安装virtualenv
```bash
$ pip install virtualenv
```
* 使用virtualenv
```bash
$ mkdir project_dir
$ cd project_dir
$ virtualenv -p python3 venv
$ source venv/bin/activate
```

virtualenv命令会在当前文件夹下创建一个新的文件夹，文件夹名由用户指定。在这个文件夹中会安装一些python运行需要的最基础的环境，并且会在bin目录下准备一个shell脚本作为virtualenv的入口，在要进入virtualenv的时候需要使用source命令执行这个activate文件，把一些系统环境变量修改成virtualenv的环境变量。在这个文件里面还做了一个shell function用于退出这个virtualenv，所以如果想要退出直接在命令行执行deactivate就好。但是也是因为deactivate是在activate文件中定义的，所以只有在一个virtualenv中执行才有效。

virtualenv有几个常用的参数

* -p PYTHON_EXE, --python=PYTHON_EXE
 指定virtualenv中使用的python版本，建议每次创建virtualenv的时候都显式设置一下用的是那个版本的python解释器

* --no-site-packages
这个option现在已经被废弃了，在老版本的pip中还需要使用。意思就是是否继承全局的env，全局的包有可能污染virtualenv中的包，所以现在一般被设置成默认值，如果想要让现在的virtualenv继承全局环境中的包，则需要使用--system-site-packages这个option来显式设置。

```bash
$ virtualenv -h
Usage: virtualenv [OPTIONS] DEST_DIR

Options:
  --version             show program's version number and exit
  -h, --help            show this help message and exit
  -v, --verbose         Increase verbosity.
  -q, --quiet           Decrease verbosity.
  -p PYTHON_EXE, --python=PYTHON_EXE
                        The Python interpreter to use, e.g.,
                        --python=python3.5 will use the python3.5 interpreter
                        to create the new environment.  The default is the
                        interpreter that virtualenv was installed with
                        (/usr/local/opt/python@2/bin/python2.7)
  --clear               Clear out the non-root install and start from scratch.
  --no-site-packages    DEPRECATED. Retained only for backward compatibility.
                        Not having access to global site-packages is now the
                        default behavior.
  --system-site-packages
                        Give the virtual environment access to the global
                        site-packages.
  --always-copy         Always copy files rather than symlinking.
  --relocatable         Make an EXISTING virtualenv environment relocatable.
                        This fixes up scripts and makes all .pth files
                        relative.
  --no-setuptools       Do not install setuptools in the new virtualenv.
  --no-pip              Do not install pip in the new virtualenv.
  --no-wheel            Do not install wheel in the new virtualenv.
  --extra-search-dir=DIR
                        Directory to look for setuptools/pip distributions in.
                        This option can be used multiple times.
  --download            Download preinstalled packages from PyPI.
  --no-download, --never-download
                        Do not download preinstalled packages from PyPI.
  --prompt=PROMPT       Provides an alternative prompt prefix for this
                        environment.
  --setuptools          DEPRECATED. Retained only for backward compatibility.
                        This option has no effect.
  --distribute          DEPRECATED. Retained only for backward compatibility.
                        This option has no effect.
  --unzip-setuptools    DEPRECATED.  Retained only for backward compatibility.
                        This option has no effect.              
```


### 使用virtualenvwrapper系列命令

用了一段时间之后你会发现，其实使用virtualenv这个命令来创建python环境，也是一件特别麻烦的事情，每次都需要找到virtualenv所在目录下面的bin目录中的activate文件，一般项目都在不同的文件夹下，项目之间虚拟环境的切换就成了一个特别麻烦的事情。

被折磨一段时间之后，我发现了一个python环境管理的神器virtualenvwrapper。正如它名字一样，这个工具是为了提升用户体验对virtualenv做了进一步的封装。主要表现在

1. 统一管理virtualenv

所有使用virtualenvwrapper工具链生成的env都是在同一个大的文件夹中。而且在使用virtualenv的时候不用痛苦的去找virtualenv所在路径。

2. 一键式的管理virtualenv
virtualenv的增删改查都只需要一个命令就够了

下面来体验一下这个工具：

* 安装virtualenvwrapper
```bash
$ pip install virtualenvwrapper
```

* 配置virtualenvwrapper
```bash
$ echo "export WORKON_HOME=~/Envs\nsource /usr/local/bin/virtualenvwrapper.sh" >> ~/.zshrc
$ mkdir ~/Envs
$ source ~/.zshrc
```

在第一次配置完成之后就不需要再配置其他的东西了

==如果使用bash，就把上面的`~/.zshrc`改成`~/.bashrc`==

==如果python的安装路径不在/usr/local/bin下面，可以用`find / -name virtualenvwrapper.sh`命令去查找，然后Good Luck==

==virtualenv的总目录～/Envs可以用你自己的名字，记得把WORKON_HOME也改成对应的路径就好==

* 使用virtualenvwrapper工具链
直接运行virtualenvwrapper可以得到所有的工具链列表
常用的也就是CRUD（增删改查）这四个操作再加上一个进入virtualenv的操作：

	* 创建virtualenv
	```bash
	$ mkvirtualenv -p python3 env_name
	```

	* 删除virtualenv
	```bash
	$ rmvirtualenv env_name
	```

	* 列出所有virtualenv
	```bash
	$ workon
	```
	* 进入virtualenv
	```bash
	$ workon env_name
	```

     * 更新virtualenv
     直接使用pip就好

#### ==ref==

```bash
$ virtualenvwrapper
virtualenvwrapper is a set of extensions to Ian Bicking's virtualenv
tool.  The extensions include wrappers for creating and deleting
virtual environments and otherwise managing your development workflow,
making it easier to work on more than one project at a time without
introducing conflicts in their dependencies.

For more information please refer to the documentation:

    http://virtualenvwrapper.readthedocs.org/en/latest/command_ref.html

Commands available:

  add2virtualenv: add directory to the import path

  allvirtualenv: run a command in all virtualenvs

  cdproject: change directory to the active project

  cdsitepackages: change to the site-packages directory

  cdvirtualenv: change to the $VIRTUAL_ENV directory

  cpvirtualenv: duplicate the named virtualenv to make a new one

  lssitepackages: list contents of the site-packages directory

  lsvirtualenv: list virtualenvs

  mkproject: create a new project directory and its associated virtualenv

  mktmpenv: create a temporary virtualenv

  mkvirtualenv: Create a new virtualenv in $WORKON_HOME

  rmvirtualenv: Remove a virtualenv

  setvirtualenvproject: associate a project directory with a virtualenv

  showvirtualenv: show details of a single virtualenv

  toggleglobalsitepackages: turn access to global site-packages on/off

  virtualenvwrapper: show this help message

  wipeenv: remove all packages installed in the current virtualenv

  workon: list or change working virtualenvs
```
