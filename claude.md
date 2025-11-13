这是一个论文的latex项目，我用同一个课题，该研究成果需要发一篇期刊论文和一篇毕设论文

[elsarticle-template-num.tex](elsarticle-template-num.tex)是主要的论文文档，我主要写作在这里，该文档用于发表期刊论文
[bishe.tex](bishe.tex)是我的毕业论文，我有关毕设的都在这里

[picture](picture) 存储了所有论文图片
[ref.bib](ref.bib)存储了所有引用
[mamba.pdf](mamba.pdf) mamba原文

# 毕设
该部分的提示词仅用于[bishe.tex](bishe.tex)
[bishe_ref](bishe_ref)是我上一届毕业师兄的毕业论文，我的格式要向他看齐
只需要内容向他看齐即可（比如章节安排，章节内内容）。行间隔，引用间隔，字号大小等这些不用考虑。

在参考论文中，比如我工作一，GGAM，它是包含引言、方法、消融实验三大部分的，是写在一个章节里的。最后的整体实验放在第五章了
1. 第1章 绪论 - 背景介绍
2. 第2章 相关工作 - 文献综述
3. 第3章 GGAM - 顺带介绍整体架构
4. 第4章 ASD-SSM
5. 第5章 实验 - 实验结果与分析
6. 第6章 结论 - 总结


# 代码
原代码仓库在本地的
D:\PointSS下
其中模型主要代码在
pointcept/models/point_transformer_v3/point_transformer_v3m1_base.py
用于训练的数据结构在
pointcept/models/utils/structure.py

毕设相较于小论文，新加了创新点：masa，分支在masa-implementation
小论文用的分支是feature/asd-ssm

# 格式统一
我的引用中会议或期刊必须要有姓名，且姓名格式要统一。
一定要有标题会议名或期刊名。卷名，页码，年份。页码必须是起始页到终止页。

## 标题：
统一用字段：title，注意标题要套两个括号
title = {{3D ShapeNets: A Deep Representation for Volumetric Shapes}},

## 会议名/期刊名
会议用booktitle
booktitle = {Proceedings of the IEEE International Conference on Computer Vision},
期刊用journal字段

## 作者
姓在前，名在后，名要写全名
Yu, Xumin and Tang, Lulu and Rao, Yongming and Huang, Tiejun and Zhou, Jie and Lu, Jiwen

## 页码
要是pages = {106141 - 106151},这种格式

## 不需要的字段
这几个字段出现的话需要删除，是多余字段
  - ✓ language = {English} - 0 个剩余
  - ✓ copyright = {...} - 0 个剩余
  - ✓ address = {...} - 0 个剩余
  - ✓ key = {...} - 0 个剩余
  - ✓ keywords = {...} - 0 个剩余
  - ✓ note = {...} - 0 个剩余

## 其他
对于标题和会议名/期刊名，要遵循实词首字母大写，一些特数词比如ShapeNets，看需要大写，LORA大写，
像这个LORA: LOW-RANK ADAPTATION OF LARGE LANGUAGE MODELS完全都大写的肯定是不对的

下面是我的调试命令，你不必理会：
我在ref.bib添加了@inproceedings{pcm ,的引用，参照claude.md的格式统一部分帮我修正一下格式