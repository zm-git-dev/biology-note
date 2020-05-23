# 三维基因组之TAD的形成机制以及其特征 

## Abstract

了解细胞核内染色体折叠的机制对于确定基因组结构和功能之间的关系至关重要。最近应用的“染色体构象捕获”技术揭示了许多物种的基因组被组织成频繁交互的染色质区域，称为TADs。这种染色体区域化已成为高阶基因组结构和功能的重要特征。虽然TADs现在已经被广泛的研究于各种生物体中，但它们在大小、结构和形成过程中所涉及的蛋白质方面似乎有特定的特征。在此，我们描述了这些跨物种染色质区域的主要特征，并讨论了染色质结构、基因组活性和表观基因组之间的关系，着重讲述了TAD形成的机制原理。还涉及到了TADs在基因组进化中的潜在影响。

## Introduce

A图展示了细胞核内的染色体折叠情况：染色质丝 > TAD > 区室 > 染色体疆域

+ 染色质丝：染色体折叠的最基本的层是DNA组蛋白结合水平，形成核小体进而形成染色质丝
+ TAD：由于基因调控，染色体以不同的核小体密度进行包装，并在submegabase尺度折叠成内部相互作用的高级结构
+ 区室：在染色体尺度，染色质被隔离成活跃的“A”和被抑制的的“B”区室，说明了具有相同表观遗传特征的染色质区域会更多的进行交互
+ 染色体疆域：每个染色体在细胞核内占据自己位置，形成染色体疆域

B图展示了不同染色体水平的Hi-C热图，反映了不同层次的高阶染色体折叠，两个坐标轴上都表示基因组坐标，区域间的交互频率用颜色深浅表示

+ 在submegabase尺度，TAD沿着正方形的对角线分布，相互作用十分频繁，同时TAD存在边界而且TAD被 contact depletion zones 分割开
+ 在染色体尺度上，染色质的远距离的交互形成图中具有典型格子图案的相互排斥的A、B区室
+ 与染色体间的交互相比，染色体内的交互频率会更高，这与染色体疆域的形成是相互一致的

<img src="https://s1.ax1x.com/2020/03/31/GlV5As.png" alt="Fig. 1" style="zoom:67%;" />

## TADs Across Species

在这个板块，作者主要讲述了TAD在不同的物种中的特征以及形成机制

### TADs in mammals 

在哺乳动物中，TAD的特征十分的保守，特征主要有以下几条

+ 大多数哺乳动物TAD边界的一个显著特征是存在 CCCTC-binding factor (CTCF)  以及structural maintenance of chromosomes (SMC) cohesin complex， loop-anchored TADs 总是出现CTCF之间，并且位于一个收敛的方向，并且改变单个CTCF的方向会使TAD边界的位置消失或移动
+ 针对这些TADs的形成，提出了一种称为“loop extrusion model“的模型，这个模型指出loop-anchored TADs 的形成与CTCF/cohesin有关，CTCF/cohesin来自于黏连蛋白复合物的出现和移除之间的平衡，这是一个动态的过程。另一个SMC蛋白复合物富集在TAD边界。
+ 在缺乏cohesin或TADs通过自发的染色质接触形成的情况下，可能存在其他的TAD形成机制
+ CTCF与增强子-启动子的交互有关；transcription-associated supercoiling 和 type II DNA topoisomerase都与染色体的拓扑结构有关

<img src="https://s1.ax1x.com/2020/03/31/GlVohq.png" alt="mammals" style="zoom:80%;" />

+ 图中黑色的圆圈表明复合物再TAD边界的停留时间增加

### TADs in Drosophila

+ 果蝇TADs表现出与表观遗传有很好的相关性，并根据其具体的表观遗传特征分为四个主要类别(active TADs、PcG TADs、 null or void TADs、 heterochromatin TADs}

+ RNAPII和转录过程与果蝇TAD的形成有关，但并不完全决定其形成
+ Zelda（一种转录因子）在TAD边界处起到绝缘子的作用，它可能与TAD边界处的其他调控因子合作，如 BEAF-32 and GAGA factor ，但在脊椎动物中没有发现其同系物，这也许是果蝇特有的机制

<img src="https://s1.ax1x.com/2020/03/31/GlVfBQ.png" alt="Drosophila"  />

### TADs in Caenorhabditis elegans 

+ X染色体上存在约1Mb大小的self-interacting domains ，但常染色体没有这个明显特征
+ 雌雄同体的X染色体被 dosage compensation complex (DCC) 特异性结合，（DCC是一种凝缩蛋白复合物）

### TADs in plants

+ 拟南芥中没有明显的的TAD，但拟南芥基因组包含了与H3K27me3或H3K9me2等受抑制的染色质标记的相互作用的紧密区域；在水稻和棉花中可以鉴定出明显的TAD
+ 玉米、番茄、高粱、粟、水稻中发现了一种类似于果蝇的 TAD-like domains ，也可以根据它的表观遗传特征分成四种染色质类型
+ 在水稻TAD边界上发现了DNA GC-rich motifs，类似于那些属于TCP家族的被植物特异性转录因子结合的序列

![ plants](https://s1.ax1x.com/2020/03/31/GlVh7j.png)

### Self-interacting domains in yeast

+ 在裂殖酵母中发现了叫一种做”globules“ 的 Self-interacting domains，
+ 最近，用了一个类似于Hi-C的Micro-C技术揭示了 small self-interacting domains的存在

### TAD-like domains in bacteria 

+  新月柄杆菌中存在分立的chromosomal interaction domains (CIDs) ，类似于真核生物的TAD
+  研究表明，富含plectonemes的区域形成CIDs，而边界是由高表达基因和无plectonemes区域的形成而形成的。
+  研究发现SMC环能够围绕DNA和染色体臂，形成依赖于复合物中腺苷三磷酸酶(ATPase)活性的 processive loops 

![bacteria ](https://s1.ax1x.com/2020/03/31/GlVWng.png)

### General and specific features of TADs 

+ 虽然TADs或相互作用域作为基因组组织的一个基本组成部分出现，但它们的特征并不是普遍保守的。 在不同的物种中，交互域的显著程度、边界的清晰度都存在差异。此外，TAD形成的分子机制可能是多种多样的，这与不同类型TADs的存在是一致的。因此，尚不清楚TADs是高阶基因组的普遍单位，还是在进化过程中由于作用于染色质的不同分子引擎的相互作用而反复出现。然而，在不同物种都保守的一个显著特征是基因活性和基因组折叠之间的关系。边界的染色质活性相对较高
+ CTCF在脊椎动物的特性较为保守，然而，在其他生物如植物、酵母和秀丽隐杆线虫中没有发现CTCF
+ 然而，绝缘体蛋白也可能在其他物种转录区域的TAD边界的定义中发挥类似的作用，如果蝇中的BEAF-32和CP190，植物中的TCP蛋白

## Physical Nature Of TADs

### TADs and compartments

下图展示了在果蝇和哺乳动物中的染色体折叠情况

A图（果蝇）

+ ①TAD和区室都与其表观遗传区域相对应，其优先发生自身折叠并和同类型的TAD发生远程顺式交互
+ ②大的受抑制的染色质区域形成显著的TAD
+ ③这些大的受抑制的区域被这些转录基因分隔开，这些转录基因形成小的active TAD 或者 inter-TAD–like regions 

B图（哺乳动物）

+ ①在哺乳动物中，为TAD的形成提出了一种“loop extrusion model”，里边包含了一种 loop extrusion factor，被Nipbl和Wapl装载和释放，黏连蛋白挤压染色质使其分离、与另一个黏连蛋白发生碰撞、通过CTCF蛋白反向或者其他边界上的原件来到达TAD的边界。这些loop在TAD的边界之间的交互是被看作是一个strong peak
+ ②在活跃的转录起始位点发现了绝缘子的存在
+ ③最新研究发现，loop挤压过程会与本身活跃与不活跃的染色质之间的隔离相互竞争

<img src="https://s1.ax1x.com/2020/04/08/GWHh8K.png" alt="compartment" style="zoom:67%;" />

### TAD structure and dynamics

+ 显微镜技术、聚合物模型和Single-cell Hi-C (scHi-C) 都表明哺乳动物的TADs可以呈现各种各样的构象，从浓缩的球状物体到更延伸的结构
+ TADs的边界精度和绝缘程度在不同的细胞类型和不同的细胞周期期都不同
+ “stirring model” 表明增强子和启动子的相互作用是动态的。转录本身可能稳定近端染色质构象，减少增强子和启动子之间的距离
+ 因此，TADs可能建立一个局部的染色体环境，调控信号会调节远端的增强子和启动子之间的动态相互作用的频率

## TADs And Genome Evolution 

+ TAD可能作为缓冲因子，允许突变发挥局部影响，而不影响周围的的其他TAD位点
+ TAD在大小上表现出灵活可变性，可以允许DNA序列的增加和缺失，这有利于新的调控效应的出现
+ 这些三维结构有利于维持内部各种元件的选择压力，进而有利于控制某些特定的位点
+ CTCF binding 和染色质的结构趋于共同进化
+ 在斑马鱼和其他脊椎动物中，位于相同TAD的基因对比临近TAD的基因对在同源性上更近一些，
+ TAD的边界存在局部开放的染色质结构，边界一旦被扰动，会导致异常的交互和基因的错误表达，并通常会带来有害的影响，这为TAD在进化中保持完整性提供了基础
+ 基因组的复制有时候会导致新的TAD出现，这些新的TAD有时候可以解释病理，但有时候它没有表型
+ 在脊椎动物中，一个新的TAD的出现可能导致新的顺势调控元件的插入

## Conclusion And Perspective 

染色体的折叠情况是一个跨物种保守的特征，为什么CTCF在不同物种之间的出现与消失仍然是一个有待解决的问题，在存在CTCF的物种中，CTCF可以用来定义TAD的边界，但是其他物种不存在CTCF表明TAD是一个可以脱离CTCF而存在的一个更基本的染色质结构，并且在这些物种中转录基因的分布和绝缘子的出现和分布为交互模型的构建提供了框架。

## 参考

+ Principles of genome folding into topologically associating domains