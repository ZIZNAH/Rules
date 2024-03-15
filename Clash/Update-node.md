# 更新node

## 使用 Node 版本管理器（NVM）

### 1、安装 NVM：
如果你还没有安装 NVM，可以使用以下命令在 Ubuntu 上安装：
~~~bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
~~~
或者使用 wget 安装：
~~~bash
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
~~~

### 2、重新加载 shell 配置：
执行以下命令以重新加载 shell 配置文件（或者关闭当前终端并重新打开一个新的终端）：
~~~bash
source ~/.bashrc
~~~
如果你使用的是 Zsh，应该是 ~/.zshrc 文件。

### 3、安装 Node.js：
使用 NVM 安装你想要的 Node.js 版本。例如，要安装最新的 LTS 版本，可以运行以下命令：
~~~bash
nvm install --lts
~~~
或者指定其他版本号：
~~~bash
nvm install <version>
~~~

### 4、切换版本：
安装完毕后，可以使用以下命令切换到新安装的版本：
~~~bash
nvm use <version>
~~~

### 5、验证：
确认 Node.js 版本已成功升级：
~~~bash
node -v
~~~
确认 npm 也已更新：
~~~bash
npm -v
~~~
这样，你就成功地使用 NVM 升级了 Node.js。
