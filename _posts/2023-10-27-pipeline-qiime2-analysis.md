---
layout: post
title: QIIME2 构建数据库以及常规分析流程
categories: [测序数据分析]
description: QIIME2 构建数据库以及常规分析流程
keywords: QIIME2，测序数据分析
---

QIIME2 分析流程是扩增子分析常用的流程之一，这里提供一个完整的 16S 数据分析流程，包括构建数据库以及常规分析。QIIME2 是各种插件组成的综合体 pipeline，具体的信息可以看官方网站 [QIIME2](https://docs.qiime2.org/2023.9/).

构建数据库的过程，参考：[Processing, filtering, and evaluating the SILVA database with RESCRIPt](https://forum.qiime2.org/t/processing-filtering-and-evaluating-the-silva-database-and-other-reference-sequence-data-with-rescript/15494)

## 下载 SILVA 数据库

在 [SILVA](https://www.arb-silva.de/no_cache/download/archive/release_138.1/Exports/) 数据库下载并解压如下文件：

- tax_slv_ssu_138.1.txt
- taxmap_slv_ssu_ref_nr_138.1.txt
- tax_slv_ssu_138.1.tre
- SILVA_138.1_SSURef_NR99_tax_silva_trunc.fasta

## 初步构建 QIIME2 物种分类数据库

Import the Taxonomy Rank file

```shell
qiime tools import \
    --type 'FeatureData[SILVATaxonomy]' \
    --input-path tax_slv_ssu_138.1.txt \
    --output-path taxranks-silva-138.1-ssu-nr99.qza
```

Import the Taxonomy Mapping file

```shell
qiime tools import \
    --type 'FeatureData[SILVATaxidMap]' \
    --input-path taxmap_slv_ssu_ref_nr_138.1.txt \
    --output-path taxmap-silva-138.1-ssu-nr99.qza
```

Import the Taxonomy Hierarchy Tree file

```shell
qiime tools import \
    --type 'Phylogeny[Rooted]' \
    --input-path tax_slv_ssu_138.1.tre \
    --output-path taxtree-silva-138.1-nr99.qza
```

Import the sequence file

```shell
qiime tools import \
    --type 'FeatureData[RNASequence]' \
    --input-path SILVA_138.1_SSURef_NR99_tax_silva_trunc.fasta \
    --output-path silva-138.1-ssu-nr99-rna-seqs.qza
```

转换 FeatureData[RNASequence] 为 FeatureData[DNASequence]，获得物种序列

```shell
qiime rescript reverse-transcribe \
    --i-rna-sequences silva-138.1-ssu-nr99-rna-seqs.qza \
    --o-dna-sequences silva-138.1-ssu-nr99-seqs.qza
```

使用 qiime2 的 silva 分类法创建物种标签分类

```shell
qiime rescript parse-silva-taxonomy \
    --i-taxonomy-tree taxtree-silva-138.1-nr99.qza \
    --i-taxonomy-map taxmap-silva-138.1-ssu-nr99.qza \
    --i-taxonomy-ranks taxranks-silva-138.1-ssu-nr99.qza \
    --o-taxonomy silva-138.1-ssu-nr99-tax.qza
```

------------------------------------------- Optional (可选的) -------------------------------------------

也可以直接下载别人构建好的数据库来使用

```shell
qiime rescript get-silva-data \
    --p-version '138.1' \
    --p-target 'SSURef_NR99' \
    --o-silva-sequences silva-138.1-ssu-nr99-rna-seqs.qza \
    --o-silva-taxonomy silva-138.1-ssu-nr99-tax.qza

qiime rescript reverse-transcribe \
    --i-rna-sequences silva-138.1-ssu-nr99-rna-seqs.qza
    --o-dna-sequences silva-138.1-ssu-nr99-seqs.qza
```

------------------------------------------- Optional (可选的) -------------------------------------------

## 对初步数据库进行质控

删除有 5 个以上模糊碱基的序列，8 个碱基以上的同聚物（Homopolymer 是指基因组上单一碱基重复的区域）

```shell
# 这里用的是默认参数
qiime rescript cull-seqs \
    --i-sequences silva-138.1-ssu-nr99-seqs.qza \
    --o-clean-sequences silva-138.1-ssu-nr99-seqs-cleaned.qza
```

通过序列长度和物种分类信息过滤数据库中的序列，具体地，Archaea (16S) >= 900 bp, Bacteria (16S) >= 1200 bp, and any Eukaryota (18S) >= 1400 bp.

```shell
qiime rescript filter-seqs-length-by-taxon \
    --i-sequences silva-138.1-ssu-nr99-seqs-cleaned.qza \
    --i-taxonomy silva-138.1-ssu-nr99-tax.qza \
    --p-labels Archaea Bacteria Eukaryota \
    --p-min-lens 900 1200 1400 \
    --o-filtered-seqs silva-138.1-ssu-nr99-seqs-filt.qza \
    --o-discarded-seqs silva-138.1-ssu-nr99-seqs-discard.qza
```

去除重复冗余的序列

```shell
qiime rescript dereplicate \
    --i-sequences silva-138.1-ssu-nr99-seqs-filt.qza  \
    --i-taxa silva-138.1-ssu-nr99-tax.qza \
    --p-mode 'uniq' \
    --o-dereplicated-sequences silva-138.1-ssu-nr99-seqs-derep-uniq.qza \
    --o-dereplicated-taxa silva-138.1-ssu-nr99-tax-derep-uniq.qza
```

## 构建物种分类器

经过上述处理后，构建 bayes 物种分类器，当然也有其他算法的分类器，可以看 QIIME2 官网，这里的分类器基于的 silva 数据库中 16S 全长序列（部分可能不是）。输出的 silva-138.1-ssu-nr99-classifier.qza 是后续分析可以用到的。

```shell
qiime feature-classifier fit-classifier-naive-bayes \
  --i-reference-reads  silva-138.1-ssu-nr99-seqs-derep-uniq.qza \
  --i-reference-taxonomy silva-138.1-ssu-nr99-tax-derep-uniq.qza \
  --o-classifier silva-138.1-ssu-nr99-classifier.qza
```

------------------------------------------- Optional (可选的) -------------------------------------------

上述步骤基于的是 16S 全长数据库，可以基于测序区间不同，来构建所需要引物区间的物种分类器，比如 515f-806r

```shell
qiime feature-classifier extract-reads \
    --i-sequences silva-138.1-ssu-nr99-seqs-derep-uniq.qza \
    --p-f-primer GTGYCAGCMGCCGCGGTAA \
    --p-r-primer GGACTACNVGGGTWTCTAAT \
    --p-n-jobs 2 \
    --p-read-orientation 'forward' \
    --o-reads silva-138.1-ssu-nr99-seqs-515f-806r.qza
```

在构建所需引物区间后还会再进行去重，固定引物区间后还会有可能有重复的

```shell
qiime rescript dereplicate \
    --i-sequences silva-138.1-ssu-nr99-seqs-515f-806r.qza \
    --i-taxa silva-138.1-ssu-nr99-tax-derep-uniq.qza \
    --p-mode 'uniq' \
    --o-dereplicated-sequences silva-138.1-ssu-nr99-seqs-515f-806r-uniq.qza \
    --o-dereplicated-taxa  silva-138.1-ssu-nr99-tax-515f-806r-derep-uniq.qza
```

在上述基础上，构建最终的 515f-806r 分类器

```shell
qiime feature-classifier fit-classifier-naive-bayes \
    --i-reference-reads silva-138.1-ssu-nr99-seqs-515f-806r-uniq.qza \
    --i-reference-taxonomy silva-138.1-ssu-nr99-tax-515f-806r-derep-uniq.qza \
    --o-classifier silva-138.1-ssu-nr99-515f-806r-classifier.qza
```

------------------------------------------- Optional (可选的) -------------------------------------------

## 数据分析过程

分析前需要表明的是：扩增子数据测序方式和类型有多种，例如 pair-end 双端测序、single-end 单端测序、单一区间测序、16S 全长测序等等，以及测序公司给你返回的原始数据中包不包含引物序列等等，这都需要根据自己的测序数据来具体分析。

这里提供的分析过程是最常见的一种，双端测序、测序公司去除了引物序列并进行了质控、过程只包括从 fq 数据到 ASV table，物种注释结果以及 ASV 序列。我想后续的分析内容无非是根据自己的实验分组情况，在不同物种分类水平做差异分析。

制作样本序列信息表 (例如名为 manifest.tsv)，3 列信息分别是样本名称和两端测序数据的路径，形式如下：

```shell
sample-id       forward-absolute-filepath       reverse-absolute-filepath
SRR12466539     /root/pipe_script/qiime2/test/SRR12466539/SRR12466539_1.fastq.gz        /root/pipe_script/qiime2/test/SRR12466539/SRR12466539_2.fastq.gz
SRR12466540     /root/pipe_script/qiime2/test/SRR12466540/SRR12466540_1.fastq.gz        /root/pipe_script/qiime2/test/SRR12466540/SRR12466540_2.fastq.gz
```

将上一步数据读取

```shell
qiime tools import \
    --type 'SampleData[PairedEndSequencesWithQuality]' \
    --input-path /root/pipe_script/qiime2/test/manifest.tsv \
    --output-path result/paired-end-demux.qza \
    --input-format PairedEndFastqManifestPhred33V2
```

数据质量 summary 可视化

```shell
qiime demux summarize \
    --i-data result/paired-end-demux.qza \
    --o-visualization result/paired-end-demux-summary.qzv
```

利用 dada2 进行 denoise，我这里使用的是质控好的数据，所以几个质控参数设置的都是 0，即不对序列做处理，也就意味着用 denoise-single 和 denoise-paired 都没问题。

```shell
# --o-representative-sequences 输出的代表序列
# --o-table 输出的 ASV table
# --o-denoising-stats 输出的 denoise 统计信息
qiime dada2 denoise-single \
    --i-demultiplexed-seqs result/paired-end-demux.qza \
    --p-trim-left 0 \
    --p-trunc-len 0 \
    --p-n-threads 8 \
    --o-representative-sequences result/rep-seqs-dada2.qza \
    --o-table result/table-dada2.qza \
    --o-denoising-stats result/stats-dada2.qza
```

对 denoise 后的数据进行 summary 可视化

```shell
qiime feature-table summarize \
    --i-table result/table-dada2.qza \
    --o-visualization result/table-dada2.qzv
```

利用 bayes 方法做物种注释

```shell
# --o-classification 物种分类结果
# --i-classifier 就是上面构建好的物种分类器
qiime feature-classifier classify-sklearn \
    --i-classifier /root/database/SILVA_138.1_SSURef_NR99/silva-138.1-ssu-nr99-classifier.qza \
    --i-reads result/rep-seqs-dada2.qza \
    --o-classification result/taxonomy-dada2-sliva.qza \
    --p-n-jobs 3 \
    --verbose
```

对上述 qza 结果转换输出为 tsv，fasta 格式文件。

```shell
# 输出统计信息表
qiime tools export \
    --input-path  result/stats-dada2.qza \
    --output-path result/   # default out file: stats.tsv
# 输出代表序列
qiime tools export \
    --input-path  result/rep-seqs-dada2.qza \
    --output-path result/   # default out file: dna-sequences.fasta
# 输出biom格式的ASV table
qiime tools export \
    --input-path  result/table-dada2.qza \
    --output-path result/   # default out file: feature-table.biom
# 输出物种分类结果
qiime tools export \
    --input-path  result/taxonomy-dada2-sliva.qza \
    --output-path result/  # default out file: taxonomy.tsv
# 转换biom格式为tsv格式
biom convert -i feature-table.biom -o feature-table.tsv --to-tsv
```

经过上述步骤就得到了扩增子分析中重要的几个结果了，ASV table、ASV 序列和物种注释结果。后续的相关分析就是基于这几个结果去做。
