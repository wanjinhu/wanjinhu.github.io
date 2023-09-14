---
layout: post
title: 利用细菌全基因组测序方式对菌株安全性评价
categories: [临床数据分析]
description: 利用细菌全基因组测序方式对菌株安全性评价
keywords: 细菌基因组, 临床数据分析
---

如何利用全基因组测序方式来评估菌株的安全性？这里主要指抗生素耐药性和毒力因子，以及涉及到的可移动元件。

## 背景和前提说明
2023年2月13日，国家卫生健康委员会发布了《食品安全国家标准 食品用菌种安全性评价程序》（征求意见稿）。文件你可以点击 [这个链接](images/blog/食品安全国家标准-食品用菌种安全性评价程序.pdf) 进行下载。

标准规定了食品用菌种（包括细菌、丝状真菌、酵母、放线菌及单细胞藻类）的安全性评价程序。具体评价方法包括了相关文献综述，全基因组测序，动物致病性试验，耐药性试验，产毒试验和其他的活性代谢产物试验等。应该说评价的内容比较全面，我这里给大家介绍的内容是关于细菌的全基因组测序内容。

这份标准对全基因组测序相关的内容有具体的要求，最主要的是评估菌株基因组序列中可能存在的毒力因子和抗生素耐药性基因，方法就是尽可能的与已知的相关数据库进行比对。标准如下：
  
> 序列长度覆盖度≥60%，输入序列与数据库中序列的匹配度（≥ 85 %）和 e 值（≤1e5 ）

文件中虽然没有具体指明比对的软件，上面这3个参数可以利用常见的blast和diamond比对软件获得。

这份标准中提到了具体的数据库，例如毒力基因（或毒素合成关键基因）数据库（包括但不限于 VFDB、PAI DB、MvirDB、CGE 等），耐药基因数据库（包括但不限于CARD、ResFinder、Argannot、NDARO 等）。但是有些数据库多年不更新，不提供基因序列或者只能利用在线比对的方式等，这些数据库就暂时不考虑。原则上就是尽可能的找到毒力和耐药性基因的参考数据库，然后利用比对软件比对，获得比对结果。

## 参考数据库说明
我这里一共找到12个数据库作为参考，7个抗生素耐药性，3个毒力基因，1个可移动元件和1个原噬菌体。

相同类型数据库中有的基因是重复的，在实际的分析中也体现出来了。同一个基因在不同数据库中记录的信息详细度也是有区别的，但依旧是同一个基因。

实际分析后，比较有效的数据库，抗生素耐药性基因数据库以CARD和MEGARes为代表，毒力因子数据库以VFDB为代表。以下是具体的数据库信息，

### 抗生素耐药性数据库
1. [CARD](https://card.mcmaster.ca/home) : 抗生素耐药性
2. [Resfinder](http://genepi.food.dtu.dk/resfinder) : 抗生素耐药性
3. [NCBI_ARM](https://www.ncbi.nlm.nih.gov/bioproject/PRJNA313047) : NCBI整理的抗生素耐药性数据库
4. [Bacmet2](http://bacmet.biomedicine.gu.se/download_temporary.html) : 抗生素耐药性
5. [ARG_ANNOT](https://www.mediterranee-infection.com/acces-ressources/base-de-donnees/arg-annot-2/) : 抗生素耐药性
6. [MEGARes](https://db.meglab.org/) : 抗生素耐药性
7. [Disinfinder](https://bitbucket.org/genomicepidemiology/disinfinder_db/src/master/) : 消毒剂抗性基因

### 毒力基因数据库
1. [VFDB](http://www.mgc.ac.cn/VFs/main.htm) : 毒力因子
2. [Virulencefinder](https://bitbucket.org/genomicepidemiology/virulencefinder_db/src/master/) : 毒力因子
3. [Ecoli_VF](https://github.com/aleimba/ecoli_VF_collection) : 来源于大肠杆菌的毒力因子

### 可移动元件数据库
1. [mobileOG-db](https://mobileogdb.flsi.cloud.vt.edu/) : 可移动元件/转录调控因子数据库

### 原噬菌体
1. [Phigaro](https://github.com/bobeobibo/phigaro) : 某些温和的噬菌体侵染细菌后，其核酸整合到宿主细菌染色体中的状态

## 使用到的软件
1. [Unicycler](https://github.com/rrwick/Unicycler) : 是我用过**细菌基因组**组装软件中最好的，不论是只有二代测序的基因组扫描图，还是二代+三代测序的基因组完成图。我之前测试过十几款细菌基因组组装软件，可以点击 [Microbial-genome](https://github.com/wanjinhu/Microbial-genome) 查看相关信息。
2. [GTDBtk](https://ecogenomics.github.io/GTDBTk/index.html) : 是根据基因组分类数据库，用来对细菌基因组鉴定物种信息的，我认为是目前基于基因组鉴定物种最好的方法，适用于单菌和宏基因组MAG水平。
3. [PGAP](https://github.com/ncbi/pgap) : 是NCBI提供的原核生物基因组注释pipeline，比如Bb-12菌株在NCBI上的信息，可以点击 [这个链接](https://www.ncbi.nlm.nih.gov/datasets/genome/GCF_000025245.2/) 查看，PGAP的注释结果可以作为参考。
4. [Prokka](https://github.com/tseemann/prokka) : 可以用来快速注释细菌、古菌和病毒基因组，并且可以生成符合标准的输出文件（比如gff3和gbk等）。
5. [CARD-RGI](https://github.com/arpcard/rgi) : 根据同源性（homology）和SNP模型从蛋白质或者核苷酸数据中预测抗生素抗性基因，是利用CARD作为参考数据库的。
6. [BLAST](https://blast.ncbi.nlm.nih.gov/Blast.cgi?CMD=Web&PAGE_TYPE=BlastHomeNew) : 对于只提供了核苷酸的数据库，利用BLAST来比对。
7. [DIAMOND](https://github.com/bbuchfink/diamond) : 提供了蛋白的数据库，利用DIAMOND来比对。
8. [Proksee](https://proksee.ca/) : 基因组分析集合的在线分析网站。这里我用它来绘制基因组圈图，其实网站本身提供了丰富的基因组分析的方法，一般的分析都可以直接进行。

## 分析评估流程
整个分析流程并不复杂，主要是前期对数据库的整理，特别是对数据库的注释文件的检查和包含注释的信息的熟悉，选择你需要的信息。

有整合在一起的pipeline包含了组装、基因预测和注释的功能，比如Bactopia，操作方便，只是功能都是固定好的。当然你也可以直接用其基因预测的结果，因为该pipeline使用到的软件也是Prokka.

我把具体的操作分析流程分步展示如下：

### 1. 序列组装
#### 完成图：二代+三代测序数据
```shell
# -1，-2 表示二代短读长测序的双端测序数据，-l 表示三代长读长测序数据，-o 表示输出文件夹
unicycler -1 test_1.fq.gz -2 test_2.fq.gz -l test_long.fq.gz -o Assembly -t 16
```
#### 扫描图：二代测序数据
```shell
unicycler -1 test_1.fq.gz -2 test_2.fq.gz -o Assembly -t 16
``` 
#### 主要输出结果说明：
- `Assembly/assembly.fasta` 组装结果的fasta格式，下一步分析使用的
- `Assembly/assembly.gfa` 组装结果的gfa格式，特别是对于扫描图基因组来说，多个contig的连接关系可以很清晰地看到，利用 [Bandage](https://github.com/rrwick/Bandage) 可以看到
- `Assembly/unicycler.log` 组装过程日志，你可以看到unicycler在组装过程中到底做了哪些事情

### 2. 基因组物种分类鉴定
#### GTDBtk
```shell
# --genome_dir 基因组存放的文件夹，--out_dir 指定输出文件夹，--prefix 指定输出文件的前缀，--extension 基因组的后缀
gtdbtk classify_wf --genome_dir Assembly --out_dir GTDB_result --cpus 16 --prefix test --extension fasta
```

#### GTDBtk主要输出结果说明：
- `GTDB_result/test.bac120.summary.tsv` 物种分类结果, 包括物种分类信息，与参考基因组的ANI相似度等具体信息

#### PGAP
```shell
# -o 表示输出文件夹，-g 表示输入的基因组，-s 表示提供的物种信息，是程序运行的必须信息，你可以利用GTDBtk运行得到的分类信息，使用的taxcheck参数会检查你提供的物种信息，如果不一致会自动纠正，--no-internet 表示不使用互联网，--no-self-update 表示不更新，--taxcheck 表示检查物种分类信息，会得到checkM的分析结果，--auto-correct-tax 表示自动纠正物种分类信息，--ignore-all-errors 表示忽略运行中的其他错误
pgap.py -r -o PGAP_result -g Assembly/assembly.fasta -s 'Bifidobacterium longum' --no-internet --no-self-update --taxcheck --auto-correct-tax --ignore-all-errors
```

#### PGAP主要输出结果说明：
- 你可以点击 [这个链接](https://github.com/ncbi/pgap/wiki/Output-Files) 来查看输出结果文件夹下每个文件的含义
- 最重要的文件的结果是：PGAP_result/ani-tax-report.txt，是物种检查的结果，点击 [这个链接](https://github.com/ncbi/pgap/wiki/Taxonomy-Check) 查看详细说明

### 3. 基因预测
```shell
# --outdir 输出文件夹，--prefix 输出文件的前缀，--kingdom 物种分类信息，--rfam 是否使用rfam数据库
prokka --outdir Prokka --force --prefix test --kingdom Bacteria --rfam --cpus 16 Assembly/assembly.fasta
```

#### Prokka输出结果说明：
- prokka输出的就是基因的相关信息，比如核苷酸序列，CDS的蛋白序列，gff/gbk等基因注释文件，以及对基因统计的表格。具体的说明，点击 [这个链接](https://github.com/tseemann/prokka#output-files) 来查看
- `Prokka/prokka.faa` CDS的蛋白序列
- `Prokka/prokka.ffn` 核苷酸序列

### 4. 基因注释
这里的基因注释指的是用抗生素耐药性基因数据库、毒力因子数据库和可移动元件的注释。对于rRNA、tRNA以及其他基因的注释，Prokka已经分析了，其底层利用的数据库是UniProt, Pfam和TIGRFAMs。当然Prokka还会预测到很多假定蛋白，这些基因可能是某些功能基因，注释需要用其他数据库来注释。

#### CARD-RGI抗生素耐药性基因注释
CARD抗生素耐药性基因注释通过RGI的方式去分析，而不是直接利用BLAST和DIAMOND的方式去分析。
```shell
# --input_sequence 输入的基因序列，--output_file 输出文件的前缀，--local 本地运行 -t 基因类型
rgi main --input_sequence Prokka/prokka.faa --output_file Card_result --local --clean -t protein
```
#### DIAMOND比对
如果数据库提供了蛋白序列，则利用diamond比对的方式去做，比blastp的方式要快一些。这里以VFDB毒力因子数据库为例，
```shell
# 数据库构建diamond的索引，--in 输入的需要构建索引的蛋白序列，--db 输出的索引
diamond makedb --in VFDB_setA_pro.fas --db VFDB_setA_pro
# 利用diamond blastp去比对，--db 数据库索引，--query 待比对的蛋白序列，--out 比对输出结果，--evalue 比对evalue阈值，--max-target-seqs 最大比对目标序列数，--outfmt 输出格式
diamond blastp --db VFDB_setA_pro.dmnd --query Prokka/prokka.faa --out vf_anno.txt --evalue 0.00001 --max-target-seqs 1 --outfmt 6 qseqid sseqid pident length qcovhsp mismatch gapopen qstart qend sstart send evalue bitscore
```

#### BLAST比对
有的数据库只提供了核苷酸序列，则用blastn比对的方式去做。这里以MEGARes耐药基因数据库为例，
```shell
# 数据库构建索引，-dbtype 数据库类型(nucl还是prot)，-in 输入的序列，-input_type 序列类型，-out 输出的索引
makeblastdb -dbtype nucl -in megares_database_v3.00.fasta -input_type fasta -out megares
# blastn比对，-query 待比对的核苷酸序列，-db 数据库索引，-out 比对输出结果，-evalue 比对evalue阈值，-max_target_seqs 最大比对目标序列数，-max_hsp 比对最大的hsp序列数，-outfmt 输出格式
blastn -query Prokka/prokka.ffn -db megares -out megares_anno.txt -evalue 0.00001 -max_target_seqs 1 -max_hsps 1 -outfmt '6 qseqid sseqid pident length qcovhsp mismatch gapopen qstart qend sstart send evalue bitscore'
```

### 5. 后续其他分析
可移动元件分析，也是利用预测得到的基因和可移动元件数据库mobileOG-db进行比对的方式获得的，为了确定抗生素耐药性基因是不是由于水平基因转移的方式获得的，只需要看下抗生素耐药性基因编号和可移动元件的编号是不是对应的即可。具体的你可以参考[这篇文章](https://www.nature.com/articles/s41467-022-29096-9)。

做完基因注释就得到了主要的分析结果了。为了后续便于查询和可视化的操作，后续我利用Proksee来绘制基因组圈图，并用Rmd的方式得到一个html形式的报告。
