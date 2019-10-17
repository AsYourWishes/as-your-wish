# Git相关问题记录

1. Gitlab服务器IP地址变更后，已存在项目地址中IP部分不变，导致地址不匹配。

   修改/var/opt/gitlab/nginx/conf/gitlab-http.conf里一个ip地址

2. 查找 git config 文件位置

   Git Bash 中执行`git config --global -e`，可以充底部看到 global config 文件的路径
