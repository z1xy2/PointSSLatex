这是一个论文的latex项目

[elsarticle-template-num.tex](elsarticle-template-num.tex)是主要的论文文档，我主要写作在这里
[picture](picture) 存储了所有论文图片
[ref.bib](ref.bib)存储了所有引用
[mamba.pdf](mamba.pdf) mamba原文
# 代码
原代码仓库在本地的
D:\PointSS下
其中模型主要代码在
pointcept/models/point_transformer_v3/point_transformer_v3m1_base.py
用于训练的数据结构在
pointcept/models/utils/structure.py

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


