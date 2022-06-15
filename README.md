# configurations

## jupyter
python 3.10 安装 jupyterlab 和其他库的时候会遇到导入`_ssl`库失败的情况，是因为编译安装时没有指定相应的`openssl>1.1.1`的包

1. 安装 openssl

[参考官网](https://docs.python.org/3/using/unix.html?highlight=openssl#custom-openssl)

- 查找 ssl 路径：`find /etc/ -name openssl.cnf -printf "%h\n"`
- 下载 [openssl](https://www.openssl.org/source/)：`curl -O https://www.openssl.org/source/openssl-VERSION.tar.gz`
- 解压：`tar xzf openssl-VERSION`
- pushd openssl-VERSION # 保存当前工作目录到内存，并且使用`openssl-VERSION`作为工作目录 [pushd & popd](https://en.wikipedia.org/wiki/Pushd_and_popd#:~:text=The%20pushd%20command%20saves%20the,Location%20%2Dstack%20in%20Windows%20PowerShell)
- ./config --prefix=[自定义安装路径S1] --libdir=lib --openssldir=/etc/ssl
- 编译安装：`make -j1 depend && make -j8 && make install_sw`
- popd

2. 安装 python3

- 安装包下载：[国内](https://registry.npmmirror.com/binary.html?path=python/)  [官网](https://www.python.org/ftp/python/)
- 同理省略...
- pushd python-3.x.x 
- 配置：`./configure -C --with-openssl=[自定义安装路径S1] --with-openssl-rpath=auto --prefix=[自定义python路径]`
- 编译安装：`make -j8 && make altinstall`
- popd

3. 安装 jupyterlab

[参考官网](https://jupyter.org/install)
[配置参考](https://iscottmark.github.io/2021/jupyterlab/)

- pip 安装 jupyterlab：`pip3 install jupyterlab -i https://pypi.doubanio.com/simple/`
- 生成密码
```python
# 生成 hash 密码
from jupyter_server.auth import passwd
passwd()
# 依据提示输入两遍设置的密码，从而会得到一个的[密钥S2]
```
- 生成配置文件：`jupyter-lab --generate-config`
- 添加配置：`vim ~/.jupyter/jupyter_lab_config.py`
```
c.ServerApp.allow_remote_access = True
c.ServerApp.ip = '*'

# 启动时不自动打开浏览器 
c.ServerApp.open_browser = False
c.LabServerApp.open_browser = False
c.ExtensionApp.open_browser = False
c.LabApp.open_browser = False

# 添加刚刚生成的密钥
c.ServerApp.password = '[密钥S2]'

# 根据个人需要修改端口号（默认 8888）
c.ServerApp.port = 8889
# 如果使用 root 用户启动需要添加
c.ServerApp.allow_root = True
# 指定 ipyb 文件存放地址--工作路径
c.ServerApp.notebook_dir = '/app/jupyternotebook'
```
