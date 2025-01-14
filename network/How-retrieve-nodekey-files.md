*nodekey文件找回参考

# 一、说明
本文档适用于机器上有备份与云文档登记或节点信息一致信息的nodekey文件，但当前运行nodekey与云文档不相符的情况。思路是找到与当前节点nodekey不同的文件进行替换，然后重启节点，尝试恢复节点信息与云文档登记的一致

# 二、注意
cat是查看命令，cat nodekey的内容时，显示的内容一定要保密
示例：

root@VM-8-2-ubuntu:/home/lighthouse#cat /home/lighthouse/data/mainnet.old/geth/nodekey

9xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxyyyyroot@VM-8-2-ubuntu:/home/lighthouse#，其中9xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxyyyy一定要保密

# 三、正文

1、查看当前节点信息：

lighthouse@VM-8-2-ubuntu:~$```sudo docker exec -it jouleverse-mainnet /j/geth attach /data/mainnet/geth.ipc```

'>'```admin.nodeInfo```

'>'```exit```  ///退出geth

2、进入管理员模式

lighthouse@VM-8-2-ubuntu:~$```sudo -s```

3、查找叫nodekey的文件，示例：

root@VM-8-2-ubuntu:/home/lighthouse#```find / -name "nodekey" 2>/dev/null```

/home/lighthouse/data/nodekey

/home/lighthouse/data/mainnet/geth/nodekey

/home/lighthouse/data/mainnet.old/mainnet/geth/nodekey

/home/lighthouse/data/mainnet.old/geth/nodekey

4、查看nodekey的文件与当前运行节点文件的差异，示例：

root@VM-8-2-ubuntu:/home/lighthouse#```cat /home/lighthouse/data/nodekey```

3xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxttttroot@VM-8-2-ubuntu:/home/lighthouse# 

root@VM-8-2-ubuntu:/home/lighthouse#```cat /home/lighthouse/data/mainnet/geth/nodekey```   //当前节点nodekey文件路径

3xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxttttroot@VM-8-2-ubuntu:/home/lighthouse#    //当前节点nodekey文件内容

root@VM-8-2-ubuntu:/home/lighthouse#```cat /home/lighthouse/data/mainnet.old/mainnet/geth/nodekey```

9xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxyyyyroot@VM-8-2-ubuntu:/home/lighthouse# 

root@VM-8-2-ubuntu:/home/lighthouse#```cat /home/lighthouse/data/mainnet.old/geth/nodekey```   //先前节点nodekey文件路径

9xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxyyyyroot@VM-8-2-ubuntu:/home/lighthouse#    //先前节点nodekey文件内容

5、复制先前nodekey文件到当前节点文件夹，示例：

root@VM-8-2-ubuntu:/home/lighthouse# ```cp /home/lighthouse/data/mainnet.old/geth/nodekey /home/lighthouse/data/mainnet/geth/nodekey```

6、查看复制后的当前nodekey文件内容，示例：

root@VM-8-2-ubuntu:/home/lighthouse#```cat /home/lighthouse/data/mainnet/geth/nodekey```

9xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxyyyyroot@VM-8-2-ubuntu:/home/lighthouse#

7、节点重启

（1）停止节点运行：root@VM-8-2-ubuntu:/home/lighthouse#```sudo docker stop jouleverse-mainnet```

（2）一条命令清除所有已停止的容器（当遇到提示容器已存在错误时可随时执行）：root@VM-8-2-ubuntu:/home/lighthouse#```sudo docker container prune```

（3）启动见证节点。执行下述命令，后台运行节点（关闭远程终端后会持续运行）： root@VM-8-2-ubuntu:/home/lighthouse#```sudo docker run -v ~/jouleverse-node-20240115:/j -v ~/data:/data --name "jouleverse-mainnet" -p 30311:30311/tcp -p 30311:30311/udp -p 8501:8501 -d ubuntu:20.04 /j/geth --config /j/node-mainnet-witness.toml --snapshot=false --ignore-legacy-receipts```

（4）确认节点成功运行：root@VM-8-2-ubuntu:/home/lighthouse#```sudo docker ps```

（5）检查后台运行节点的日志：root@VM-8-2-ubuntu:/home/lighthouse#```sudo docker logs -n 100 -f jouleverse-mainnet```

观察节点运行日志，应该能够找到其他节点并开始同步区块链，出现类似这样的日志：

```
INFO [09-28|12:52:39.426] Imported new chain segment               blocks=2048 txs=2 mgas=0.042 elapsed=3.446s      mgasps=0.012 number=37943 hash=a43ebf..37a8c0 age=11mo3w42m dirty=2.69KiB
INFO [09-28|12:52:43.182] Imported new chain segment               blocks=2048 txs=13 mgas=0.273 elapsed=3.732s      mgasps=0.073 number=39991 hash=110c48..f0999f age=11mo2w6d  dirty=2.69KiB
INFO [09-28|12:52:46.944] Imported new chain segment               blocks=2048 txs=3  mgas=0.063 elapsed=3.747s      mgasps=0.017 number=42039 hash=1a0a88..98d165 age=11mo2w6d  dirty=2.69KiB
INFO [09-28|12:52:50.256] Imported new chain segment               blocks=2048 txs=1  mgas=0.021 elapsed=3.291s      mgasps=0.006 number=44087 hash=3ed781..448020 age=11mo2w5d  dirty=2.69KiB
```

（6）ctrl+c终止日志刷新

8、查看当前节点信息

root@VM-8-2-ubuntu:/home/lighthouse#```exit```

exit

lighthouse@VM-8-2-ubuntu:~$```sudo docker exec -it jouleverse-mainnet /j/geth attach /data/mainnet/geth.ipc```

进入geth控制台

'>'```admin.nodeInfo```

显示节点信息

出现节点信息与云文档登记信息核对

'>'```exit```  ///退出geth
