# Huggingface 无法访问问题
找到huggingface_hub依赖包下载配置文件，参考下面这个地址：
```
/path/to/env/site-packages/huggingface_hub/constants.py
```
如果环境中有HF_ENDPOINT的设定就会采用该设定作为前缀，即上面镜像的https://hf-mirror.com而非默认的https://huggingface.co

![Image](/image/111.PNG)

原文链接：https://blog.csdn.net/qq_40277409/article/details/138566289?ops_request_misc=&request_id=&biz_id=102&utm_term=huggingface%E5%AE%98%E7%BD%91%E6%97%A0%E6%B3%95%E8%AE%BF%E9%97%AE&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-7-138566289.142^v100^pc_search_result_base1&spm=1018.2226.3001.4187


## 问题2：
OSError: We couldn't connect to 'https://huggingface.co' to load this file, couldn't find it in the c
ached files and it looks like bert-base-uncased is not the path to a directory containing a file name
d config.json.
Checkout your internet connection or see how to run the library in offline mode at 'https://huggingfa
ce.co/docs/transformers/installation#offline-mode'.

## 解决方法：
```
import os
os.environ['HF_ENDPOINT'] = 'https://hf-mirror.com'
from transformers import BertModel

```
