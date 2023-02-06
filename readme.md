# aws linux 安装python环境
- 检查当前的环境
```
[ec2-user@ip-172-31-1-5 ~]$ python --version
Python 2.7.18

[ec2-user@ip-172-31-1-5 ~]$ python3 --version
Python 3.7.16
```
- 系统的信息
```
cat /proc/version
Linux version 5.10.162-141.675.amzn2.x86_64 (mockbuild@ip-10-0-51-51) (gcc10-gcc (GCC) 10.3.1 20210422 (Red Hat 10.3.1-1), GNU ld version 2.35.2-9.amzn2.0.1) #1 SMP Mon Jan 9 22:45:11 UTC 2023
```

## 安装新版本python3
- 准备依赖
```
sudo yum update -y
sudo yum groupinstall "Development Tools" -y
```
- 删除原来的openssl
```
sudo yum erase openssl-devel -y
```
- 安装依赖
```
sudo yum install openssl11 openssl11-devel  libffi-devel bzip2-devel wget -y
```
- 检查gcc版本
```
[ec2-user@ip-172-31-57-202 ~]$ gcc --version
gcc (GCC) 7.3.1 20180712 (Red Hat 7.3.1-15)
Copyright (C) 2017 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```
- 下载，解压
```
wget https://www.python.org/ftp/python/3.11.1/Python-3.11.1.tgz
tar -xf Python-3.11.1.tgz
cd Python-3.11.1
```
- 编译，安装
```
./configure --enable-optimizations
[ec2-user@ip-172-31-62-220 Python-3.11.1]$ ./configure --enable-optimizations
checking build system type... x86_64-pc-linux-gnu
checking host system type... x86_64-pc-linux-gnu
checking for Python interpreter freezing... ./_bootstrap_python
checking for python3.11... no
checking for python3.10... no
checking for python3.9... no
checking for python3.8... no
checking for python3.7... python3.7
…… （2分钟左右）
config.status: creating Modules/ld_so_aix
config.status: creating pyconfig.h
configure: creating Modules/Setup.local
configure: creating Makefile

[ec2-user@ip-172-31-62-220 Python-3.11.1]$ make
Running code to generate profile data (this can take a while):
# First, we need to create a clean build with profile generation
# enabled.
make profile-gen-stamp
make[1]: Entering directory `/home/ec2-user/Python-3.11.1'
make clean
make[2]: Entering directory `/home/ec2-user/Python-3.11.1'
find . -depth -name '__pycache__' -exec rm -rf {} ';'
…… （10分钟左右）
make[1]: Leaving directory `/home/ec2-user/Python-3.11.1'

[ec2-user@ip-172-31-62-220 Python-3.11.1]$ sudo make altinstall
……
Looking in links: /tmp/tmpbd0zx3by
Processing /tmp/tmpbd0zx3by/setuptools-65.5.0-py3-none-any.whl
Processing /tmp/tmpbd0zx3by/pip-22.3.1-py3-none-any.whl
Installing collected packages: setuptools, pip
  WARNING: The script pip3.11 is installed in '/usr/local/bin' which is not on PATH.
  Consider adding this directory to PATH or, if you prefer to suppress this warning, use --no-warn-script-location.
Successfully installed pip-22.3.1 setuptools-65.5.0
WARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager. It is recommended to use a virtual environment instead: https://pip.pypa.io/warnings/venv
[ec2-user@ip-172-31-62-220 Python-3.11.1]$ 
```
- 验证安装
```
[ec2-user@ip-172-31-62-220 Python-3.11.1]$ python3.11 --version
Python 3.11.1
```
## 搭建虚拟环境
- 准备安装虚拟环境 - 检查更新
```
yum check-update
```
### 方法1：用当前系统的python来创建虚拟环境。虚拟环境只能用系统的python版本
```
[ec2-user@ip-172-31-1-5 ~]$ mkdir test1
[ec2-user@ip-172-31-1-5 ~]$ python3 -m venv test1/myenv
[ec2-user@ip-172-31-1-5 ~]$ source test1/myenv/bin/activate
```
### 方法2：使用virtualenv创建不同版本python的虚拟环境
- 1 安装virtualenv
```
	pip3 install virtualenv
```
- 2 测试你的安装
```
[ec2-user@ip-172-31-1-5 ~]$ virtualenv --version
virtualenv 20.17.1 from /home/ec2-user/.local/lib/python3.7/site-packages/virtualenv/__init__.py
```
- 3 创建虚拟环境
```
mkdir test2
[ec2-user@ip-172-31-1-5 ~]$ virtualenv  test2/venv
created virtual environment CPython3.7.16.final.0-64 in 871ms
  creator CPython3Posix(dest=/home/ec2-user/test2/venv, clear=False, no_vcs_ignore=False, global=False)
  seeder FromAppData(download=False, pip=bundle, setuptools=bundle, wheel=bundle, via=copy, app_data_dir=/home/ec2-user/.local/share/virtualenv)
    added seed packages: pip==22.3.1, setuptools==65.6.3, wheel==0.38.4
  activators BashActivator,CShellActivator,FishActivator,NushellActivator,PowerShellActivator,PythonActivator
  ```
- 4 创建python2的虚拟环境
```
[ec2-user@ip-172-31-1-5 ~]$ python --version
Python 2.7.18
[ec2-user@ip-172-31-1-5 ~]$ which python
/usr/bin/python
[ec2-user@ip-172-31-1-5 ~]$ virtualenv -p /usr/bin/python test2/venv2
created virtual environment CPython2.7.18.final.0-64 in 845ms
  creator CPython2Posix(dest=/home/ec2-user/test2/venv2, clear=False, no_vcs_ignore=False, global=False)
  seeder FromAppData(download=False, pip=bundle, setuptools=bundle, wheel=bundle, via=copy, app_data_dir=/home/ec2-user/.local/share/virtualenv)
    added seed packages: pip==20.3.4, setuptools==44.1.1, wheel==0.37.1
  activators BashActivator,CShellActivator,FishActivator,NushellActivator,PowerShellActivator,PythonActivator
[ec2-user@ip-172-31-1-5 ~]$ source test2/venv2/bin/activate
(venv2) [ec2-user@ip-172-31-1-5 ~]$ python --version
Python 2.7.18

   ```
### 方法3：使用pyenv创建不同子版本的python虚拟环境
- 1 安装pyenv
```
sudo yum -y install gcc gcc-c++ make git patch zlib-devel readline-devel sqlite-devel bzip2-devel bzip2-libs xz-devel
sudo yum install -y net-tools vim lrzsz tree screen lsof tcpdump nmap sysstat dos2unix
git clone https://github.com/pyenv/pyenv.git ~/.pyenv

echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n eval "$(pyenv init -)"\nfi' >> ~/.bashrc

git clone https://github.com/pyenv/pyenv-virtualenv.git ~/.pyenv/plugins/pyenv-virtualenv
echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.bashrc
source ~/.bashrc
```

- 2 pyenv的使用
	- 安装指定版本的python
```
 pyenv install --list
 pyenv install 2.7.15
 pyenv install 3.6.5
```
	- 卸载指定版本的python
```
pyenv uninstall 2.7.15
```
	- 创建虚拟环境
```
pyenv virtualenv 2.7.15 p2715
```
	- 删除虚拟环境
```
pyenv virtualenv-delete p2715
```

### 方法4 conda:
- 1 澄清概念：
Anaconda与conda、pip与conda的区别

- 2 获得python3版本
```
python3 --version
Python 3.7.16
```
- 3 下载对应版本的miniconda的installer
网址
https://docs.conda.io/en/latest/miniconda.html#linux-installers
```
wget https://repo.anaconda.com/miniconda/Miniconda3-py37_22.11.1-1-Linux-x86_64.sh
#验证sha256哈希
sha256sum Miniconda3-py37_22.11.1-1-Linux-x86_64.sh
```
- 4 运行installer
```
[ec2-user@ip-172-31-62-220 ~]$ bash Miniconda3-py37_22.11.1-1-Linux-x86_64.sh
Welcome to Miniconda3 py37_22.11.1-1
In order to continue the installation process, please review the license
agreement.
Please, press ENTER to continue
……

The following packages listed on https://www.anaconda.com/cryptography are included in the Repository accessible through Miniconda that relate to cryptography.
Last updated March 21, 2022
Do you accept the license terms? [yes|no]
[no] >>> yes  #此处选yes


Miniconda3 will now be installed into this location:
/home/ec2-user/miniconda3
  - Press ENTER to confirm the location
  - Press CTRL-C to abort the installation
  - Or specify a different location below
[/home/ec2-user/miniconda3] >>>  #此处按回车


PREFIX=/home/ec2-user/miniconda3
Unpacking payload ...
Installing base environment...
Downloading and Extracting Packages
Downloading and Extracting Packages
Preparing transaction: done
Executing transaction: done
installation finished.
Do you wish the installer to initialize Miniconda3
by running conda init? [yes|no]
[no] >>> yes  #此处选yes


no change     /home/ec2-user/miniconda3/condabin/conda
no change     /home/ec2-user/miniconda3/bin/conda
no change     /home/ec2-user/miniconda3/bin/conda-env
……
If you'd prefer that conda's base environment not be activated on startup,
   set the auto_activate_base parameter to false:

conda config --set auto_activate_base false

Thank you for installing Miniconda3!

# 安装完成
```
- 5 重新启动终端
- 6 验证安装
```
conda list
```
- 7 conda的使用
	- 启动虚拟环境
```
conda activate py3.5
```
	- 虚拟环境列表以及当前的虚拟环境
```
conda info --envs  或者  conda env list
```

	- 退出虚拟环境
```
conda deactivate
```
	- 创建虚拟环境
```
conda create --name py3.5 python=3.5
conda create --name py2.7.15 python=2.7.15
```
	- 删除一个虚拟环境
```
conda remove --name py3.5 --all
```
	- 为当前虚拟环境安装包
```
conda install numpy
```
	- 删除当前环境中的包
```
conda remove  numpy
```
	- 在主环境中向指定名称的虚拟环境安装包
```
conda install -n py3.5 numpy
```

