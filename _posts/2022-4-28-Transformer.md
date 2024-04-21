---
title:      Transformer              # 标题 
date:       2022-04-28              # 时间
author:     vhwz                      # 作者
math: true
categories: [Algorithm, DeepLearning]
---

### transformer

由 google 论文 Attention is all you need 提出， 用于NLP任务

### vision trdansformer

由 An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale提出  
将 transformer用于计算机视觉任务

![image-20220811154027527](/assets/img/old_blog/image-20220811154027527.png)

首先将图像分割为N个patch，每个patch 尺寸为(p, p, 3)，将其展开为向量，尺寸变为(1, p\*p\*3)

利用一个FC层将尺寸映射为(1, D),这一步骤称为patch embedding。

patch embedding之后得到了N个（1，D）向量，再额外加入一个（1，D）的向量用于后续的分类。

接下来给每个向量与位置编码相加，位置编码是可学习的，尺寸也是（1，D），这一步骤称为position embedding。

接下来将得到的（N+1, D) 向量输入transformer encoder.

transformer encoder是由多个基本单元堆叠而成的，每个单元包含以下步骤：

layer norm，如下公式所示，首先计算输入向量的整体均值和方差，然后利用算得的均值和方差对输入向量进行归一化，再乘以gamma，加上beta。其中gamma和beta是可学习的参数
$$
\mathrm{y}=\frac{\mathrm{x}-\mathrm{E}[\mathrm{x}]}{\sqrt{\operatorname{Var}[\mathrm{x}]+\epsilon}} * \gamma+\beta
$$
layer norm 的结果还是（N+1 ,D）的向量，结果向量被送入多头自注意力模块

在自注意力模块中，有三个无bias的FC层，或者称为三个矩阵，它们中的参数是可学习的,尺寸为 (D, D）

这三个矩阵称为Wq, Wk, Wv.它们与输入向量(记为X）做矩阵乘法，得到q, k, v

q = XWq, k = XWk, v = XWv

结果q, k, v尺寸为(N+1, D)

计算 q*k.T, 得到尺寸为(N+1, N+1)

结果除以sqrt(D)，进行一个缩放，再经过softmax（对每一行进行）

得到的结果与v进行矩阵乘法，得到结果尺寸为(N+1, D)

接下来再进过layer norm，然后

MLP是一个两层Fc，将输入进行映射（N+1, D) - > (N+1, hidden_D) - > (N+1, D)



vision transformer的特点：没有卷积的局部性和平移不变性这两个归纳偏置

### swin transformer

由Swin Transformer: Hierarchical Vision Transformer using Shifted Windows提出

在视觉领域大部分数据集上取得了最好的结果

整体架构：

![image-20220811200412824](/assets/img/old_blog/image-20220811200412824.png)

要点：

1.patch merge

![image-20220811191423830](/assets/img/old_blog/image-20220811191423830.png)

2.在局部窗口内进行自注意力计算，在移动窗口内进行自注意力计算

![image-20220811194829964](/assets/img/old_blog/image-20220811194829964.png)

3.循环位移：提高计算效率