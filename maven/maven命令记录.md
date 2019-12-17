# maven命令记录

## 1. 删除下载失败的jar包

到本地仓库执行命令：

* windows

  ```powershell
  for /r %i in (*.lastUpdated) do del %i
  ```

* linux

  ```shell
  find ~/ . -name "*.lastUpdated" -exec rm -rf {} \;
  ```

  