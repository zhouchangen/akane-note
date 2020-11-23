# 2 Python-Flask

## 1. Virtualenv虚拟环境

是一个互相隔离的目录，因为我们可能用到不同版本的环境

```
mkvirtualenv flask_py
mkvirtualenv flask_py -p python3  // p: path
pip install flask=0.10.1
pip list // 查看安装了什么安装包
```



### 1.2 pip是什么？

pip 是 Python 包管理工具

```
sudo apt-get install python-pip
```



### 1.3 site-package是什么？

python包的存储





### 1.4 别人下载了你的项目，如何安装依赖包？

**1.首先备份**

```
pip freeze > requirements.txt
```

**2.恢复，别人执行即可安装相应的依赖**

```
pip install -r requirements.txt
```



## 2. Python的Web框架

Django、Flask