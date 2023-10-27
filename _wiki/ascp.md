---
layout: wiki
title: ascp
cate1: BioTools
cate2: DataDownload
description: 利用 ascp 下载数据
keywords: ascp
---

### 如何安装 ascp？

直接推荐 conda 下载的方式：

```shell
# 安装
conda create -n ascp
conda activate ascp
conda install -c hcc aspera-cli
# 安装后id_rsa的位置为：
/root/miniconda3/envs/ascp/etc/asperaweb_id_dsa.openssh
```

### 如何使用 ascp？

一个简单的下载示例：

```shell
ascp -i /root/miniconda3/envs/ascp/etc/asperaweb_id_dsa.openssh -l 100M -QT -P33001 -k1 era-fasp@fasp.sra.ebi.ac.uk:/vol1/fastq/SRR769/007/SRR7696207/SRR7696207_1.fastq.gz ./
```

### 去哪里获取待下载数据的链接？

我这里只推荐 ENA 数据库来获取数据下载链接 [ENA](https://www.ebi.ac.uk/ena/browser/home)

ENA 提供了 SRA 数据和原始的 fq.gz 数据下载链接。比如下载 SRR7696207 的双端数据，在 ENA 中搜索 SRR7696207 后，选择显示 fastq_aspera 和 sra_aspera（默认是不显示的）。

![show_ascp_of_sra_fq](/images/wiki/ascp_1.png)

然后点击对应的链接就可以复制 ascp 下载链接了，直接替换下载链接即可。

![copy_ascp_of_sra_fq](/images/wiki/ascp_2.png)
