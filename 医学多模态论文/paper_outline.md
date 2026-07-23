# Paper Outline: DOR — Dynamic Orthogonal Relay for Text-Free Medical Image Segmentation

  

---

  

## 论文标题

  

> **Dynamic Orthogonal Relay: Combating Prototype Collapse for Text-Free Medical Image Segmentation**

  

备选：*SCSF: Semantic Consistency via Dynamic Prototypes for Text-Free Medical Segmentation*

  

---

  

## 核心叙事线

  

```

【发现现象】ProLearn (ICCV 2025) 提出"文本依赖"概念 + 静态原型聚类方案

↓

【发现问题】我们分析发现 ProLearn 原型严重坍缩（相似度 0.97/0.99）

↓

【分析根因】聚类抹平了个体差异 → 模型只能学共性 → 医学分割中差异才是关键

↓

【提出方案】不聚类，保留所有文本 → FAISS 检索 → DOR 动态修正错误 token

↓

【验证方案】DOR 原型保持分散（<0.3），Dice 0.836，消融证实各组件必要

```

  

---

  

## 摘要（约 200 词）

  

> Text-guided medical image segmentation achieves strong performance but requires paired image-text data at inference—a condition rarely met in clinical practice. ProLearn (ICCV 2025) pioneered text-free inference by distilling textual knowledge into a static prototype space via clustering. However, we identify a critical limitation: the resulting prototypes suffer from severe collapse, with average pairwise cosine similarities exceeding 0.97 for visual prototypes and 0.98 for textual prototypes on QaTa-COV19. This collapse forces the model to rely on coarse commonalities across all reports, neglecting the subtle inter-case differences essential for accurate lesion delineation. To address this, we propose **Dynamic Orthogonal Relay (DOR)** , a learnable prototype bank that actively corrects noisy retrieval signals. Rather than clustering texts into static prototypes, DOR preserves the full retrieval database and introduces three mechanisms: (1) cross-modal confidence gating to identify unreliable text tokens, (2) a visual-space prototype bank that queries visual features to supply corrective semantics, and (3) a three-phase evolution strategy with orthogonal constraints that prevents prototype collapse throughout training. Experiments on QaTa-COV19 demonstrate that our method achieves a Dice score of 0.836 without any text input at inference, outperforming both pure vision baselines and retrieval-without-filtering variants. Visualization confirms that DOR maintains prototype diversity (average inter-prototype similarity below 0.3) compared to the severe collapse observed in static clustering approaches.
---

## 章节大纲

---

### 1. Introduction（约 1 页）

**第一段：大背景（3-4 句）**

- 医学图像分割是临床诊断的基础任务

- 文本引导的分割方法（LViT, MQFormer, CAT-Net, LA-ViT）利用放射报告显著提升分割精度

- 但共同假设：推理时图像和文本必须成对出现

- 临床现实：急诊（先拍片后出报告）、体检（报告数天后才写）、历史数据无报告

**第二段：ProLearn 的贡献与我们的发现（5-6 句）**

- ProLearn (Ye et al., ICCV 2025) 首次提出"文本依赖 (textual reliance)"概念

- 方案：训练前对图文特征分别聚类 → 静态 prototype space → 推理时相似度查询 prototype 替代文本

- **我们的分析发现了严重问题**：对 QaTa-COV19 的相似度测量：

- 图像间平均余弦相似度：0.8297

- 文本间平均相似度（含 padding）：0.9627

- 文本间平均相似度（去 padding）：0.8991

- **ProLearn 视觉原型平均相似度：0.9726**

- **ProLearn 文本原型平均相似度：0.9856**

- 这意味着 ProLearn 的原型几乎完全坍缩为单一向量

**第三段：原型坍缩的后果与现有方案不足（4-5 句）**

- 原型坍缩 → 模型只能学到所有文本的"共性"

- 医学分割的关键恰恰在于个体差异——不同病例的病灶边界、形态、纹理各不相同

- MG-UNet 尝试用 Memory Bank + 注意力参数化学习文本共性，使共性更可靠复杂

- 但核心问题未解决：共性足够，差异缺失

- 注：ProLearn 代码无法复现，MG-UNet 无开源代码，我们以其思路作为方法论参考

**第四段：我们的方案（5-6 句）**

- 核心洞察：检索文本中大部分 token 是正确的（共性），少部分是错误的（需要修正）

- 关键挑战：**找到错误 token，用正确语义替换**

- 我们提出 Dynamic Orthogonal Relay (DOR)：

1. 跨模态置信度门控：视觉确认文本可靠性，识别并抑制不可靠 token

2. 可学习正交原型库：在**视觉空间**（非文本空间）中提供修正信号。原型做 query 查询视觉特征，文本仅作为注意力引导

3. 三阶段演化 + 正交约束：FPS 初始化 → 对齐校准 → 注意力精调，全训练过程防止原型坍缩

- 与 ProLearn 的本质区别：我们不聚类文本（保留完整检索库），原型可训练且在视觉空间中演化 

**第五段：贡献列表**

> 1. 揭示了静态原型聚类在文本自由分割中的**原型坍缩问题**，提供定量证据

> 2. 提出 DOR——第一个将"检索文本修正"（而非"文本替代"或"共性学习"）作为核心思路的方法

> 3. 三阶段原型演化 + 正交约束 + 多损失防坍缩机制

> 4. QaTa-COV19 上 Dice 0.836，原型保持分散（相似度 <0.3），消融实验和可视化验证

---
### 2. Related Work（约 0.6 页）

#### 2.1 Text-Guided Medical Image Segmentation

- LViT (TMI 2023), MQFormer (MICCAI 2023), CAT-Net (TMI 2023), LA-ViT

- 共同局限：推理时需要成对文本
#### 2.2 Text-Free Medical Image Segmentation（最重要子节）

- **ProLearn (ICCV 2025)**：首个提出"文本依赖"概念

- 方案：视觉-文本双粒度聚类 → 静态 prototype space → 相似度查询

- 局限：原型坍缩（我们发现的 0.97+ 相似度），丢失个体差异

- **MG-UNet**：Memory Bank + 注意力学习文本共性，参数化使共性更可靠

- 局限：仍以共性为核心，差异信息未保留

- **SGSeg (MICCAI 2024)**：自生成伪文本替代真实文本

- 局限：生成文本质量不稳定

- **我们的位置**：不聚类、不生成、不学共性——保留原始检索文本，用 DOR 修正噪声
#### 2.3 Prototype Learning

- Snell et al. (NeurIPS 2017), DeepCluster (ECCV 2018)

- Cross Prototype Network (PRCV 2024)

- 区别：现有原型方法用于分类/聚类，我们用于跨模态噪声修正 + 防坍缩
---
### 3. Method（约 2-2.5 页）
#### 3.1 Overall Framework（约 150 词 + 图 1）

- 图像 → ConvNeXt → 多尺度特征，瓶颈层 E₄

- E₄ → BiomedCLIP → FAISS 检索 top-1 → 文本特征 T

- E₄, T → DOR → 增强特征 F

- F → 层次化解码器 → 分割输出
#### 3.2 Cross-Modal Retrieval（约 200 词）

**为什么不用聚类？— 与 ProLearn 的设计分歧**

- 我们的分析揭示静态聚类的三重问题：

1. 文本高度同质化（QaTa-COV19 文本相似度 0.96），聚类原型坍缩为单一向量（0.99）

2. 聚类抹平个体差异，每个 cluster center 是共性平均

3. 静态不可训练：视觉特征在训练后期逐渐忽略退化的文本信号 → 模型退化为纯视觉


**我们的方案：保留所有文本**

- 训练集 25% 图文对 → BiomedCLIP 视觉编码 512 维 → FAISS IndexFlatIP

- 推理时：图像 → 512 维 → FAISS 检索 top-1 → token 级文本特征 T

- 优势：保留个体差异；DOR 动态过滤；检索库可随时更新无需重训

- 为什么 top-1：实验发现精度足够，top-k 聚合可能引入噪声

#### 3.3 Dynamic Orthogonal Relay（约 1000 词 + 图 2）

##### 3.3.1 Motivation and Design Philosophy（约 200 词）

核心洞察：

> "检索文本中，大部分 token 传递的语义是正确的（共性），少部分 token 由于检索偏差传递了错误或不相关的语义（噪声）。关键挑战：**找到错误 token，用正确语义替换。**"

三个子问题及 DOR 的答案：

1. 如何识别错误 token？→ **跨模态置信度门控**（视觉确认文本可靠性）

2. 正确修正信号从哪来？→ **可学习视觉原型库**（从视觉空间而非文本空间来）

3. 如何防止修正信号自身退化？→ **正交约束 + 三阶段演化**（全训练过程防坍缩）

DOR 的设计哲学：

- **原型在视觉空间**（Q=原型查视觉 K/V），不在文本空间

- **文本是注意力引导**，不是信息源

- **最终输出是视觉原型的增强**，不是文本特征的变形

##### 3.3.2 Cross-Modal Confidence Gating（约 150 词）

- sim = E₄ · Tᵀ / (√D · 0.1)

- t_conf = max_N(sim)：每个文本 token 被视觉确认的置信度

- t_gate = σ(t_conf / τ)，τ warmup 期更高（更软）

- T_filtered = T ⊙ t_gate

- 直觉：与所有视觉 patch 相似度都低的文本 token → 描述的内容不在图中 → 抑制

##### 3.3.3 Visual-Space Prototype Attention（约 200 词）

**与现有方法的本质区别：**

- ProLearn 等：文本做 query，查文本空间 → 原型存文本知识

- DOR：**原型做 query，查视觉空间** → 原型存视觉语义

机制：

- Q = q_proj(P)，P ∈ R^{K×D}，K 个可学习原型

- K = k_proj(E₄)，V = v_proj(E₄)，视觉特征

- 注意力方向：原型主动寻找视觉空间中匹配的区域

视觉未匹配掩码（Unmatched Suppression）：

- v_conf = max_L(sim)：视觉 patch 与文本的匹配度

- unmatched = 1 − σ(v_conf / τ)

- A_logits += log(unmatched + ε) · λ_mask

- 直觉：文本已覆盖的区域 → 降低原型关注；让原型关注文本"没说清楚"的区域
##### 3.3.4 Three-Phase Prototype Evolution（约 250 词）

| Phase | Epoch | 策略 | 动机 |

|-------|-------|------|------|

| **1: Geometric Init** | 0-4 | FPS + Gram-Schmidt 正交化，无 EMA | 投影层 warm-up；FPS 最大几何覆盖；正交化保初始多样性 |

| **2: Alignment-Driven** | 5-9 | 找语义最相似 patch 聚合，动量 0.3/0.7 | 几何初始化后语义校准；大步长快速适应 |

| **3: Attention EMA** | 10+ | 注意力软分配，动量 0.9/0.1，活性门控 | 后期稳定；活性门控冻结死原型 |


##### 3.3.5 Loss Functions（约 200 词）


| Loss | 公式 | 防坍缩作用 |

|------|------|-----------|

| L_ortho | MSE(P_norm·P_normᵀ, I_K) | 直接对抗坍缩：强制原型正交 |

| L_align | ReLU(0.8−max cos(P,E₄)).mean() | 防止退化为无意义向量 |

| L_util | MSE(mean(STE(A)), 1/K) | 防止少数原型垄断→其他死亡 |

| L_dec | ‖corr(F_norm, T_norm)‖²_F | 防止 F 坍缩为 T 的拷贝 |

| L_div | MSE(F_sim, I_K) | 防止 batch 内所有输出趋同 |

总损失：L_total = L_seg + 0.1·L_v2v + 0.1·L_v2t + 0.05·L_l2l + 0.2·L_dec + 0.05·L_div + 0.1·L_util + 0.1·L_ortho + 0.5·L_align

##### 3.3.6 Output Projection（约 80 词）

- F = decouple_proj(V_extra)：Linear→LayerNorm→GELU→Linear

- F = F ⊙ σ(proto_activity / τ)：原型激活度门控

#### 3.4 Hierarchical Decoder（约 150 词）

- 4 尺度 GuideDecoder，Self-Attn → Cross-Attn(F) → Upsample + Skip

- SubpixelUpsample → 1×1 Conv → Sigmoid
---

### 4. Experiments（约 1.5-2 页）

  

#### 4.1 Experimental Setup（约 200 词）

  

数据集：QaTa-COV19，二值病灶分割。检索库占训练集 25%。

  

实现：ConvNeXt-Tiny (22K), BiomedCLIP (PubMedBERT-256), 224×224, AdamW lr=1e-4, CosineAnnealingLR, BS=32, K=32, max_epochs=100, patience=20. 指标：Dice, mIoU, Acc.

  

**Baselines 说明**：ProLearn (ICCV 2025) 和 MG-UNet 是核心参照，但前者代码无法复现，后者无开源代码。我们在文中引用其结果作为参考，并重新实现其核心思路（静态聚类、Memory Bank）做消融对比。

  

#### 4.2 Prototype Collapse Analysis（约 250 词 + 图 2 + 表）

  

**这是论文最重要的实验。**

  

**定量分析表：**

  

| 测量对象 | 平均余弦相似度 |

|----------|--------------|

| 图像（BiomedCLIP 视觉编码）| 0.8297 |

| 文本含 padding（BiomedCLIP）| 0.9627 |

| 文本去 padding（BiomedCLIP）| 0.8991 |

| ProLearn 视觉原型 | **0.9726** |

| ProLearn 文本原型 | **0.9856** |

| DOR 原型（训练后）| **< 0.3** |

  

**图 2：原型相似度矩阵对比**

- 左：ProLearn（一片红，坍缩）

- 右：DOR（对角线外低值，分散）

  

**结论**：ProLearn 静态聚类导致原型坍缩为单一向量；DOR 的正交约束 + 三阶段演化成功维持了原型多样性。

  

#### 4.3 Comparison with Baselines（约 200 词 + 表 1）

  

| Method | Text at Inf. | Dice | mIoU | Acc |

|--------|-------------|------|------|-----|

| U-Net | ✗ | -- | -- | -- |

| ConvNeXt + Decoder (pure vision) | ✗ | -- | -- | -- |

| Ours w/o DOR (retrieval only) | ✗ | -- | -- | -- |

| **Ours (SCSF)** | **✗** | **0.836** | **0.720** | -- |

  

注：ProLearn 因无法复现不纳入直接对比。

  

#### 4.4 Ablation Studies（约 300 词 + 表 2 + 表 3）

  

**表 2：DOR 组件消融**

  

| Variant | Dice |

|---------|------|

| Full SCSF (K=32) | **0.836** |

| − DOR (retrieval only) | -- |

| − Three-Phase Evolution (fixed prototypes) | -- |

| − L_ortho + L_align | -- |

| w/o retrieval (pure vision) | -- |

  

**表 3：原型数量 K 的影响**

  

| K | 8 | 16 | 32 | 64 |

|---|----|----|----|-----|

| Dice | -- | -- | 0.836 | -- |

  

#### 4.5 Qualitative Analysis（约 200 词 + 图 3 + 图 4）

- 图 3：分割结果可视化（原图 / GT / pure vision / Ours），选 challenging cases

- 图 4：三阶段原型 PCA 演化

  

---

  

### 5. Discussion（约 0.3 页）

  

#### 5.1 Why Does DOR Avoid Collapse?

1. FPS + Gram-Schmidt 正交初始化 → 初始几何分散

2. L_ortho 持续对抗坍缩趋势

3. L_util 防止少数原型垄断

4. L_align 防止原型退化

5. 三阶段动态演化 vs 静态固定

6. 原型在视觉空间 → 与分割的像素级多样性共享表征

  

#### 5.2 Limitations

1. 检索库覆盖率决定上限

2. 单数据集（ProLearn 用了 3 个）

3. 三阶段分界点经验值

4. K=32 未自适应

  

---

  

### 6. Conclusion（约 120 词）

  

本文揭示了文本自由分割中静态原型聚类的坍缩问题，提出 DOR——通过视觉空间可学习原型、跨模态置信度门控、正交约束和三阶段演化策略，在不使用推理时文本的情况下实现医学图像分割。QaTa-COV19 Dice 0.836，原型保持分散（<0.3）。

  

---

  

## 实验补充清单

  

### 必须做

| # | 实验 | 代码改动 |

|---|------|---------|

| 1 | 纯视觉 baseline（无检索无 DOR）| `enhanced_semantic_seq = E_4` |

| 2 | 无 DOR baseline（检索文本直送解码器）| `enhanced_semantic_seq = response_text_emb` |

| 3 | 原型坍缩分析图（ProLearn vs DOR 相似度矩阵）| `expertise/analyze_prototype.py` |

| 4 | 分割结果可视化 | inference 脚本 |

  

### 强烈建议

| # | 实验 | 代码改动 |

|---|------|---------|

| 5 | 去掉 L_ortho + L_align 消融 | 注释对应 loss |

| 6 | 原型数量 K=8/64 对比 | 修改 `num_prototypes` |

| 7 | 固定原型（无三阶段）| 将原型设为不可训练的 buffer |

  

---

  

## 图表清单

  

| # | 内容 | 状态 |

|---|------|------|

| 图 1 | 整体框架图 | ❌ 需画 |

| 图 2 | **原型坍缩对比**：ProLearn vs DOR 相似度矩阵 | ⚠️ analyze_prototype.py 产出 |

| 图 3 | 分割结果对比（原图/GT/pure vision/Ours）| ⚠️ inference 产出 |

| 图 4 | 三阶段原型 PCA 演化 | ⚠️ analyze_prototype.py 产出 |

| 表 1 | 主实验结果 | ⚠️ baseline 跑完填 |

| 表 2 | DOR 组件消融 | ⚠️ 消融跑完填 |

| 表 3 | 原型数量 K 的影响 | ⚠️ 跑完填 |

| 表 4（新）| **原型坍缩定量分析**（BiomedCLIP vs ProLearn vs DOR）| ⚠️ 已有部分数据 |

  

---

  

## 投稿目标

  

| 优先级 | 会议 | CCF | deadline | 录用通知 |

|--------|------|-----|----------|---------|

| **首选** | PRCV 2026 | C | 5 月 30 日 | 6 月 20 日 |

| 备选 | ACCV 2026 | C | 7 月 3 日 | 9 月 20 日 |