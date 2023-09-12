---
layout: post
title: 利用细菌全基因组测序方式对菌株安全性评价
categories: [临床数据分析]
description: 利用细菌全基因组测序方式对菌株安全性评价
keywords: 细菌基因组, 临床数据分析
---

如何利用全基因组测序方式来评估菌株的安全性？这里主要指抗生素耐药性和毒力因子，以及涉及到的可移动元件。

### 背景和前提说明

2023年2月13日，国家卫生健康委员会发布了《食品安全国家标准 食品用菌种安全性评价程序》（征求意见稿）。文件你可以点击[这个链接](files/食品安全国家标准-食品用菌种安全性评价程序.pdf)进行下载；

标准规定了食品用菌种（包括细菌、丝状真菌、酵母、放线菌及单细胞藻类）的安全性评价程序。具体评价方法包括了相关文献综述，全基因组测序，动物致病性试验，耐药性试验，产毒试验和其他的活性代谢产物。应该说评价的内容比较全面，我这里给大家介绍的内容是关于细菌的全基因组测序内容；

这份标准对全基因组测序相关的内容有具体的要求，最主要的是评估菌株基因组序列中可能存在的毒力因子和抗生素耐药性基因，方法就是尽可能的与已知的相关数据库进行比对。标准如下，虽然没有具体指明比对的软件，这3个参数可以利用常见的blast和diamond比对软件获得；
  
> 序列长度覆盖度≥60%，输入序列与数据库中序列的匹配度（≥ 85 %）和 e 值（≤1e5 ）

尽管这份标准中提到了具体的数据库，例如毒力基因（或毒素合成关键基因）数据库（包括但不限于 VFDB、PAI DB、MvirDB、CGE 等），耐药基因数据库（包括但不限于CARD、ResFinder、Argannot、NDARO 等）。但是有些数据库多年不更新，不提供基因序列或者只能利用在线比对的方式等，这些数据库就暂时不考虑。原则上就是尽可能的找到毒力和耐药性基因的参考数据库，然后利用比对软件比对，获得比对结果。

### 参考数据库说明

- 我这里一共使用到了12个数据库作为参考，7个抗生素耐药性，3个毒力基因，1个可移动元件和1个原噬菌体。

#### 抗生素耐药性数据库
1. [CARD](https://card.mcmaster.ca/home) : 抗生素耐药性
2. [Resfinder](http://genepi.food.dtu.dk/resfinder) : 抗生素耐药性
3. [NCBI_ARM](https://www.ncbi.nlm.nih.gov/bioproject/PRJNA313047) : NCBI整理的抗生素耐药性数据库
4. [Bacmet2](http://bacmet.biomedicine.gu.se/download_temporary.html) : 抗生素耐药性
5. [ARG_ANNOT](https://www.mediterranee-infection.com/acces-ressources/base-de-donnees/arg-annot-2/) : 抗生素耐药性
6. [MEGARes](https://db.meglab.org/) : 抗生素耐药性
7. [Disinfinder](https://bitbucket.org/genomicepidemiology/disinfinder_db/src/master/) : 消毒剂抗性基因

#### 毒力基因数据库
1. [VFDB](http://www.mgc.ac.cn/VFs/main.htm) : 毒力因子
2. [Virulencefinder](https://bitbucket.org/genomicepidemiology/virulencefinder_db/src/master/) : 毒力因子
3. [Ecoli_VF](https://github.com/aleimba/ecoli_VF_collection) : 来源于大肠杆菌的毒力因子

#### 可移动元件数据库
1. [mobileOG-db](https://mobileogdb.flsi.cloud.vt.edu/) : 可移动元件/转录调控因子数据库

#### 原噬菌体
1. [Phigaro](https://github.com/bobeobibo/phigaro) : 某些温和的噬菌体侵染细菌后，其核酸整合到宿主细菌染色体中的状态

### 使用到的软件
1. [Unicycler]()
2. [GTDBtk]()
3. [Prokka]()
4. [CARD-RGI]()
5. [BLAST]()
6. [DIAMOND]()
7. [Proksee]()

### 分析评估流程


### 评估后的一些说明


