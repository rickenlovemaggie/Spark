
1、进入 yum 源配置目录

```
cd /etc/yum.repos.d
```

2、备份系统自带 yum 源

```
mv CentOS-Base.repo CentOS-Base.repo.bak
```

3、下载新的 yum 源，比如 网易的 yum 源：

```
wget http://mirrors.163.com/.help/CentOS6-Base-163.repo
```

4、更新 yum 源：

```
yum makecache
```
