# 错误集锦（中文）

更新于3.6 15：39

## pytorch is not compiled with nccl support

[NCCL]（https://blog.csdn.net/qq_40185847/article/details/115074443）

## RuntimeError: [enforce fail at ..\c10\core\CPUAllocator.cpp:72] data. DefaultCPUAllocator

[tensor error](https://blog.csdn.net/Hunter_Murphy/article/details/107923209)

## DefaultCPUAllocator: not enough memory: you tried to allocate 94633984 bytes. Buy new RAM!

小结中的3.6日更新已解决

[小结](./小结.md)

[memory](https://blog.csdn.net/canpian7/article/details/115419162)

## 理解finetrue

虽然不难理解，但还是贴个我学习时候的[link](https://zhuanlan.zhihu.com/p/35890660)

## 多卡运行
[Multi-GPU](https://blog.csdn.net/shulongjiang/article/details/110789001)

## iccp libpng warning 
自己重新洗数据，无解

## cache rewrite
重新训练前把已经保存的train cache移出去，或者让他自己保存npy文件也行

## 提示warning wandb的
pip install wandb，看weight和bias的

## msi after runner
重启解决

## 训练完后训练机迟滞
保存完数据之后就重启，固态寄了而已（也不能说寄，缓存跟不上，有积攒数据处理的非常慢）

## 3.9 15：35
已证实双卡都加载的情况下，运行wandb保存训练数据时会造成严重的内存泄露，导致以下结果，包括但不限于死机，重启，cpu非训练时80%+负载，GPU2掉线，主板报警，建议所有人使用双卡训练时关闭wandb，使用tensorboard以查看数据。使用YOLO框架的建议使用最新的release或者将其他训练时产生的问题提交issue。

## 3.9 15：38
GPU2 无法跑在PCIE 3.0x16下，速率协商确实有问题，兼容模式跑在PCIE3.0x4上，注意一下bs大小和负载可安全完成调参和训练。

## 提示tensorboard 但是你又打不开
在events.out.tfevents的上一级调用cmd执行tensorboard --logdir=1，出现的logdir后的就是log所在文件夹，此时运行chrome就行了，地址是127.0.0.1:6006或localhost:6006

## cv2 相关的 
此时应该跑完了第一轮epoch，在对图像绘图时内存不够，适当调小bs即可

## 3.6 19：57 新增
## wandb.errors.UsageError: api_key not configured (no-tty). call wandb.login(key=[your_api_key])

在terminal里面查看一下装了wandb没有，装了就wandb init，会给出网址，登录完就行了，最后会给一个不可输入的验证码，点击复制后paste到terminal里面，直接enter，验证码是不可见类型，看不到是正常的。

怎么看？跑train的时候有usage和分布图链接

## wandb: Network error (ConnectionError), entering retry loop.
问题在测试10epochs的时候出现，loss图非常完美，但是最后processing的时候loop了很久

额，截至到现在其实都没有比较好的方式解决，我的话可能会选择offline去看，但是这样就不能云端同步，看不到微调后的云端对比数据[documents](https://docs.wandb.ai/guides/self-hosted/local)

## wandb: Network error (TransientError), entering retry loop.

同上

## OSError: [WinError 1455] 页面文件太小，无法完成操作。 Error loading “D:\Anaconda\envs\pytorch-1.4\lib\site-package
要么调小bs。要么把workers设为0

## \VEN_10DE&DEV_17C2&SUBSYS_113210DE&REV_A1\6&7dcfedc&0&0000020A 需要进一步安装。
问题出自训练机，公版卡的pcie速率有问题，协商的速率问题很大，交换pcie口后无法解决，搁置。

## RuntimeError: can't start new thread
出自finetune轮末期，调小workers数到4即可。

## 显卡显存寄了
ros看一眼哪个颗粒寄了，可能是脱焊，热风枪吹一下看颗粒数值，还是不行就是真寄了

## RuntimeError: Unable to find a valid cuDNN algorithm to run convolution
这个时候不是cudnn真的寄了，是bs太大，显存撑不住，调小点bs。当然前期遇到这问题说不定是你cudnn没配置好

## 3.9 16:08 CUDA memory 相关，提示了 CUDA launch blocking和CUDA API相关，没能log下来，系统保护重启了
暂无解决方式
[ISSUE](https://github.com/pytorch/pytorch/issues/9163),解决的其中之一，但是训练机我默认开启NCCL，所以大概率还是卡2的问题
## 软重启后丢失GPU2，出自训练机
硬重启一波看看，如果还不行就寄，降低PCIE速率试试，如果还不行就显存出问题了。

## loss, loss_items = compute_loss(pred, targets.to(device))  # loss scaled by batch_size
RuntimeError: CUDA error: unspecified launch failure
CUDA kernel errors might be asynchronously reported at some other API call,so the stacktrace below might be incorrect.
For debugging consider passing CUDA_LAUNCH_BLOCKING=1.
正在解决

## RuntimeError: DataLoader worker (pid(s) 11800, 1728) exited unexpectedly
先试试0行不行，不行可能是bs大，调小bs再不行就是代码寄
