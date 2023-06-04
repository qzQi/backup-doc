# conda使用

没想到Linux可以直接使用conda。

https://tf.wiki/zh_hans/basic/installation.html

```bash
conda create --name tf2 python=3.7      # “tf2”是你建立的conda虚拟环境的名字
conda activate tf2                      # 进入名为“tf2”的conda虚拟环境
conda deactivate            # to deactivate tf2
```

在 Python 开发中，很多时候我们希望每个应用有一个独立的 Python 环境（比如应用 1 需要用到 TensorFlow 1.X，而应用 2 使用 TensorFlow 2.0）。这时，Conda 虚拟环境即可为一个应用创建一套 “隔离” 的 Python 运行环境。使用 Python 的包管理器 conda 即可轻松地创建 Conda 虚拟环境。常用命令如下：

```bash
conda create --name [env-name]      # 建立名为[env-name]的Conda虚拟环境
conda activate [env-name]           # 进入名为[env-name]的Conda虚拟环境
conda deactivate                    # 退出当前的Conda虚拟环境
conda env remove --name [env-name]  # 删除名为[env-name]的Conda虚拟环境
conda env list                      # 列出所有Conda虚拟环境
```



## 作业temp

