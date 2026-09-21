# PDCCH / DCI 相关 3GPP 协议原文摘抄与解读

<!-- TOC-START -->
<details open>
<summary><b>目录</b></summary>

  - [0. 版本与约定](#0-版本与约定)
- [1. TS 38.211 §7.3.2 Physical downlink control channel (PDCCH)](#1-ts-38211-732-physical-downlink-control-channel-pdcch)
  - [1.1 §7.3.2.1 Control-channel element (CCE)](#11-7321-control-channel-element-cce)
  - [1.2 §7.3.2.2 Control-resource set (CORESET)](#12-7322-control-resource-set-coreset)
  - [1.3 §7.3.2.3 Scrambling](#13-7323-scrambling)
  - [1.4 §7.3.2.4 PDCCH modulation](#14-7324-pdcch-modulation)
  - [1.5 §7.3.2.5 Mapping to physical resources](#15-7325-mapping-to-physical-resources)
- [2. TS 38.211 §7.4.1.3 Demodulation reference signals for PDCCH](#2-ts-38211-7413-demodulation-reference-signals-for-pdcch)
  - [2.1 §7.4.1.3.1 Sequence generation](#21-74131-sequence-generation)
  - [2.2 §7.4.1.3.2 Mapping to physical resources](#22-74132-mapping-to-physical-resources)
- [3. TS 38.211 通用函数（§5.1.3 / §5.2.1）](#3-ts-38211-通用函数513--521)
  - [3.1 §5.1.3 QPSK](#31-513-qpsk)
  - [3.2 §5.2.1 Pseudo-random sequence generation](#32-521-pseudo-random-sequence-generation)
- [4. TS 38.212 §5.1 CRC calculation](#4-ts-38212-51-crc-calculation)
- [5. TS 38.212 §5.3.1 Polar coding](#5-ts-38212-531-polar-coding)
  - [5.1 §5.3.1 码长 N 的确定](#51-531-码长-n-的确定)
  - [5.2 §5.3.1.1 Interleaving](#52-5311-interleaving)
  - [5.3 §5.3.1.2 Polar encoding](#53-5312-polar-encoding)
- [6. TS 38.212 §5.4.1 Rate matching for Polar code](#6-ts-38212-541-rate-matching-for-polar-code)
  - [6.1 §5.4.1.1 Sub-block interleaving](#61-5411-sub-block-interleaving)
  - [6.2 §5.4.1.2 Bit selection](#62-5412-bit-selection)
  - [6.3 §5.4.1.3 Interleaving of coded bits](#63-5413-interleaving-of-coded-bits)
- [7. TS 38.212 §7.3 Downlink control information](#7-ts-38212-73-downlink-control-information)
  - [7.1 §7.3.2 CRC attachment](#71-732-crc-attachment)
  - [7.2 §7.3.3 Channel coding](#72-733-channel-coding)
  - [7.3 §7.3.4 Rate matching](#73-734-rate-matching)
  - [7.4 §7.3 DCI 完整数据流（小结）](#74-73-dci-完整数据流小结)
- [8. TS 38.214 §4.1 Power allocation for downlink](#8-ts-38214-41-power-allocation-for-downlink)
- [9. TS 38.213 §10 / §12 / §13](#9-ts-38213-10--12--13)
  - [9.1 §10.1 CCE 索引与 PDCCH 候选（`cce_index` 的来源）](#91-101-cce-索引与-pdcch-候选cce_index-的来源)
  - [9.2 §12 Bandwidth part operation](#92-12-bandwidth-part-operation)
  - [9.3 §13 UE procedure for monitoring Type0-PDCCH CSS sets](#93-13-ue-procedure-for-monitoring-type0-pdcch-css-sets)
- [10. TS 38.331 §6.3.2 `ControlResourceSet`](#10-ts-38331-632-controlresourceset)
- [11. SCF-222 5G FAPI §3.4.2.1 PDCCH PDU](#11-scf-222-5g-fapi-3421-pdcch-pdu)
  - [11.1 Table 3.4.2.1-1 PDCCH PDU（旧编号 Table 3-36）](#111-table-3421-1-pdcch-pdu旧编号-table-3-36)
  - [11.2 Table 3.4.2.1-2 DCI PDU（旧编号 Table 3-37）](#112-table-3421-2-dci-pdu旧编号-table-3-37)
- [12. 全链路小结](#12-全链路小结)
  - [12.1 协议章节 → cuPHY 代码 的完整映射](#121-协议章节--cuphy-代码-的完整映射)
  - [12.2 关键常数的协议出处](#122-关键常数的协议出处)
  - [12.3 本文档的摘抄覆盖情况](#123-本文档的摘抄覆盖情况)

</details>
<!-- TOC-END -->

> **用途**：为 `spec_list.md`（cuBB 26-1 PDCCH 协议章节清单）提供协议原文底稿。
> 每节按 **原文（英文，逐字）→ 译文 → 解读** 三段组织，章节顺序与 `spec_list.md` 末尾的「关联 3GPP 文档一览」一致。

## 0. 版本与约定

| 协议 | 版本 | 本地对应文件 |
|---|---|---|
| TS 38.211 | **V17.10.0** | `4_[PHY]_TS_38211_v171000p.pdf` |
| TS 38.212 | **V17.13.0** | `5_[PHY]_TS_38212_v171300p.pdf` |
| TS 38.213 | **V17.13.0** | `6_[PHY]_TS_38213_v171300p.pdf` |
| TS 38.214 | **V17.16.0** | `7_[PHY]_TS_38214_v171600p.pdf` |
| TS 38.331 | **V17.16.0** | `10_[RRC]_TS_38331_v171600p.pdf` |
| SCF-222 5G FAPI | **222.10.00 (2025-07)** | `SCF-222.10.00-5G-FAPI-PHY-API-Specification.pdf` |

**记号约定**（贯穿全文）：

| 记号 | 含义 | 典型值 / 对应 cuPHY 字段 |
|---|---|---|
| $N_{RB}^{CORESET}$ | CORESET 频域 RB 数 | `rb_coreset`（由 `freq_domain_resource` 推导）|
| $N_{symb}^{CORESET}$ | CORESET 时域符号数 | `n_sym` ∈ {1,2,3} |
| $N_{REG}^{CORESET}$ | CORESET 内 REG 总数 | $=N_{RB}^{CORESET}N_{symb}^{CORESET}$ |
| $L$ | REG bundle 大小 | `bundle_size` ∈ {2,3,6} |
| $R$ | 交织器行数 | `interleaver_size` ∈ {2,3,6} |
| $n_{shift}$ | 交织移位量 | `shift_index` |
| $C$ | 交织器列数 | $=N_{REG}^{CORESET}/(LR)$ |
| $M_{bit}$ | PDCCH 编码后比特数 | `nTxBits` $=2\cdot9\cdot6\cdot AL$ |
| $M_{symb}$ | QPSK 符号数 | $=M_{bit}/2$ |
| $\beta_{PDCCH}$ / $\beta_{DMRS}^{PDCCH}$ | 数据 / DMRS 幅度因子 | `beta_qam` / `beta_dmrs` |
| $n_{ID}$ | 加扰 ID | `dmrs_id`（`pdcch-DMRS-ScramblingID` 或 $N_{ID}^{cell}$）|
| $n_{RNTI}$ | 加扰 RNTI | `rntiBits` |
| $AL$ | 聚合等级 | `aggr_level` ∈ {1,2,4,8,16} |

> **关于 ETSI PDF 的公式缺失**：本地 ETSI 版 PDF 在 §7.3.2.3 处**丢失了 MathType 对象**——
> `c_init` 公式及 $n_{RNTI}$ 两处定义在 PDF 中渲染为空白（经 `get_text()` 与位图渲染双重确认，
> 该位置既无文字也无嵌入图片）。此类位置本文档以 `⟪PDF 此处为空白⟫` 明确标注，
> 并在其后单独给出**补注**（来源：3GPP 原始版本 + cuPHY 代码交叉验证），不与原文摘抄混淆。

### 章节编排顺序（与信号处理顺序不同，务必注意）

**本文档按「协议编号」排序，不按「信号处理流程」排序。** 排序规则有三层：

1. **先按协议号**：38.211 → 38.212 → 38.214 → 38.213 → 38.331 → SCF FAPI
2. **同一协议内先主体、后依赖**：§1 先讲 PDCCH 本体，§3 才补它调用的 QPSK 与 Gold 序列
3. **越靠后越远离物理层**：38.211 是波形，FAPI 是接口，最后是全链路小结

| 章 | 协议 | 内容 |
|---|---|---|
| §1 | 38.211 §7.3.2 | PDCCH 本体（CCE / CORESET / 加扰 / 调制 / RE 映射）|
| §2 | 38.211 §7.4.1.3 | PDCCH DM-RS |
| §3 | 38.211 §5.1.3 / §5.2.1 | 通用函数：QPSK、Gold 序列 |
| §4 | 38.212 §5.1 | CRC 计算 |
| §5 | 38.212 §5.3.1 | Polar 编码 |
| §6 | 38.212 §5.4.1 | 速率匹配 |
| §7 | 38.212 §7.3 | DCI（CRC 附着 / 信道编码 / 速率匹配）|
| §8 | 38.214 §4.1 | 下行功率分配 |
| §9 | 38.213 §10 / §12 / §13 | 搜索空间、BWP、CORESET#0 |
| §10 | 38.331 §6.3.2 | `ControlResourceSet` |
| §11 | SCF FAPI §3.4.2.1 | PDCCH PDU / DCI PDU |
| §12 | — | 全链路小结 |

#### 实际处理流程是**反着走**的

发射链路上，**38.212 全部做完才轮到 38.211**：

```
DCI payload ──▶ §7 CRC+RNTI ──▶ §5 Polar 编码 ──▶ §6 速率匹配
                    └────────── 38.212 编码域 ──────────┘
                                                        │
                                                        ▼
   TF 网格 ◀── §1.5 RE 映射 ◀── §1.4 QPSK ◀── §1.3 加扰
                    └────────── 38.211 波形域 ──────────┘
```

也就是说，**文档里排第 1 章的内容，在流程上是最后一步**；而 §4~§7 的 38.212 才是链路的开头。
这是刻意的取舍：按协议号排便于对照原文查阅，代价是读起来不像流程。

#### 想顺着信号走一遍，请按这个次序读

```
§9.1  →  §7.1  →  §4     →  §5    →  §6      →  §1.3 →  §3.2 →  §1.4 →  §3.1 →  §1.5 →  §2
cce_index  CRC附着  CRC多项式  Polar   速率匹配   加扰    Gold   QPSK   星座   RE映射  DMRS
└L2 给的┘ └────────── 38.212 编码域 ──────────┘ └────────── 38.211 波形域 ──────────┘
```

#### 已有的两张流程视角图

本文档另备了两处按流程组织的内容，可与上述章节顺序互补：

- **§7.4「DCI 完整数据流」** —— 比特层面的 `a → b → c → d → f` 全链路
- **§12.1「协议章节 → cuPHY 代码 的完整映射」** —— 含 CPU/GPU kernel 分工

> **与其他文档的分工**：`spec_list.md` 按「是否需要补齐」排优先级（查缺口用），
> 本文档按协议号排（查原文用），`pdcch_fapi.md` 按 FAPI 消息结构排（查字段用）。

---

# 1. TS 38.211 §7.3.2 Physical downlink control channel (PDCCH)

## 1.1 §7.3.2.1 Control-channel element (CCE)

### 原文

> A physical downlink control channel consists of one or more control-channel elements (CCEs) as indicated in Table 7.3.2.1-1.
>
> **Table 7.3.2.1-1: Supported PDCCH aggregation levels.**
>
> | Aggregation level | Number of CCEs |
> |---|---|
> | 1 | 1 |
> | 2 | 2 |
> | 4 | 4 |
> | 8 | 8 |
> | 16 | 16 |

### 译文

> 一个物理下行控制信道由一个或多个控制信道单元（CCE）构成，如表 7.3.2.1-1 所示。
>
> **表 7.3.2.1-1：支持的 PDCCH 聚合等级**
>
> | 聚合等级 | CCE 数量 |
> |---|---|
> | 1 | 1 |
> | 2 | 2 |
> | 4 | 4 |
> | 8 | 8 |
> | 16 | 16 |

### 解读

聚合等级（AL）= 用几个 CCE 来发这一条 DCI。**这张表看似废话（AL 等于 CCE 数），真正的信息是"只有这 5 个值"** ——必须是 2 的幂，最大 16。

**比喻**：CCE 是"标准集装箱"，DCI 是货物。货多（payload 大）或路况差（信道差、UE 在小区边缘）就多用几个箱子装，冗余度更高、更抗错。AL=1 是最省资源但最脆弱，AL=16 是最浪费但最可靠。

对 cuPHY 的直接影响——这是**唯一**决定编码输出长度的量：

$$M_{bit} = 2 \times 9 \times 6 \times AL$$

拆开看：每个 CCE = 6 个 REG = 6 个 RB；每 RB 12 个 RE 中 **9 个是数据、3 个是 DMRS**；每个 RE 承载 QPSK 的 2 bit。所以 AL=16 时 $M_{bit}=1728$ bit。

```c
// polar_encoder.cu:1052
const uint32_t nTxBits = 2 * 9 * 6 * dci_params.aggr_level;
```

`aggr_level` 只能是 5 个合法值这件事，在 cuPHY 里被用作**性能优化的前提**：

```c
// 因为 AL 必为 2 的幂，才能直接算出向上取整到 2 的幂的结果
const uint32_t roundUpToPow2_nTxBits = 2 * 64 * dci_params.aggr_level;
```

L2 adapter 侧则做了严格白名单校验（`scf_5g_fapi_dl_validate.cpp:283`），非法值直接拒收整个 PDU。

---

## 1.2 §7.3.2.2 Control-resource set (CORESET)

### 原文

> A control-resource set consists of $N_{RB}^{CORESET}$ resource blocks in the frequency domain and $N_{symb}^{CORESET} \in \{1,2,3\}$ symbols in the time domain.
>
> A control-channel element consists of 6 resource-element groups (REGs) where a resource-element group equals one resource block during one OFDM symbol. Resource-element groups within a control-resource set are numbered in increasing order in a time-first manner, starting with 0 for the first OFDM symbol and the lowest-numbered resource block in the control resource set.
>
> A UE can be configured with multiple control-resource sets. Each control-resource set is associated with one CCE-to-REG mapping only.
>
> The CCE-to-REG mapping for a control-resource set can be interleaved or non-interleaved and is described by REG bundles:
>
> - REG bundle $i$ is defined as REGs $\{iL, iL+1, \ldots, iL+L-1\}$ where $L$ is the REG bundle size, $i = 0,1,\ldots,N_{REG}^{CORESET}/L - 1$, and $N_{REG}^{CORESET} = N_{RB}^{CORESET} N_{symb}^{CORESET}$ is the number of REGs in the CORESET
>
> - CCE $j$ consists of REG bundles $\{f(6j/L), f(6j/L+1), \ldots, f(6j/L+6/L-1)\}$ where $f(\cdot)$ is an interleaver
>
> For non-interleaved CCE-to-REG mapping, $L = 6$ and $f(x) = x$.
>
> For interleaved CCE-to-REG mapping, $L \in \{2,6\}$ for $N_{symb}^{CORESET} = 1$ and $L \in \{N_{symb}^{CORESET}, 6\}$ for $N_{symb}^{CORESET} \in \{2,3\}$. The interleaver is defined by
>
> $$f(x) = \left(rC + c + n_{shift}\right) \bmod \left(N_{REG}^{CORESET}/L\right)$$
> $$x = cR + r$$
> $$r = 0,1,\ldots,R-1$$
> $$c = 0,1,\ldots,C-1$$
> $$C = N_{REG}^{CORESET}/(LR)$$
>
> where $R \in \{2,3,6\}$.
>
> The UE is not expected to handle configurations resulting in the quantity $C$ not being an integer.
>
> For a CORESET configured by the *ControlResourceSet* IE:
>
> - $N_{RB}^{CORESET}$ is given by the higher-layer parameter *frequencyDomainResources*;
> - $N_{symb}^{CORESET}$ is given by the higher-layer parameter *duration*, where $N_{symb}^{CORESET} = 3$ is supported only if the higher-layer parameter *dmrs-TypeA-Position* equals 'pos3';
> - interleaved or non-interleaved mapping is given by the higher-layer parameter *cce-REG-MappingType*;
> - $L$ equals 6 for non-interleaved mapping and is given by the higher-layer parameter *reg-BundleSize* for interleaved mapping;
> - $R$ is given by the higher-layer parameter *interleaverSize*;
> - $n_{shift} \in \{0,1,\ldots,274\}$ is given by the higher-layer parameter *shiftIndex* if provided, otherwise $n_{shift} = N_{ID}^{cell}$;
> - for both interleaved and non-interleaved mapping, the UE may assume
>   - the same precoding being used within a REG bundle if the higher-layer parameter *precoderGranularity* equals *sameAsREG-bundle*;
>   - the same precoding being used across the all resource-element groups within the set of contiguous resource blocks in the CORESET, and that no resource elements in the CORESET overlap with an SSB or LTE cell-specific reference signals as indicated by the higher-layer parameter *lte-CRS-ToMatchAround*, *lte-CRS-PatternList1*, or *lte-CRS-PatternList2*, if the higher-layer parameter *precoderGranularity* equals *allContiguousRBs*.
>
> For CORESET 0 configured by the *ControlResourceSetZero* IE:
>
> - $N_{RB}^{CORESET}$ and $N_{symb}^{CORESET}$ are defined by clause 13 of [5, TS 38.213];
> - the UE may assume interleaved mapping
> - $L = 6$;
> - $R = 2$;
> - $n_{shift} = N_{ID}^{cell}$;
> - the UE may assume normal cyclic prefix when CORESET 0 is configured by MIB or SIB1;
> - the UE may assume the same precoding being used within a REG bundle.

### 译文

> 一个控制资源集在频域上由 $N_{RB}^{CORESET}$ 个资源块构成，在时域上由 $N_{symb}^{CORESET} \in \{1,2,3\}$ 个符号构成。
>
> 一个控制信道单元由 6 个资源单元组（REG）构成，其中一个资源单元组等于一个 OFDM 符号期间的一个资源块。控制资源集内的资源单元组按**时间优先**的方式递增编号，从第一个 OFDM 符号、控制资源集中编号最低的资源块开始编号为 0。
>
> 一个 UE 可以被配置多个控制资源集。每个控制资源集只关联一种 CCE-to-REG 映射。
>
> 控制资源集的 CCE-to-REG 映射可以是交织的或非交织的，并用 REG bundle 描述：
>
> - REG bundle $i$ 定义为 REG 集合 $\{iL, iL+1, \ldots, iL+L-1\}$，其中 $L$ 是 REG bundle 大小，$i = 0,1,\ldots,N_{REG}^{CORESET}/L - 1$，且 $N_{REG}^{CORESET} = N_{RB}^{CORESET} N_{symb}^{CORESET}$ 是 CORESET 内的 REG 数量
>
> - CCE $j$ 由 REG bundle 集合 $\{f(6j/L), f(6j/L+1), \ldots, f(6j/L+6/L-1)\}$ 构成，其中 $f(\cdot)$ 是一个交织器
>
> 对于非交织 CCE-to-REG 映射，$L = 6$ 且 $f(x) = x$。
>
> 对于交织 CCE-to-REG 映射，当 $N_{symb}^{CORESET} = 1$ 时 $L \in \{2,6\}$；当 $N_{symb}^{CORESET} \in \{2,3\}$ 时 $L \in \{N_{symb}^{CORESET}, 6\}$。交织器定义为
>
> $$f(x) = \left(rC + c + n_{shift}\right) \bmod \left(N_{REG}^{CORESET}/L\right)$$
> $$x = cR + r$$
> $$r = 0,1,\ldots,R-1$$
> $$c = 0,1,\ldots,C-1$$
> $$C = N_{REG}^{CORESET}/(LR)$$
>
> 其中 $R \in \{2,3,6\}$。
>
> UE 不需要处理导致 $C$ 不为整数的配置。
>
> 对于由 *ControlResourceSet* IE 配置的 CORESET：
>
> - $N_{RB}^{CORESET}$ 由高层参数 *frequencyDomainResources* 给出；
> - $N_{symb}^{CORESET}$ 由高层参数 *duration* 给出，其中 $N_{symb}^{CORESET} = 3$ 仅在高层参数 *dmrs-TypeA-Position* 等于 'pos3' 时才支持；
> - 交织或非交织映射由高层参数 *cce-REG-MappingType* 给出；
> - 非交织映射时 $L$ 等于 6；交织映射时由高层参数 *reg-BundleSize* 给出；
> - $R$ 由高层参数 *interleaverSize* 给出；
> - $n_{shift} \in \{0,1,\ldots,274\}$ 在提供了高层参数 *shiftIndex* 时由其给出，否则 $n_{shift} = N_{ID}^{cell}$；
> - 对于交织和非交织映射，UE 均可假设
>   - 若高层参数 *precoderGranularity* 等于 *sameAsREG-bundle*，则在一个 REG bundle 内使用相同的预编码；
>   - 若高层参数 *precoderGranularity* 等于 *allContiguousRBs*，则在 CORESET 中连续资源块集合内的所有资源单元组上使用相同的预编码，且 CORESET 中没有资源单元与 SSB 或由高层参数 *lte-CRS-ToMatchAround*、*lte-CRS-PatternList1*、*lte-CRS-PatternList2* 指示的 LTE 小区特定参考信号重叠。
>
> 对于由 *ControlResourceSetZero* IE 配置的 CORESET 0：
>
> - $N_{RB}^{CORESET}$ 和 $N_{symb}^{CORESET}$ 由 [5, TS 38.213] 第 13 节定义；
> - UE 可假设为交织映射
> - $L = 6$；
> - $R = 2$；
> - $n_{shift} = N_{ID}^{cell}$；
> - 当 CORESET 0 由 MIB 或 SIB1 配置时，UE 可假设常规循环前缀；
> - UE 可假设在一个 REG bundle 内使用相同的预编码。

### 解读

这是整个 PDCCH 里**最绕**的一节。核心就一件事：**逻辑上连续的 CCE，物理上要被打散到整个 CORESET 频段上去**。

#### 三层容器

```
RE  ──12个──> REG(=1个RB×1个符号) ──L个──> REG bundle ──6/L个──> CCE ──AL个──> PDCCH
```

注意 REG 的编号是**时间优先**：CORESET 有 3 个符号时，REG 0/1/2 是同一个 RB 的三个符号，REG 3/4/5 才是下一个 RB。

```
频率 ↑
 RB2 │ REG6 │ REG7 │ REG8 │
 RB1 │ REG3 │ REG4 │ REG5 │
 RB0 │ REG0 │ REG1 │ REG2 │
     └──sym0──sym1──sym2──→ 时间
```

#### 为什么要交织

**比喻**：把鸡蛋分装到篮子里。非交织 = 一个 CCE 的 6 个 REG 全放在**相邻的 6 个 RB**上——如果这段频率正好碰上深衰落，整条 DCI 全军覆没。交织 = 把这 6 个 REG **撒到整个 CORESET 的不同频率位置**，即使某段频率被打掉，其他部分还在，Polar 译码仍有机会救回来。

交织公式本质是一个**行列转置（block interleaver）**：

```
把 x 按“列优先”写进 R 行 C 列的矩阵，再按“行优先”读出，最后整体循环移位 n_shift
```

- $x = cR + r$ ← 写入位置（第 $r$ 行第 $c$ 列）
- $f(x) = rC + c + n_{shift}$ ← 读出位置

$n_{shift}$ 的作用是**小区间随机化**：默认 $n_{shift}=N_{ID}^{cell}$，相邻小区即使 CORESET 配置完全相同，PDCCH 落的物理位置也会错开，避免持续碰撞。

#### cuPHY 实现对照

`compute_map()`（`embed_pdcch_tf_signal.cu:263`）逐字对应：

```c
uint32_t c = log_bundle / interleaver_size;   // c = x / R
uint32_t r = log_bundle - c * interleaver_size; // r = x mod R
log_bundle = (r * C + c + shift_index) % N_bundle;  // f(x)
```

`C` 的计算（`embed_pdcch_tf_signal.cu:469`）：

```c
uint32_t C = interleaved ? (n_CCEs * 6 / (bundle_size * interleaver_size)) : 1;
```

这里用 $n_{CCE}\cdot 6$ 代替 $N_{REG}^{CORESET}$ ——**仅当 CORESET 位图全 1 时两者才相等**。位图有空洞时 $N_{REG}^{CORESET}$ 应按**实际分配的** REG 数算，cuPHY 走的正是这条路（`n_CCE = popcount(freq_domain_resource) * n_sym`），与协议的"$N_{REG}^{CORESET}$ 是 CORESET 内 REG 数"一致。

**"$C$ 必须是整数"这条约束**：cuPHY 没有做运行时检查（`cuphyPdcchPipelinePrepare()` 里有一段被 `/* */` 注释掉的 sanity check），依赖 L2 保证。配置非法时会静默产生错误的 bundle 映射。

**cuPHY 未实现 `precoderGranularity`**：协议给了两种预编码粒度假设，adapter 只校验取值 0/1，kernel 完全不使用——cuPHY 对每个 DCI 用同一个 `pmW` 矩阵，等价于 *sameAsREG-bundle*。

---

## 1.3 §7.3.2.3 Scrambling

### 原文

> The UE shall assume the block of bits $b(0), \ldots, b(M_{bit}-1)$, where $M_{bit}$ is the number of bits transmitted on the physical channel, is scrambled prior to modulation, resulting in a block of scrambled bits $\tilde{b}(0), \ldots, \tilde{b}(M_{bit}-1)$ according to
>
> $$\tilde{b}(i) = \left(b(i) + c(i)\right) \bmod 2$$
>
> where the scrambling sequence $c(i)$ is given by clause 5.2.1. The scrambling sequence generator shall be initialized with
>
> ⟪**PDF 此处为空白** —— ETSI 版 MathType 对象丢失⟫
>
> where
>
> - for a UE-specific search space as defined in clause 10 of [5, TS 38.213], ⟪空白⟫ equals the higher-layer parameter *pdcch-DMRS-ScramblingID* if configured;
> - for a PDCCH with the CRC scrambled by G-RNTI, G-CS-RNTI, or MCCH-RNTI in a common search space as defined in clause 10 of [5, TS 38.213], $n_{ID} \in \{0,1,\ldots,65535\}$ equals the higher-layer parameter *pdcch-DMRS-ScramblingID* if configured in a common MBS frequency resource;
> - $n_{ID} = N_{ID}^{cell}$ otherwise
>
> and where
>
> - ⟪空白⟫ is given by the C-RNTI for a PDCCH in a UE-specific search space if the higher-layer parameter *pdcch-DMRS-ScramblingID* is configured, and
> - ⟪空白⟫ otherwise.

### 补注（非原文）

本地 ETSI PDF 丢失的三处，按 3GPP 原始版本应为：

$$c_{init} = \left(n_{RNTI} \cdot 2^{16} + n_{ID}\right) \bmod 2^{31}$$

- 第一处空白 = $n_{ID}$
- 第二、三处空白 = $n_{RNTI}$，且第三处完整为 "$n_{RNTI} = 0$ otherwise"

**交叉验证**（cuPHY 代码逐位吻合，`embed_pdcch_tf_signal.cu:302`）：

```c
uint32_t c_init = ((params[DCI_id].rntiBits << 16) + params[DCI_id].dmrs_id) & 0x7fffffffU;
//                              └─ ×2^16 ─┘         └─ n_ID ─┘              └─ mod 2^31 ─┘
```

### 译文

> UE 应假设比特块 $b(0), \ldots, b(M_{bit}-1)$（其中 $M_{bit}$ 是在该物理信道上传输的比特数）在调制前经过加扰，得到加扰后的比特块 $\tilde{b}(0), \ldots, \tilde{b}(M_{bit}-1)$，按下式：
>
> $$\tilde{b}(i) = \left(b(i) + c(i)\right) \bmod 2$$
>
> 其中加扰序列 $c(i)$ 由 5.2.1 节给出。加扰序列生成器应初始化为
>
> $$c_{init} = \left(n_{RNTI} \cdot 2^{16} + n_{ID}\right) \bmod 2^{31} \quad \text{（据原始版本补）}$$
>
> 其中
>
> - 对于 [5, TS 38.213] 第 10 节定义的 UE 专用搜索空间，若已配置，$n_{ID}$ 等于高层参数 *pdcch-DMRS-ScramblingID*；
> - 对于在公共搜索空间中 CRC 由 G-RNTI、G-CS-RNTI 或 MCCH-RNTI 加扰的 PDCCH，若在公共 MBS 频率资源中配置了高层参数 *pdcch-DMRS-ScramblingID*，则 $n_{ID} \in \{0,1,\ldots,65535\}$ 等于该参数；
> - 其他情况下 $n_{ID} = N_{ID}^{cell}$
>
> 且
>
> - 若配置了高层参数 *pdcch-DMRS-ScramblingID*，则对 UE 专用搜索空间中的 PDCCH，$n_{RNTI}$ 由 C-RNTI 给出；
> - 否则 $n_{RNTI} = 0$。

### 解读

加扰就是**把编码后的比特和一串伪随机序列逐位异或**（mod 2 加法 = XOR）。

**为什么要加扰**：

1. **防止长串相同比特** → 避免 PAPR 尖峰和定时估计误锁
2. **小区/用户间随机化** → 不同 $c_{init}$ 产生完全不同的序列，邻区干扰被"白化"成噪声而非结构化干扰

$c_{init}$ 的构造是个**拼接**而非相加：

```
 31 bit 的 c_init
┌──────────────┬────────────────┐
│  n_RNTI      │     n_ID       │
│  (高 15 bit) │   (低 16 bit)  │
└──────────────┴────────────────┘
      ×2^16 就是左移 16 位腾出低 16 位给 n_ID
```

**比喻**：像"区号 + 电话号码"。$n_{ID}$（小区级）保证不同小区互不干扰，$n_{RNTI}$（用户级）保证同小区不同 UE 的 PDCCH 互不干扰。公共搜索空间里 $n_{RNTI}=0$ ——因为那是**广播性质**的，所有 UE 都要能解。

cuPHY 侧（`genScramblingSeqKernel`）每个 DCI 一个线程块，每线程产生 32 bit：

```c
d_x_scramSeq_addr[offset + tid] = __brev(gold32(c_init, tid * 32));
```

`__brev` 是**位序反转**——因为协议按 $c(0), c(1), \ldots$ 的顺序消费，而 `gold32` 返回的 32 bit 字里 bit 顺序相反，反转后才能用 `>> (31 - (idxBit & 0x1F))` 直接取位。

> **注意**：cuPHY 的 `dmrs_id` 一个字段同时充当 PDCCH 数据加扰的 $n_{ID}$（本节）和 DMRS 的 $N_{ID}$（§7.4.1.3.1）。协议上这两者是**不同参数**（FAPI 的 `n-ID-PDCCH-Data` 与 `n-ID-PDCCH-DMRS-v3`），cuBB 未区分，见 `spec_list.md` 的 ⚠️ 表。

---

## 1.4 §7.3.2.4 PDCCH modulation

### 原文

> The UE shall assume the block of bits $\tilde{b}(0), \ldots, \tilde{b}(M_{bit}-1)$ to be QPSK modulated as described in clause 5.1.3, resulting in a block of complex-valued modulation symbols $d(0), \ldots, d(M_{symb}-1)$.

### 译文

> UE 应假设比特块 $\tilde{b}(0), \ldots, \tilde{b}(M_{bit}-1)$ 按 5.1.3 节所述进行 QPSK 调制，得到复值调制符号块 $d(0), \ldots, d(M_{symb}-1)$。

### 解读

一句话，但信息量在于：**PDCCH 永远是 QPSK，没有自适应调制**。

对比 PDSCH 可以用 QPSK/16QAM/64QAM/256QAM/1024QAM 随信道质量切换——PDCCH 不行。原因很实际：**UE 在盲检 PDCCH 时还不知道任何调度信息**，如果调制方式也要盲猜，搜索空间会爆炸。固定 QPSK 是"自举"的必要条件。

由此 $M_{symb} = M_{bit}/2$，恒定。

---

## 1.5 §7.3.2.5 Mapping to physical resources

### 原文

> The UE shall assume the block of complex-valued symbols $d(0), \ldots, d(M_{symb}-1)$ to be scaled by a factor $\beta_{PDCCH}$ and mapped to resource elements $(k,l)_{p,\mu}$ used for the monitored PDCCH and not used for the associated PDCCH DMRS in increasing order of first $k$, then $l$. The antenna port $p = 2000$.

### 译文

> UE 应假设复值符号块 $d(0), \ldots, d(M_{symb}-1)$ 被因子 $\beta_{PDCCH}$ 缩放，并映射到用于所监听 PDCCH、且未被相应 PDCCH DMRS 占用的资源单元 $(k,l)_{p,\mu}$ 上，映射顺序为**先 $k$ 递增、再 $l$ 递增**。天线端口 $p = 2000$。

### 解读

三个要点：

**1. 映射顺序"先 k 后 l"= 频率优先**。先把一个 OFDM 符号的所有子载波填满，再换下一个符号。

**2. "not used for the associated PDCCH DMRS"** —— 数据要**跳过** DMRS 占用的 RE。每个 RB 里 DMRS 占 3 个（位置 1/5/9），剩下 9 个给数据。这就是 $M_{bit}$ 公式里 "9" 的来源。

```
一个 RB 的 12 个 RE（k = 0..11）：
┌───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┬───┐
│ D │DMR│ D │ D │ D │DMR│ D │ D │ D │DMR│ D │ D │
└───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┴───┘
  0   1   2   3   4   5   6   7   8   9  10  11
      ↑           ↑           ↑
   DMRS 在 k mod 4 == 1 的位置
```

**3. 天线端口 2000** —— 单端口、单层，无空间复用（对比：PDSCH 用 1000+ 且可多层，SSB 用 4000）。

cuPHY 的 kernel 用一个巧妙的方式同时完成"跳过 DMRS"和"计算数据索引"：

```c
if((tid & 0x3) == 1) { /* DMRS */ }
else {
    // 从 RE 序号 tid 反推它是第几个数据 RE：减掉前面已出现的 DMRS 个数
    idxQam = (tid - ((tid - 1) / 4 + 1)) + symbol_id * n_qam_per_sym;
}
```

$\beta_{PDCCH}$ 即 `beta_qam`，由 FAPI 的 `powerControlOffsetSS` 换算而来（见第 6 章 38.214 §4.1）。

---

# 2. TS 38.211 §7.4.1.3 Demodulation reference signals for PDCCH

## 2.1 §7.4.1.3.1 Sequence generation

### 原文

> The UE shall assume the reference-signal sequence $r_l(m)$ for OFDM symbol $l$ is defined by
>
> $$r_l(m) = \frac{1}{\sqrt{2}}\left(1 - 2 \cdot c(2m)\right) + j\frac{1}{\sqrt{2}}\left(1 - 2 \cdot c(2m+1)\right).$$
>
> where the pseudo-random sequence $c(i)$ is defined in clause 5.2.1. The pseudo-random sequence generator shall be initialized with
>
> $$c_{init} = \left(2^{17}\left(N_{symb}^{slot} n_{s,f}^{\mu} + l + 1\right)\left(2N_{ID}+1\right) + 2N_{ID}\right) \bmod 2^{31}$$
>
> where $l$ is the OFDM symbol number within the slot, $n_{s,f}^{\mu}$ is the slot number within a frame, and
>
> - $N_{ID} \in \{0,1,\ldots,65535\}$ is given by the higher-layer parameter *pdcch-DMRS-ScramblingID* if provided;
> - $N_{ID} \in \{0,1,\ldots,65535\}$ is given by the higher-layer parameter *pdcch-DMRS-ScramblingID* if configured for a common search space in a common MBS frequency resource;
> - $N_{ID} = N_{ID}^{cell}$ otherwise.

### 译文

> UE 应假设 OFDM 符号 $l$ 的参考信号序列 $r_l(m)$ 定义为
>
> $$r_l(m) = \frac{1}{\sqrt{2}}\left(1 - 2 \cdot c(2m)\right) + j\frac{1}{\sqrt{2}}\left(1 - 2 \cdot c(2m+1)\right).$$
>
> 其中伪随机序列 $c(i)$ 在 5.2.1 节定义。伪随机序列生成器应初始化为
>
> $$c_{init} = \left(2^{17}\left(N_{symb}^{slot} n_{s,f}^{\mu} + l + 1\right)\left(2N_{ID}+1\right) + 2N_{ID}\right) \bmod 2^{31}$$
>
> 其中 $l$ 是时隙内的 OFDM 符号号，$n_{s,f}^{\mu}$ 是帧内的时隙号，且
>
> - 若提供了高层参数 *pdcch-DMRS-ScramblingID*，则 $N_{ID} \in \{0,1,\ldots,65535\}$ 由其给出；
> - 若为公共 MBS 频率资源中的公共搜索空间配置了高层参数 *pdcch-DMRS-ScramblingID*，则 $N_{ID} \in \{0,1,\ldots,65535\}$ 由其给出；
> - 其他情况下 $N_{ID} = N_{ID}^{cell}$。

### 解读

DMRS 序列本身就是**把 Gold 序列每 2 bit 映射成一个 QPSK 点**——和数据用同一套伪随机源，只是消费方式不同（数据是 XOR，DMRS 是直接调制）。$1/\sqrt{2}$ 保证每个符号功率归一。

关键在 $c_{init}$ **每个 OFDM 符号都要重算**（含 $l$）：

```
c_init = 2^17 × (14·slot + l + 1) × (2·N_ID + 1) + 2·N_ID
              └────── 时变部分 ──────┘   └── 小区/UE 标识 ──┘
```

**比喻**：像"日期 + 身份证号"生成的一次性密码。同一个小区、同一个符号位置，每个时隙的 DMRS 都不同——**防止相邻时隙的 DMRS 被误当作当前时隙的**，也让干扰在时间上随机化。

$N_{symb}^{slot}=14$（常规 CP）。cuPHY 实现（`generate_dmrs()`，`embed_pdcch_tf_signal.cu:239`）：

```c
uint32_t t = start_sym + symbol_id;
uint32_t c_init = (1 << 17) * (OFDM_SYMBOLS_PER_SLOT * slot_number + t + 1) * (2 * dmrs_id + 1) + (2 * dmrs_id);
c_init &= ~(1 << 31);   // mod 2^31
```

逐项对应。注意 `c_init &= ~(1<<31)` 清最高位实现 mod $2^{31}$——因为 `uint32_t` 乘法自然溢出后只需再砍掉 bit31。

---

## 2.2 §7.4.1.3.2 Mapping to physical resources

### 原文

> The UE shall assume the sequence $r_l(m)$ is mapped to resource elements $(k,l)_{p,\mu}$ according to
>
> $$a_{k,l}^{(p,\mu)} = \beta_{DMRS}^{PDCCH} \cdot r_l\left(3n + k'\right)$$
> $$k = nN_{sc}^{RB} + 4k' + 1$$
> $$k' = 0,1,2$$
> $$n = 0,1,\ldots$$
>
> where the following conditions are fulfilled
>
> - they are within the resource element groups constituting the PDCCH the UE attempts to decode if the higher-layer parameter *precoderGranularity* equals *sameAsREG-bundle*,
> - all resource-element groups within the set of contiguous resource blocks in the CORESET where the UE attempts to decode the PDCCH if the higher-layer parameter *precoderGranularity* equals *allContiguousRBs*.
>
> The reference point for $k$ is
>
> - subcarrier 0 of the lowest-numbered resource block in the CORESET if the CORESET is configured by the PBCH or by the *controlResourceSetZero* field in the *PDCCH-ConfigCommon* IE,
> - subcarrier 0 in common resource block 0 otherwise
>
> The quantity $l$ is the OFDM symbol number within the slot.
>
> The antenna port $p = 2000$.
>
> A UE not attempting to detect a PDCCH in a CORESET shall not make any assumptions on the presence or absence of DM-RS in the CORESET.
>
> In absence of CSI-RS configuration, and unless otherwise configured, the UE may assume PDCCH DM-RS and SS/PBCH block to be quasi co-located with respect to Doppler shift, Doppler spread, average delay, delay spread, and, when applicable, spatial Rx parameters.

### 译文

> UE 应假设序列 $r_l(m)$ 按下式映射到资源单元 $(k,l)_{p,\mu}$：
>
> $$a_{k,l}^{(p,\mu)} = \beta_{DMRS}^{PDCCH} \cdot r_l\left(3n + k'\right)$$
> $$k = nN_{sc}^{RB} + 4k' + 1$$
> $$k' = 0,1,2$$
> $$n = 0,1,\ldots$$
>
> 其中需满足以下条件
>
> - 若高层参数 *precoderGranularity* 等于 *sameAsREG-bundle*，则这些资源单元位于构成 UE 尝试译码的 PDCCH 的资源单元组内；
> - 若高层参数 *precoderGranularity* 等于 *allContiguousRBs*，则为 UE 尝试译码 PDCCH 的 CORESET 中连续资源块集合内的所有资源单元组。
>
> $k$ 的参考点为
>
> - 若 CORESET 由 PBCH 或 *PDCCH-ConfigCommon* IE 中的 *controlResourceSetZero* 字段配置，则为 CORESET 中编号最低的资源块的子载波 0；
> - 否则为公共资源块 0 的子载波 0。
>
> $l$ 为时隙内的 OFDM 符号号。
>
> 天线端口 $p = 2000$。
>
> 不尝试在某 CORESET 中检测 PDCCH 的 UE，不应对该 CORESET 中 DM-RS 的存在与否作任何假设。
>
> 在没有 CSI-RS 配置且未另行配置的情况下，UE 可假设 PDCCH DM-RS 与 SS/PBCH block 在多普勒频移、多普勒扩展、平均时延、时延扩展以及（适用时）空间接收参数方面是准共址的。

### 解读

#### RE 位置：$k = 12n + 4k' + 1$

$k'=0,1,2$ 代入得 RB 内偏移 **1, 5, 9** ——即每 4 个子载波放一个 DMRS，1/3 的密度。

```
k' :        0           1           2
k  :   12n+1       12n+5       12n+9
RB : │ · D · · │ · D · · │ · D · · │
       0 1 2 3   4 5 6 7   8 9 10 11
```

cuPHY 的判断极简：`(tid & 0x3) == 1` ——因为 1,5,9 就是 "mod 4 余 1"。

#### 参考点切换：本节最容易被忽略的坑

$k$ 从哪里开始数，取决于 CORESET 是不是 **CORESET#0**：

| CORESET 来源 | 参考点 | cuPHY |
|---|---|---|
| PBCH / *controlResourceSetZero* | CORESET 最低 RB 的子载波 0 | `coreset_type == 0` → `gold_start_bit = 0` |
| 其他（*ControlResourceSet* IE） | **CRB 0** 的子载波 0 | `coreset_type != 0` → `gold_start_bit = start_rb * 6` |

**为什么 CORESET#0 特殊**：UE 读 MIB 时**还不知道 CRB 0 在哪**（那要靠 SIB1 里的 `offsetToPointA`）。所以对 CORESET#0，序列只能从 CORESET 自身起点开始数——这是"自举"的必然要求。

`start_rb * 6` 里的 6 = 每 RB 3 个 DMRS × 每个 DMRS 消耗 2 bit Gold。

```c
// embed_pdcch_tf_signal.cu:221
uint32_t gold_start_bit = (coreset_type == 0) ? 0 : start_rb * 6;
```

#### QCL 那段

"PDCCH DM-RS 与 SSB 准共址"——这是 UE 侧的接收假设（可以用 SSB 的信道估计结果来辅助 PDCCH 解调），**与 gNB 发射链路无关**，cuPHY 不涉及。但它对 gNB 有隐含约束：CORESET#0 的波束应当与对应 SSB 的波束一致。

---

# 3. TS 38.211 通用函数（§5.1.3 / §5.2.1）

## 3.1 §5.1.3 QPSK

### 原文

> In case of QPSK modulation, pairs of bits, $b(2i), b(2i+1)$, are mapped to complex-valued modulation symbols $d(i)$ according to
>
> $$d(i) = \frac{1}{\sqrt{2}}\left[\left(1 - 2b(2i)\right) + j\left(1 - 2b(2i+1)\right)\right]$$

### 译文

> 对于 QPSK 调制，比特对 $b(2i), b(2i+1)$ 按下式映射为复值调制符号 $d(i)$：
>
> $$d(i) = \frac{1}{\sqrt{2}}\left[\left(1 - 2b(2i)\right) + j\left(1 - 2b(2i+1)\right)\right]$$

### 解读

$1-2b$ 把 $\{0,1\}$ 映射到 $\{+1,-1\}$，实虚部各取一个比特，$1/\sqrt{2}$ 做功率归一（使 $|d(i)|=1$）。

```
        Im
         ↑
  01 ●   │   ● 00        b(2i)  决定实部符号（0→+，1→−）
     -1/√2│ +1/√2        b(2i+1)决定虚部符号
    ──────┼──────→ Re
         │
  11 ●   │   ● 10
```

cuPHY（`embed_pdcch_tf_signal.cu:528`）：

```c
val.x = 0.70710678f * (1 - 2 * x) * beta_qam;   // 0.70710678 = 1/√2
val.y = 0.70710678f * (1 - 2 * y) * beta_qam;
```

`beta_qam` 就是 §7.3.2.5 的 $\beta_{PDCCH}$，直接乘进星座点。

---

## 3.2 §5.2.1 Pseudo-random sequence generation

### 原文

> Generic pseudo-random sequences are defined by a length-31 Gold sequence. The output sequence $c(n)$ of length $M_{PN}$, where $n = 0,1,\ldots,M_{PN}-1$, is defined by
>
> $$c(n) = \left(x_1(n+N_C) + x_2(n+N_C)\right) \bmod 2$$
> $$x_1(n+31) = \left(x_1(n+3) + x_1(n)\right) \bmod 2$$
> $$x_2(n+31) = \left(x_2(n+3) + x_2(n+2) + x_2(n+1) + x_2(n)\right) \bmod 2$$
>
> where $N_C = 1600$ and the first m-sequence $x_1(n)$ shall be initialized with $x_1(0) = 1, x_1(n) = 0, n = 1,2,\ldots,30$. The initialization of the second m-sequence, $x_2(n)$, is denoted by $c_{init} = \sum_{i=0}^{30} x_2(i) \cdot 2^i$ with the value depending on the application of the sequence.

### 译文

> 通用伪随机序列由长度为 31 的 Gold 序列定义。长度为 $M_{PN}$ 的输出序列 $c(n)$（$n = 0,1,\ldots,M_{PN}-1$）定义为
>
> $$c(n) = \left(x_1(n+N_C) + x_2(n+N_C)\right) \bmod 2$$
> $$x_1(n+31) = \left(x_1(n+3) + x_1(n)\right) \bmod 2$$
> $$x_2(n+31) = \left(x_2(n+3) + x_2(n+2) + x_2(n+1) + x_2(n)\right) \bmod 2$$
>
> 其中 $N_C = 1600$，第一个 m 序列 $x_1(n)$ 应初始化为 $x_1(0) = 1$，$x_1(n) = 0$（$n = 1,2,\ldots,30$）。第二个 m 序列 $x_2(n)$ 的初始化记为 $c_{init} = \sum_{i=0}^{30} x_2(i) \cdot 2^i$，其取值取决于该序列的应用场景。

### 解读

**Gold 序列 = 两条 m 序列逐位异或**。整个 NR 下行的加扰、DMRS、CSI-RS 全用这一个发生器，只靠 $c_{init}$ 区分。

```
x1: 固定初值 [1,0,0,...,0]  ──┐
    31 级 LFSR, x^31+x^3+1    ├─ XOR ─→ c(n)
x2: 初值 = c_init  ─────────┘
    31 级 LFSR, x^31+x^3+x^2+x+1
```

**两个关键设计**：

**1. $x_1$ 初值固定、$x_2$ 初值可变** —— 所以"选一条序列"等价于"选一个 $c_{init}$"。Gold 序列族的性质保证：不同 $c_{init}$ 产生的序列**互相关很低**，这正是小区间干扰随机化所需要的。

**2. $N_C = 1600$ 的丢弃** —— 前 1600 个输出**扔掉不用**。这是让 LFSR 充分"混合"，消除初值的结构性痕迹。

**比喻**：像洗牌。$c_{init}$ 是初始牌序，空转 1600 步相当于洗 1600 次——即使两副牌初始只差一张，洗完也毫不相干。

**cuPHY 的实现方式完全不同但等价**：`gold32(c_init, n)` 不做 1600 次迭代，而是用**状态转移矩阵的幂**直接跳到第 $n+1600$ 个状态，一次算出 32 bit。这是把 $O(N_C)$ 的串行递推变成 $O(1)$ 的查表+异或——GPU 上每个线程独立算自己那 32 bit，无依赖、完全并行：

```c
// genScramblingSeqKernel: 每个线程直接取第 tid*32 位开始的 32 bit
d_x_scramSeq_addr[offset + tid] = __brev(gold32(c_init, tid * 32));
```

---

# 4. TS 38.212 §5.1 CRC calculation

### 原文

> Denote the input bits to the CRC computation by $a_0, a_1, a_2, a_3, \ldots, a_{A-1}$, and the parity bits by $p_0, p_1, p_2, p_3, \ldots, p_{L-1}$, where $A$ is the size of the input sequence and $L$ is the number of parity bits. The parity bits are generated by one of the following cyclic generator polynomials:
>
> - $g_{CRC24A}(D) = [D^{24} + D^{23} + D^{18} + D^{17} + D^{14} + D^{11} + D^{10} + D^{7} + D^{6} + D^{5} + D^{4} + D^{3} + D + 1]$ for a CRC length $L = 24$;
> - $g_{CRC24B}(D) = [D^{24} + D^{23} + D^{6} + D^{5} + D + 1]$ for a CRC length $L = 24$;
> - $g_{CRC24C}(D) = [D^{24} + D^{23} + D^{21} + D^{20} + D^{17} + D^{15} + D^{13} + D^{12} + D^{8} + D^{4} + D^{2} + D + 1]$ for a CRC length $L = 24$;
> - $g_{CRC16}(D) = [D^{16} + D^{12} + D^{5} + 1]$ for a CRC length $L = 16$;
> - $g_{CRC11}(D) = [D^{11} + D^{10} + D^{9} + D^{5} + 1]$ for a CRC length $L = 11$;
> - $g_{CRC6}(D) = [D^{6} + D^{5} + 1]$ for a CRC length $L = 6$.
>
> The encoding is performed in a systematic form, which means that in GF(2), the polynomial:
>
> $$a_0 D^{A+L-1} + a_1 D^{A+L-2} + \ldots + a_{A-1}D^{L} + p_0 D^{L-1} + p_1 D^{L-2} + \ldots + p_{L-2}D^{1} + p_{L-1}$$
>
> yields a remainder equal to 0 when divided by the corresponding CRC generator polynomial.
>
> The bits after CRC attachment are denoted by $b_0, b_1, b_2, b_3, \ldots, b_{B-1}$, where $B = A + L$. The relation between $a_k$ and $b_k$ is:
>
> $$b_k = a_k \quad \text{for } k = 0,1,2,\ldots,A-1$$
> $$b_k = p_{k-A} \quad \text{for } k = A, A+1, A+2, \ldots, A+L-1.$$

### 译文

> 记 CRC 计算的输入比特为 $a_0, a_1, a_2, a_3, \ldots, a_{A-1}$，校验比特为 $p_0, p_1, p_2, p_3, \ldots, p_{L-1}$，其中 $A$ 是输入序列长度，$L$ 是校验比特数。校验比特由以下循环生成多项式之一生成：
>
> - $g_{CRC24A}(D) = [D^{24} + D^{23} + D^{18} + D^{17} + D^{14} + D^{11} + D^{10} + D^{7} + D^{6} + D^{5} + D^{4} + D^{3} + D + 1]$，CRC 长度 $L = 24$；
> - $g_{CRC24B}(D) = [D^{24} + D^{23} + D^{6} + D^{5} + D + 1]$，CRC 长度 $L = 24$；
> - $g_{CRC24C}(D) = [D^{24} + D^{23} + D^{21} + D^{20} + D^{17} + D^{15} + D^{13} + D^{12} + D^{8} + D^{4} + D^{2} + D + 1]$，CRC 长度 $L = 24$；
> - $g_{CRC16}(D) = [D^{16} + D^{12} + D^{5} + 1]$，CRC 长度 $L = 16$；
> - $g_{CRC11}(D) = [D^{11} + D^{10} + D^{9} + D^{5} + 1]$，CRC 长度 $L = 11$；
> - $g_{CRC6}(D) = [D^{6} + D^{5} + 1]$，CRC 长度 $L = 6$。
>
> 编码以**系统形式**进行，即在 GF(2) 上，多项式
>
> $$a_0 D^{A+L-1} + a_1 D^{A+L-2} + \ldots + a_{A-1}D^{L} + p_0 D^{L-1} + p_1 D^{L-2} + \ldots + p_{L-2}D^{1} + p_{L-1}$$
>
> 被相应的 CRC 生成多项式除时余数为 0。
>
> CRC 附着后的比特记为 $b_0, b_1, b_2, b_3, \ldots, b_{B-1}$，其中 $B = A + L$。$a_k$ 与 $b_k$ 的关系为：
>
> $$b_k = a_k \quad (k = 0,1,2,\ldots,A-1)$$
> $$b_k = p_{k-A} \quad (k = A, A+1, A+2, \ldots, A+L-1)$$

### 解读

DCI 用的是 **$g_{CRC24C}$**（由 §7.3.2 指定）。"系统形式"意思是**原始数据原封不动放前面，校验位追加在后面**——不像某些编码会把数据打散。

**cuPHY 常数的逐位验证**：`#define G_CRC_24_C 0x01B2B117`

```
0x01B2B117 = 0000 0001 1011 0010 1011 0001 0001 0111
                     ↑ D^24
低 24 bit: 1011 0010 1011 0001 0001 0111
bit:       23  21 20  17  15 13 12   8    4  2 1 0
           ↓   ↓  ↓   ↓   ↓  ↓  ↓    ↓    ↓  ↓ ↓ ↓
           1 0 1  1 0 0 1 0 1 0 1 1 0 0 0 1 ...
```

逐位展开得 $D^{23}+D^{21}+D^{20}+D^{17}+D^{15}+D^{13}+D^{12}+D^{8}+D^{4}+D^{2}+D+1$，加上 bit24 的 $D^{24}$ ——与 $g_{CRC24C}$ **完全吻合**。

**为什么 DCI 用 24C 而不是 24A**：24C 是专为**短块 + Polar**设计的，在 $A$ 很小（DCI 通常 20~140 bit）时误检概率更优；24A 是为 TB 级长块设计的。

---

# 5. TS 38.212 §5.3.1 Polar coding

## 5.1 §5.3.1 码长 $N$ 的确定

### 原文

> The bit sequence input for a given code block to channel coding is denoted by $c_0, c_1, c_2, c_3, \ldots, c_{K-1}$, where $K$ is the number of bits to encode. After encoding the bits are denoted by $d_0, d_1, d_2, \ldots, d_{N-1}$, where $N = 2^n$ and the value of $n$ is determined by the following:
>
> Denote by $E$ the rate matching output sequence length as given in Clause 5.4.1;
>
> If $E \le (9/8) \cdot 2^{(\lceil \log_2 E \rceil - 1)}$ and $K/E < 9/16$
>
> $\quad n_1 = \lceil \log_2 E \rceil - 1$;
>
> else
>
> $\quad n_1 = \lceil \log_2 E \rceil$;
>
> end if
>
> $R_{min} = 1/8$;
>
> $n_2 = \lceil \log_2 (K/R_{min}) \rceil$;
>
> $n = \max\{\min\{n_1, n_2, n_{max}\}, n_{min}\}$
>
> where $n_{min} = 5$.
>
> UE is not expected to be configured with $K + n_{PC} > E$, where $n_{PC}$ is the number of parity check bits defined in Clause 5.3.1.2.

### 译文

> 给定码块输入到信道编码的比特序列记为 $c_0, c_1, c_2, c_3, \ldots, c_{K-1}$，其中 $K$ 是待编码比特数。编码后的比特记为 $d_0, d_1, d_2, \ldots, d_{N-1}$，其中 $N = 2^n$，$n$ 值由下述过程确定：
>
> 记 $E$ 为 5.4.1 节给出的速率匹配输出序列长度；
>
> 若 $E \le (9/8) \cdot 2^{(\lceil \log_2 E \rceil - 1)}$ 且 $K/E < 9/16$
>
> $\quad n_1 = \lceil \log_2 E \rceil - 1$；
>
> 否则
>
> $\quad n_1 = \lceil \log_2 E \rceil$；
>
> 结束
>
> $R_{min} = 1/8$；
>
> $n_2 = \lceil \log_2 (K/R_{min}) \rceil$；
>
> $n = \max\{\min\{n_1, n_2, n_{max}\}, n_{min}\}$
>
> 其中 $n_{min} = 5$。
>
> UE 不应被配置成 $K + n_{PC} > E$，其中 $n_{PC}$ 是 5.3.1.2 节定义的校验比特数。

### 解读

这段在回答一个问题：**目标要发 $E$ 比特、有 $K$ 比特信息，Polar 码长 $N$ 该取多大？**

三个候选值取"夹逼"：

| 量 | 含义 | 直觉 |
|---|---|---|
| $n_1$ | 由 $E$ 决定的上界 | 码长别比要发的长太多（否则打孔过度）|
| $n_2$ | 由码率下限 $R_{min}=1/8$ 决定 | 码率别低于 1/8（否则纯浪费）|
| $n_{max}$ | 硬上限，DCI 取 **9** → $N \le 512$ | 复杂度封顶 |
| $n_{min}$ | 硬下限 **5** → $N \ge 32$ | 太短的 Polar 码没有极化增益 |

那个古怪的 $E \le (9/8)\cdot 2^{\lceil\log_2 E\rceil-1}$ 判断：意思是"**$E$ 只比下一档 2 的幂大一点点**"，此时宁可选小一档码长再重复，也不选大一档再大量打孔。

**cuPHY 实现的等价变形**（`polar_encoder.cu:1069`）：

```c
uint32_t nMin1CodedBits = roundUpToPow2_nTxBits / 2;          // 2^(⌈log2 E⌉-1)
constexpr float INFO_TX_BITS_RATIO_THD = (9.0f / 16.0f);
if((nTxBits > (9 * nMin1CodedBits) / 8) ||                    // E > (9/8)·2^(⌈log2E⌉-1)
   ((float)nInfoBits / (float)nTxBits >= INFO_TX_BITS_RATIO_THD))  // K/E ≥ 9/16
{
    nMin1CodedBits *= 2;                                       // → n1 = ⌈log2 E⌉
}
```

协议写的是 `if (条件A and 条件B) 取小`，cuPHY 写的是 `if (¬A or ¬B) 取大` ——**德摩根等价**。

后续三行也逐一对应：

```c
uint32_t nMin2CodedBits = roundUpToPow2Gpu(nInfoBits * MIN_CODE_RATE_INV);  // n2, R_min=1/8
uint32_t nCodedBits = min(nMin1CodedBits, nMin2CodedBits);
if(nCodedBits < N_MIN_CODED_BITS) nCodedBits = N_MIN_CODED_BITS;   // n_min = 5 → 32
if(nCodedBits > N_MAX_CODED_BITS) nCodedBits = N_MAX_CODED_BITS;   // n_max = 9 → 512
```

> **PDCCH 的实际取值**：$E = 2\cdot9\cdot6\cdot AL$，AL=1 时 $E=108$、AL=16 时 $E=1728$。
> 由于 $n_{max}=9$，AL≥8 时 $N$ 恒为 512，$E>N$ → 走**重复**分支。

---

## 5.2 §5.3.1.1 Interleaving

### 原文

> The bit sequence $c_0, c_1, c_2, c_3, \ldots, c_{K-1}$ is interleaved into bit sequence $c'_0, c'_1, c'_2, c'_3, \ldots, c'_{K-1}$ as follows:
>
> $$c'_k = c_{\Pi(k)},\ k = 0,1,\ldots,K-1$$
>
> where the interleaving pattern $\Pi(k)$ is given by the following:
>
> if $I_{IL} = 0$
>
> $\quad \Pi(k) = k$, $k = 0,1,\ldots,K-1$
>
> else
>
> $\quad k = 0$;
>
> $\quad$ for $m = 0$ to $K_{IL}^{max} - 1$
>
> $\quad\quad$ if $\Pi_{IL}^{max}(m) \ge K_{IL}^{max} - K$
>
> $\quad\quad\quad \Pi(k) = \Pi_{IL}^{max}(m) - (K_{IL}^{max} - K)$;
>
> $\quad\quad\quad k = k+1$;
>
> $\quad\quad$ end if
>
> $\quad$ end for
>
> end if
>
> where $\Pi_{IL}^{max}(m)$ is given by Table 5.3.1.1-1 and $K_{IL}^{max} = 164$.
>
> **Table 5.3.1.1-1: Interleaving pattern $\Pi_{IL}^{max}(m)$**
>
> | $m$ | $\Pi$ | $m$ | $\Pi$ | $m$ | $\Pi$ | $m$ | $\Pi$ | $m$ | $\Pi$ | $m$ | $\Pi$ |
> |---|---|---|---|---|---|---|---|---|---|---|---|
> | 0 | 0 | 28 | 67 | 56 | 122 | 84 | 68 | 112 | 33 | 140 | 38 |
> | 1 | 2 | 29 | 69 | 57 | 123 | 85 | 73 | 113 | 36 | 141 | 144 |
> | 2 | 4 | 30 | 70 | 58 | 126 | 86 | 78 | 114 | 44 | 142 | 39 |
> | 3 | 7 | 31 | 71 | 59 | 127 | 87 | 84 | 115 | 47 | 143 | 145 |
> | 4 | 9 | 32 | 72 | 60 | 129 | 88 | 90 | 116 | 64 | 144 | 40 |
> | 5 | 14 | 33 | 76 | 61 | 132 | 89 | 92 | 117 | 74 | 145 | 146 |
> | 6 | 19 | 34 | 77 | 62 | 134 | 90 | 94 | 118 | 79 | 146 | 41 |
> | 7 | 20 | 35 | 81 | 63 | 138 | 91 | 96 | 119 | 85 | 147 | 147 |
> | 8 | 24 | 36 | 82 | 64 | 139 | 92 | 99 | 120 | 97 | 148 | 148 |
> | 9 | 25 | 37 | 83 | 65 | 140 | 93 | 102 | 121 | 100 | 149 | 149 |
> | 10 | 26 | 38 | 87 | 66 | 1 | 94 | 105 | 122 | 103 | 150 | 150 |
> | 11 | 28 | 39 | 88 | 67 | 3 | 95 | 107 | 123 | 117 | 151 | 151 |
> | 12 | 31 | 40 | 89 | 68 | 5 | 96 | 109 | 124 | 125 | 152 | 152 |
> | 13 | 34 | 41 | 91 | 69 | 8 | 97 | 112 | 125 | 131 | 153 | 153 |
> | 14 | 42 | 42 | 93 | 70 | 10 | 98 | 114 | 126 | 136 | 154 | 154 |
> | 15 | 45 | 43 | 95 | 71 | 15 | 99 | 116 | 127 | 142 | 155 | 155 |
> | 16 | 49 | 44 | 98 | 72 | 21 | 100 | 121 | 128 | 12 | 156 | 156 |
> | 17 | 50 | 45 | 101 | 73 | 27 | 101 | 124 | 129 | 17 | 157 | 157 |
> | 18 | 51 | 46 | 104 | 74 | 29 | 102 | 128 | 130 | 23 | 158 | 158 |
> | 19 | 53 | 47 | 106 | 75 | 32 | 103 | 130 | 131 | 37 | 159 | 159 |
> | 20 | 54 | 48 | 108 | 76 | 35 | 104 | 133 | 132 | 48 | 160 | 160 |
> | 21 | 56 | 49 | 110 | 77 | 43 | 105 | 135 | 133 | 75 | 161 | 161 |
> | 22 | 58 | 50 | 111 | 78 | 46 | 106 | 141 | 134 | 80 | 162 | 162 |
> | 23 | 59 | 51 | 113 | 79 | 52 | 107 | 6 | 135 | 86 | 163 | 163 |
> | 24 | 61 | 52 | 115 | 80 | 55 | 108 | 11 | 136 | 137 | | |
> | 25 | 62 | 53 | 118 | 81 | 57 | 109 | 16 | 137 | 143 | | |
> | 26 | 65 | 54 | 119 | 82 | 60 | 110 | 22 | 138 | 13 | | |
> | 27 | 66 | 55 | 120 | 83 | 63 | 111 | 30 | 139 | 18 | | |

### 译文

> 比特序列 $c_0, c_1, \ldots, c_{K-1}$ 按下式交织为 $c'_0, c'_1, \ldots, c'_{K-1}$：
>
> $$c'_k = c_{\Pi(k)},\ k = 0,1,\ldots,K-1$$
>
> 其中交织图样 $\Pi(k)$ 由下述过程给出：
>
> 若 $I_{IL} = 0$
>
> $\quad \Pi(k) = k$（即不交织），$k = 0,1,\ldots,K-1$
>
> 否则
>
> $\quad k = 0$；
>
> $\quad$ 对 $m = 0$ 到 $K_{IL}^{max} - 1$
>
> $\quad\quad$ 若 $\Pi_{IL}^{max}(m) \ge K_{IL}^{max} - K$
>
> $\quad\quad\quad \Pi(k) = \Pi_{IL}^{max}(m) - (K_{IL}^{max} - K)$；
>
> $\quad\quad\quad k = k+1$；
>
> $\quad\quad$ 结束
>
> $\quad$ 结束
>
> 结束
>
> 其中 $\Pi_{IL}^{max}(m)$ 由表 5.3.1.1-1 给出，且 $K_{IL}^{max} = 164$。
>
> （表 5.3.1.1-1 数值同上表，为 164 项固定交织图样。）

### 解读

**这是一张"最大长度母表 + 剪裁"的经典设计**。

母表定义了 $K=164$ 时的交织顺序。当实际 $K<164$ 时，不另建表，而是**把母表中小于 $(164-K)$ 的值全部丢弃，剩下的减去 $(164-K)$** ——剪裁后自动得到一个合法的 $K$ 长置换。

**比喻**：一张 164 人的排队名单。今天只来了 $K$ 个人（编号从后往前保留），把没来的人从名单划掉，剩下的人按原相对顺序重新编号——顺序关系保持不变。

**为什么 DCI 必须交织（$I_{IL}=1$，见 §7.3.3）**：CRC 位在末尾，若不交织，CRC 全部落在 Polar 码的"高可靠位"或"低可靠位"的一端，会削弱 CA-SCL 译码器提前终止路径的能力。交织把信息位和 CRC 位**打散混合**，使译码器在译码过程中能持续用 CRC 做路径筛选。

**cuPHY 实现**（`polar_encoder.cu:328`）：

```c
int16_t interleaverTblStartIdx = N_MAX_INFO_BITS - nInfoBits;   // 164 - K
uint8_t bitInterleaverUlDlIdx = ... POLAR_ENC_INFO_BIT_INTERLEAVER_IDX[thrdIdxInBlk] ...;
bool validInterleaverIdx = (bitInterleaverUlDlIdx >= interleaverTblStartIdx);  // ≥ 164-K 才保留
```

`POLAR_ENC_INFO_BIT_INTERLEAVER_IDX` 的 164 项与 Table 5.3.1.1-1 **逐项一致**（已核对 m=0..163 全部数值）。`N_MAX_INFO_BITS = CUPHY_POLAR_ENC_MAX_INFO_BITS = 164` 即 $K_{IL}^{max}$。

代码里用**流压缩（stream compaction）**并行完成"丢弃 + 重编号"，避免了协议伪码里的串行 `k=k+1`。

---

## 5.3 §5.3.1.2 Polar encoding

### 原文

> The Polar sequence $\mathbf{Q}_0^{N_{max}-1} = \{Q_0^{N_{max}}, Q_1^{N_{max}}, \ldots, Q_{N_{max}-1}^{N_{max}}\}$ is given by Table 5.3.1.2-1, where $0 \le Q_i^{N_{max}} \le N_{max}-1$ denotes a bit index before Polar encoding for $i = 0,1,\ldots,N_{max}-1$ and $N_{max} = 1024$. The Polar sequence $\mathbf{Q}_0^{N_{max}-1}$ is in ascending order of reliability $W(Q_0^{N_{max}}) < W(Q_1^{N_{max}}) < \ldots < W(Q_{N_{max}-1}^{N_{max}})$, where $W(Q_i^{N_{max}})$ denotes the reliability of bit index $Q_i^{N_{max}}$.
>
> For any code block encoded to $N$ bits, a same Polar sequence $\mathbf{Q}_0^{N-1} = \{Q_0^N, Q_1^N, Q_2^N, \ldots, Q_{N-1}^N\}$ is used. The Polar sequence $\mathbf{Q}_0^{N-1}$ is a subset of Polar sequence $\mathbf{Q}_0^{N_{max}-1}$ with all elements $Q_i^{N_{max}}$ of values less than $N$, ordered in ascending order of reliability $W(Q_0^N) < W(Q_1^N) < W(Q_2^N) < \ldots < W(Q_{N-1}^N)$.
>
> Denote $\overline{\mathbf{Q}}_I^N$ as a set of bit indices in Polar sequence $\mathbf{Q}_0^{N-1}$, and $\overline{\mathbf{Q}}_F^N$ as the set of other bit indices in Polar sequence $\mathbf{Q}_0^{N-1}$, where $\overline{\mathbf{Q}}_I^N$ and $\overline{\mathbf{Q}}_F^N$ are given in Clause 5.4.1.1, $\left|\overline{\mathbf{Q}}_I^N\right| = K + n_{PC}$, $\left|\overline{\mathbf{Q}}_F^N\right| = N - \left|\overline{\mathbf{Q}}_I^N\right|$, and $n_{PC}$ is the number of parity check bits.
>
> Denote $\mathbf{G}_N = (\mathbf{G}_2)^{\otimes n}$ as the $n$-th Kronecker power of matrix $\mathbf{G}_2$, where $\mathbf{G}_2 = \begin{bmatrix} 1 & 0 \\ 1 & 1 \end{bmatrix}$.
>
> For a bit index $j$ with $j = 0,1,\ldots,N-1$, denote $\mathbf{g}_j$ as the $j$-th row of $\mathbf{G}_N$ and $w(\mathbf{g}_j)$ as the row weight of $\mathbf{g}_j$, where $w(\mathbf{g}_j)$ is the number of ones in $\mathbf{g}_j$. […PC 比特放置规则，$n_{PC}=0$ 时不适用，此处从略…]
>
> Generate $\mathbf{u} = [u_0\ u_1\ u_2\ \ldots\ u_{N-1}]$ according to the following:
>
> […伪码：信息位按 $\overline{\mathbf{Q}}_I^N$ 依次填入 $c'_k$，其余位置（frozen）置 0…]
>
> The output after encoding $\mathbf{d} = [d_0\ d_1\ d_2\ \ldots\ d_{N-1}]$ is obtained by $\mathbf{d} = \mathbf{u}\mathbf{G}_N$.

> **摘抄说明**：Table 5.3.1.2-1（Polar 可靠度序列）共 **1024 项**，横跨原文 4 页，
> 本文档不逐字转录。cuPHY 中以 5 张按 $N$ 截断的 LUT 形式存在：
> `POLAR_REL_SEQ_IDXS_{32,64,128,256,512}`（`polar_encoder.cu:63-160`），
> 分别对应 $\mathbf{Q}_0^{N-1}$（$N=32,64,128,256,512$），即"母表中值小于 $N$ 的元素按可靠度升序"。

### 译文

> Polar 序列 $\mathbf{Q}_0^{N_{max}-1} = \{Q_0^{N_{max}}, \ldots, Q_{N_{max}-1}^{N_{max}}\}$ 由表 5.3.1.2-1 给出，其中 $0 \le Q_i^{N_{max}} \le N_{max}-1$ 表示 Polar 编码前的比特索引（$i = 0,1,\ldots,N_{max}-1$），$N_{max} = 1024$。该序列按可靠度升序排列：$W(Q_0^{N_{max}}) < W(Q_1^{N_{max}}) < \ldots$，其中 $W(\cdot)$ 表示该比特索引的可靠度。
>
> 对于任何编码到 $N$ 比特的码块，使用同一个 Polar 序列 $\mathbf{Q}_0^{N-1}$。$\mathbf{Q}_0^{N-1}$ 是 $\mathbf{Q}_0^{N_{max}-1}$ 的**子集**——取其中所有取值小于 $N$ 的元素，并按可靠度升序排列。
>
> 记 $\overline{\mathbf{Q}}_I^N$ 为 Polar 序列中信息位索引集合，$\overline{\mathbf{Q}}_F^N$ 为其余（冻结位）索引集合，二者在 5.4.1.1 节给出，且 $\left|\overline{\mathbf{Q}}_I^N\right| = K + n_{PC}$，$\left|\overline{\mathbf{Q}}_F^N\right| = N - \left|\overline{\mathbf{Q}}_I^N\right|$，$n_{PC}$ 为校验比特数。
>
> 记 $\mathbf{G}_N = (\mathbf{G}_2)^{\otimes n}$ 为矩阵 $\mathbf{G}_2$ 的 $n$ 次 Kronecker 幂，其中 $\mathbf{G}_2 = \begin{bmatrix} 1 & 0 \\ 1 & 1 \end{bmatrix}$。
>
> 生成 $\mathbf{u} = [u_0\ u_1\ \ldots\ u_{N-1}]$：信息位按 $\overline{\mathbf{Q}}_I^N$ 顺序填入，其余位置置 0。
>
> 编码输出 $\mathbf{d} = \mathbf{u}\mathbf{G}_N$。

### 解读

Polar 码的全部思想在一句话里：**同一个信道用 $\mathbf{G}_N$ 变换后，会"极化"成一批极好的子信道和一批极差的子信道**。把信息放在好的位置（$\overline{\mathbf{Q}}_I$），差的位置固定填 0（frozen，收发双方都知道）。

$\mathbf{G}_2$ 的 Kronecker 幂展开就是**蝶形网络**——和 FFT 结构同构：

```
u0 ──⊕──────⊕───── d0        每一级：上路 = 上⊕下，下路 = 下
     │      │
u1 ──┼──⊕───┼──⊕── d1        n = log2(N) 级
     │  │   │  │
u2 ──⊕──┼───⊕──┼── d2        总运算量 O(N log N)
        │      │
u3 ─────⊕─────⊕─── d3
```

**可靠度序列为什么能"截断复用"**：Polar 码有个漂亮性质——$N=512$ 的可靠度排序，就是 $N=1024$ 母序列里所有 $<512$ 的元素保持相对顺序。所以协议只需给一张 1024 项的表，所有码长共用。

cuPHY 直接存了 5 张截断好的表（用空间换时间，避免运行时过滤）：

```c
static __device__ __constant__ uint16_t const* POLAR_REL_SEQ_IDXS_LUT_PTR[] = {
   POLAR_REL_SEQ_IDXS_32,  POLAR_REL_SEQ_IDXS_64,  POLAR_REL_SEQ_IDXS_128,
   POLAR_REL_SEQ_IDXS_256, POLAR_REL_SEQ_IDXS_512
};
uint32_t relSeqLutIdx = __ffs(nCodedBits) - __ffs(N_MIN_CODED_BITS);  // 由 N 选表
```

> **DCI 路径下 $n_{PC} = 0$**（§7.3.3 规定），所以整段 PC 比特放置规则**不适用**——
> cuPHY 的 `encodeRateMatchMultipleDCIsKernel` 传入 `procModeBmsk = 0`，走的正是无 PC 分支。
> PC 比特只在**上行** UCI Polar 中使用。

---

# 6. TS 38.212 §5.4.1 Rate matching for Polar code

## 6.1 §5.4.1.1 Sub-block interleaving

### 原文

> The bit sequence $d_0, d_1, \ldots, d_{N-1}$ is divided into 32 sub-blocks and interleaved, resulting in bit sequence $y_0, y_1, \ldots, y_{N-1}$ as follows:
>
> for $n = 0$ to $N-1$
>
> $\quad i = \lfloor 32n/N \rfloor$;
>
> $\quad J(n) = P(i) \times (N/32) + \bmod(n, N/32)$;
>
> $\quad y_n = d_{J(n)}$;
>
> end for
>
> where the sub-block interleaver pattern $P(i)$ is given by Table 5.4.1.1-1.
>
> **Table 5.4.1.1-1: Sub-block interleaver pattern $P(i)$**
>
> | $i$ | $P(i)$ | $i$ | $P(i)$ | $i$ | $P(i)$ | $i$ | $P(i)$ |
> |---|---|---|---|---|---|---|---|
> | 0 | 0 | 8 | 8 | 16 | 12 | 24 | 24 |
> | 1 | 1 | 9 | 16 | 17 | 20 | 25 | 25 |
> | 2 | 2 | 10 | 9 | 18 | 13 | 26 | 26 |
> | 3 | 4 | 11 | 17 | 19 | 21 | 27 | 28 |
> | 4 | 3 | 12 | 10 | 20 | 14 | 28 | 27 |
> | 5 | 5 | 13 | 18 | 21 | 22 | 29 | 29 |
> | 6 | 6 | 14 | 11 | 22 | 15 | 30 | 30 |
> | 7 | 7 | 15 | 19 | 23 | 23 | 31 | 31 |

### 译文

> 比特序列 $d_0, d_1, \ldots, d_{N-1}$ 被划分为 **32 个子块**并交织，得到序列 $y_0, y_1, \ldots, y_{N-1}$：
>
> 对 $n = 0$ 到 $N-1$
>
> $\quad i = \lfloor 32n/N \rfloor$；（当前比特属于第 $i$ 个子块）
>
> $\quad J(n) = P(i) \times (N/32) + \bmod(n, N/32)$；
>
> $\quad y_n = d_{J(n)}$；
>
> 结束
>
> 其中子块交织图样 $P(i)$ 由表 5.4.1.1-1 给出（数值同上表）。

### 解读

**注意粒度：交织的是"子块"，不是单个比特。** 把 $N$ 个比特切成 32 段，每段 $N/32$ 个比特，然后**整段整段地重排**，段内顺序不变。

```
原序列 d:  [块0][块1][块2][块3][块4]...[块31]
                      ↓ 按 P(i) 重排
新序列 y:  [块0][块1][块2][块4][块3]...[块31]
                        ↑ P(3)=4, P(4)=3 → 第3、4块交换
```

**为什么要它**：为下一步的"比特选择"服务。打孔/缩短是**从序列头部或尾部整块砍掉**的，子块交织决定了"砍掉哪些位置的编码比特"能让性能最优——这张 $P(i)$ 表是仿真优化出来的经验值。

**cuPHY 实现**（`polar_encoder.cu:885`，注释直接点名了本节）：

```c
uint32_t scaleFactor = nCodedBits / 32;              // N/32
uint32_t interleaverTblIdx = thrdIdxInBlk / scaleFactor;   // i = ⌊32n/N⌋
uint32_t interleavedBitPos = (POLAR_ENC_CODED_BIT_INTERLEAVER_IDX[interleaverTblIdx] * scaleFactor)
                             + (thrdIdxInBlk % scaleFactor);   // J(n)
```

`POLAR_ENC_CODED_BIT_INTERLEAVER_IDX` 的 32 项与 Table 5.4.1.1-1 **完全一致**：

```c
0,  1,  2,  4,  3,  5,  6,  7,  8, 16,  9, 17, 10, 18, 11, 19,
12, 20, 13, 21, 14, 22, 15, 23, 24, 25, 26, 28, 27, 29, 30, 31
```

---

## 6.2 §5.4.1.2 Bit selection

### 原文

> The bit sequence after the sub-block interleaver $y_0, y_1, y_2, \ldots, y_{N-1}$ from Clause 5.4.1.1 is written into a circular buffer of length $N$.
>
> Denoting by $E$ the rate matching output sequence length, the bit selection output bit sequence $e_k$, $k = 0,1,2,\ldots,E-1$, is generated as follows:
>
> if $E \ge N$  -- repetition
>
> $\quad$ for $k = 0$ to $E-1$
>
> $\quad\quad e_k = y_{\bmod(k,N)}$;
>
> $\quad$ end for
>
> else
>
> $\quad$ if $K/E \le 7/16$  -- puncturing
>
> $\quad\quad$ for $k = 0$ to $E-1$
>
> $\quad\quad\quad e_k = y_{k+N-E}$;
>
> $\quad\quad$ end for
>
> $\quad$ else  -- shortening
>
> $\quad\quad$ for $k = 0$ to $E-1$
>
> $\quad\quad\quad e_k = y_k$;
>
> $\quad\quad$ end for
>
> $\quad$ end if
>
> end if

### 译文

> 5.4.1.1 节输出的比特序列 $y_0, y_1, \ldots, y_{N-1}$ 被写入长度为 $N$ 的**循环缓冲区**。
>
> 记 $E$ 为速率匹配输出序列长度，比特选择输出 $e_k$（$k = 0,1,\ldots,E-1$）按下述方式生成：
>
> 若 $E \ge N$  —— **重复**
>
> $\quad e_k = y_{\bmod(k,N)}$（绕回循环缓冲区重复读取）
>
> 否则
>
> $\quad$ 若 $K/E \le 7/16$  —— **打孔**
>
> $\quad\quad e_k = y_{k+N-E}$（从缓冲区**尾部**取 $E$ 个，即丢弃开头 $N-E$ 个）
>
> $\quad$ 否则  —— **缩短**
>
> $\quad\quad e_k = y_k$（从缓冲区**头部**取 $E$ 个，即丢弃末尾 $N-E$ 个）
>
> $\quad$ 结束
>
> 结束

### 解读

三种情况，一张图说清：

```
循环缓冲区 y (长度 N)：
┌─────────────────────────────────────┐
│  y0  y1  y2  ...              y(N-1)│
└─────────────────────────────────────┘

E ≥ N  重复:    读完一圈再绕回来接着读
                ├────── N ──────┤├── E-N ──┤

E < N  打孔:    丢弃头部            ┌── 取这段 (E) ──┐
                ├── N-E ──┤━━━━━━━━━━━━━━━━━━━━━━━━━┤

E < N  缩短:    ┌── 取这段 (E) ──┐            丢弃尾部
                ├━━━━━━━━━━━━━━━━┤├──── N-E ────────┤
```

**打孔 vs 缩短的区别**（容易混）：

| | 丢弃位置 | 译码器怎么处理丢失的比特 | 何时用 |
|---|---|---|---|
| **打孔** puncturing | 缓冲区**开头** | LLR 置 **0**（完全未知）| 码率低（$K/E \le 7/16$）|
| **缩短** shortening | 缓冲区**末尾** | LLR 置 **+∞**（已知为 0）| 码率高 |

缩短之所以能置 $+\infty$，是因为子块交织的设计使得被砍掉的尾部位置恰好对应的编码比特恒为 0——**接收端知道它们是什么**，所以是"免费"的信息。

**PDCCH 的实际情况**：$N \le 512$（$n_{max}=9$），而 $E = 108 \cdot AL$。

| AL | $E$ | $N$ | 分支 |
|---|---|---|---|
| 1 | 108 | 128 或更小 | 打孔 / 缩短 |
| 2 | 216 | 256 | 视 $K$ 而定 |
| 4 | 432 | 512 | 视 $K$ 而定 |
| 8 | 864 | 512 | **重复**（$E>N$）|
| 16 | 1728 | 512 | **重复**（$E$ 是 $N$ 的 3.375 倍）|

cuPHY 的 `rateMatch()` 里对重复分支用 `atomicAnd` 处理多个源位写同一目标——这也是 `pdcch_tx.cpp` 里要 `memset(d_x_tx_bytes, 0, ...)` 的原因（见该文件 `MEMSET_IN_FIRST_SETUP` 注释）。

---

## 6.3 §5.4.1.3 Interleaving of coded bits

### 原文

> The bit sequence $e_0, e_1, e_2, \ldots, e_{E-1}$ is interleaved into bit sequence $f_0, f_1, f_2, \ldots, f_{E-1}$, as follows:
>
> If $I_{BIL} = 1$
>
> $\quad$ Denote $T$ as the smallest integer such that $T(T+1)/2 \ge E$;
>
> $\quad$ […三角交织伪码…]
>
> else
>
> $\quad f_k = e_k$, $k = 0, 1, \ldots, E-1$
>
> end if

### 译文

> 比特序列 $e_0, \ldots, e_{E-1}$ 交织为 $f_0, \ldots, f_{E-1}$：
>
> 若 $I_{BIL} = 1$，执行三角交织（$T$ 为满足 $T(T+1)/2 \ge E$ 的最小整数）；
>
> 否则 $f_k = e_k$（直通，不交织）。

### 解读

**DCI 路径下 $I_{BIL} = 0$（§7.3.4 明确规定），本节等价于直通。**

cuPHY 中**没有对应实现**——不是遗漏，是协议就不要求。三角交织（$I_{BIL}=1$）只用于**上行 UCI** 的 Polar 编码，用来对抗上行的突发干扰。

---

# 7. TS 38.212 §7.3 Downlink control information

## 7.1 §7.3.2 CRC attachment

### 原文

> Error detection is provided on DCI transmissions through a Cyclic Redundancy Check (CRC).
>
> The entire payload is used to calculate the CRC parity bits. Denote the bits of the payload by $a_0, a_1, a_2, a_3, \ldots, a_{A-1}$, and the parity bits by $p_0, p_1, p_2, p_3, \ldots, p_{L-1}$, where $A$ is the payload size and $L$ is the number of parity bits. Let $a'_0, a'_1, a'_2, a'_3, \ldots, a'_{A+L-1}$ be a bit sequence such that $a'_i = 1$ for $i = 0,1,\ldots,L-1$ and $a'_i = a_{i-L}$ for $i = L, L+1, \ldots, A+L-1$. The parity bits are computed with input bit sequence $a'_0, a'_1, a'_2, a'_3, \ldots, a'_{A+L-1}$ and attached according to Clause 5.1 by setting $L$ to 24 bits and using the generator polynomial $g_{CRC24C}(D)$. The output bit $b_0, b_1, b_2, b_3, \ldots, b_{K-1}$ is
>
> $$b_k = a_k \quad \text{for } k = 0,1,2,\ldots,A-1$$
> $$b_k = p_{k-A} \quad \text{for } k = A, A+1, A+2, \ldots, A+L-1,$$
>
> where $K = A + L$.
>
> After attachment, the CRC parity bits are scrambled with the corresponding RNTI $x_{rnti,0}, x_{rnti,1}, \ldots, x_{rnti,15}$, where $x_{rnti,0}$ corresponds to the MSB of the RNTI, to form the sequence of bits $c_0, c_1, c_2, c_3, \ldots, c_{K-1}$. The relation between $c_k$ and $b_k$ is:
>
> $$c_k = b_k \quad \text{for } k = 0, 1, 2, \ldots, A+7$$
> $$c_k = \left(b_k + x_{rnti,k-A-8}\right) \bmod 2 \quad \text{for } k = A+8, A+9, A+10, \ldots, A+23.$$

### 译文

> DCI 传输通过循环冗余校验（CRC）提供错误检测。
>
> **整个负荷**用于计算 CRC 校验比特。记负荷比特为 $a_0, \ldots, a_{A-1}$，校验比特为 $p_0, \ldots, p_{L-1}$，其中 $A$ 是负荷大小，$L$ 是校验比特数。令 $a'_0, a'_1, \ldots, a'_{A+L-1}$ 为这样一个比特序列：$a'_i = 1$（$i = 0,1,\ldots,L-1$），且 $a'_i = a_{i-L}$（$i = L, \ldots, A+L-1$）。即**在负荷前面预置 $L$ 个 1**。校验比特以 $a'_0, \ldots, a'_{A+L-1}$ 为输入按 5.1 节计算，取 $L = 24$ 比特并使用生成多项式 $g_{CRC24C}(D)$。输出比特 $b_0, \ldots, b_{K-1}$ 为
>
> $$b_k = a_k \quad (k = 0,1,\ldots,A-1)$$
> $$b_k = p_{k-A} \quad (k = A, A+1, \ldots, A+L-1)$$
>
> 其中 $K = A + L$。
>
> 附着之后，CRC 校验比特与相应的 RNTI $x_{rnti,0}, \ldots, x_{rnti,15}$ 加扰（$x_{rnti,0}$ 对应 RNTI 的 MSB），形成比特序列 $c_0, \ldots, c_{K-1}$。$c_k$ 与 $b_k$ 的关系为：
>
> $$c_k = b_k \quad (k = 0, 1, \ldots, A+7)$$
> $$c_k = \left(b_k + x_{rnti,k-A-8}\right) \bmod 2 \quad (k = A+8, A+9, \ldots, A+23)$$

### 解读

本节有**两个非常规操作**，cuPHY 都精确实现了：

#### (1) 前置 24 个 1

$a'_i = 1$ for $i<L$ ——在负荷前面**虚拟地加上 24 个 1** 再算 CRC（这 24 个 1 不发送）。等价于把 CRC 寄存器初值设为全 1，作用是**让全 0 负荷也能产生非零 CRC**，避免"全 0 数据 + 全 0 CRC"这种退化情形无法检错。

```c
// embed_pdcch_tf_signal.cu:110  include_crc_ones=false → 内部补 24 bit 的 0xFF
int extra_bits = include_crc_ones ? 0 : uintCRCBitLength;   // = 24
input_val = (i < (extra_bytes * stride)) ? 0xffu : input[i - extra_bytes * stride];
```

#### (2) 只加扰 CRC 的后 16 bit

$k$ 从 $A+8$ 到 $A+23$ ——即 24 bit CRC 里，**前 8 bit 不动，后 16 bit 与 RNTI 异或**。

```
        24 bit CRC
┌────────┬────────────────┐
│ 8 bit  │    16 bit      │
│  原样  │  ⊕ RNTI        │
└────────┴────────────────┘
```

```c
// embed_pdcch_tf_signal.cu:165
dci_crc ^= (rnti_crc & 0x0FFFFU);    // 低 16 bit 异或 RNTI
```

**为什么这么设计（关键）**：这就是 PDCCH **盲检测**的机制核心。gNB 不告诉 UE"这条 DCI 是给你的"——UE 拿自己的 RNTI 去反向解扰 CRC，**只有 RNTI 匹配时 CRC 才校验通过**。CRC 在这里身兼二职：

1. 错误检测（本职）
2. **隐式寻址**（用 RNTI 加扰实现"寄信人地址"）

留 8 bit 不加扰，是为了保证即使 RNTI 猜错，仍有 $2^{-8}$ 量级的额外保护，降低虚警率。

---

## 7.2 §7.3.3 Channel coding

### 原文

> Information bits are delivered to the channel coding block. They are denoted by $c_0, c_1, c_2, c_3, \ldots, c_{K-1}$, where $K$ is the number of bits, and they are encoded via Polar coding according to Clause 5.3.1, by setting $n_{max} = 9$, $I_{IL} = 1$, $n_{PC} = 0$, and $n_{PC}^{wm} = 0$.
>
> After encoding the bits are denoted by $d_0, d_1, d_2, d_3, \ldots, d_{N-1}$, where $N$ is the number of coded bits.

### 译文

> 信息比特送入信道编码模块，记为 $c_0, c_1, \ldots, c_{K-1}$，其中 $K$ 是比特数。它们按 5.3.1 节通过 Polar 编码，参数取 $n_{max} = 9$、$I_{IL} = 1$、$n_{PC} = 0$、$n_{PC}^{wm} = 0$。
>
> 编码后的比特记为 $d_0, d_1, \ldots, d_{N-1}$，其中 $N$ 是编码比特数。

### 解读

短短一句，把 §5.3.1 那套通用 Polar 流程的**四个开关全部锁死**：

| 参数 | 值 | 后果 |
|---|---|---|
| $n_{max} = 9$ | $N \le 2^9 = 512$ | cuPHY `N_MAX_CODED_BITS = 512` |
| $I_{IL} = 1$ | **必须**做 §5.3.1.1 的输入交织 | cuPHY 恒走交织分支 |
| $n_{PC} = 0$ | 无 PC 比特 | `procModeBmsk = 0` |
| $n_{PC}^{wm} = 0$ | 无行重 PC 比特 | 同上 |

对照上行 UCI：$n_{max}=10$、$n_{PC}$ 可为 3——所以**同一个 cuPHY Polar 编码器要同时服务两条路径**，靠 `procModeBmsk` 区分。DCI 侧固定传 0。

$K = A + 24$，且 cuPHY 限定 $K \le 164$（`CUPHY_POLAR_ENC_MAX_INFO_BITS`），即 DCI 负荷 $A \le 140$ bit。

---

## 7.3 §7.3.4 Rate matching

### 原文

> The input bit sequence to rate matching is $d_0, d_1, d_2, \ldots, d_{N-1}$.
>
> Rate matching is performed according to Clause 5.4.1 by setting $I_{BIL} = 0$.
>
> The output bit sequence after rate matching is denoted as $f_0, f_1, f_2, \ldots, f_{E-1}$.

### 译文

> 速率匹配的输入比特序列为 $d_0, d_1, \ldots, d_{N-1}$。
>
> 速率匹配按 5.4.1 节执行，参数取 $I_{BIL} = 0$。
>
> 速率匹配后的输出比特序列记为 $f_0, f_1, \ldots, f_{E-1}$。

### 解读

$I_{BIL} = 0$ → §5.4.1.3 的三角交织**不执行**，$f_k = e_k$ 直通。

$E$ 由 PDCCH 的资源量决定（§7.3.2.5 的 RE 数 × 2 bit/RE）：

$$E = M_{bit} = 2 \times 9 \times 6 \times AL$$

## 7.4 §7.3 DCI 完整数据流（小结）

```
 MAC 给的 DCI payload  a_0..a_{A-1}         A ≤ 140 bit
        │
        │ §7.3.2  前置 24 个 1 → 算 gCRC24C → 附加 24 bit
        ▼
    b_0..b_{K-1}                            K = A + 24 ≤ 164
        │
        │ §7.3.2  CRC 后 16 bit ⊕ RNTI
        ▼
    c_0..c_{K-1}                            ← cuPHY: h_input_w_crc_bytes
        │
        │ §5.3.1.1  输入交织 (I_IL=1, Table 5.3.1.1-1)
        │ §5.3.1.2  子信道分配 + d = u·G_N   (N ≤ 512, n_PC=0)
        ▼
    d_0..d_{N-1}                            ← cuPHY: d_x_coded_bytes
        │
        │ §5.4.1.1  32 子块交织 (Table 5.4.1.1-1)
        │ §5.4.1.2  比特选择：重复 / 打孔 / 缩短
        │ §5.4.1.3  I_BIL=0，直通
        ▼
    f_0..f_{E-1}                            E = 108·AL   ← cuPHY: d_x_tx_bytes
        │
        │ 38.211 §7.3.2.3  ⊕ Gold(c_init)
        │ 38.211 §7.3.2.4  QPSK
        │ 38.211 §7.3.2.5  映射到 RE（跳过 DMRS）
        ▼
     TF 网格
```

上述流程在 cuPHY 中的分工：

| 阶段 | 位置 | 执行 |
|---|---|---|
| CRC + RNTI 加扰 + 位序翻转 | `cuphyPdcchPipelinePrepare()` | **CPU**（setup 阶段）|
| Polar 编码 + 速率匹配 | `encodeRateMatchMultipleDCIsKernel` | GPU，1 block/DCI |
| 加扰序列生成 | `genScramblingSeqKernel` | GPU，1 block/DCI |
| 加扰 + QPSK + DMRS + RE 映射 | `genPdcchTfSignalKernel` | GPU，1 block/(DCI×符号) |

---

# 8. TS 38.214 §4.1 Power allocation for downlink

### 原文（与 PDCCH 相关的段落）

> The gNB determines the downlink transmit EPRE.
>
> For the purpose of SS-RSRP, SS-RSRQ and SS-SINR measurements, the UE may assume downlink EPRE is constant across the bandwidth. For the purpose of SS-RSRP, SS-RSRQ and SS-SINR measurements, the UE may assume downlink EPRE is constant over SSS carried in different SS/PBCH blocks. For the purpose of SS-RSRP, SS-RSRQ and SS-SINR measurements, the UE may assume that the ratio of SSS EPRE to PBCH DM-RS EPRE is 0 dB.
>
> […CSI-RSRP / CSI-RSRQ / CSI-SINR 段落…]
>
> The downlink SS/PBCH SSS EPRE can be derived from the SS/PBCH downlink transmit power given by the parameter *ss-PBCH-BlockPower* provided by higher layers. The downlink SSS transmit power is defined as the linear average over the power contributions (in [W]) of all resource elements that carry the SSS within the operating system bandwidth.
>
> […CSI-RS EPRE 推导段落…]
>
> For downlink DM-RS associated with PDSCH, the UE may assume the ratio of PDSCH EPRE to DM-RS EPRE ($\beta_{DMRS}$ [dB]) is given by Table 4.1-1 according to the number of DM-RS CDM groups without data as described in Clause 5.1.6.2. The DM-RS scaling factor $\beta_{PDSCH}^{DMRS}$ specified in Clause 7.4.1.1.2 of [4, TS 38.211] is given by
>
> $$\beta_{PDSCH}^{DMRS} = 10^{-\frac{\beta_{DMRS}}{20}}.$$

### 译文

> gNB 决定下行发射 EPRE。
>
> 为进行 SS-RSRP、SS-RSRQ 和 SS-SINR 测量，UE 可假设下行 EPRE 在整个带宽上恒定；可假设下行 EPRE 在不同 SS/PBCH block 承载的 SSS 上恒定；可假设 SSS EPRE 与 PBCH DM-RS EPRE 之比为 0 dB。
>
> 下行 SS/PBCH 的 SSS EPRE 可由高层提供的参数 *ss-PBCH-BlockPower* 给出的 SS/PBCH 下行发射功率推导。下行 SSS 发射功率定义为工作系统带宽内所有承载 SSS 的资源单元功率贡献（单位 W）的线性平均。
>
> 对于与 PDSCH 关联的下行 DM-RS，UE 可假设 PDSCH EPRE 与 DM-RS EPRE 之比（$\beta_{DMRS}$ [dB]）由表 4.1-1 根据无数据的 DM-RS CDM 组数量给出。38.211 的 7.4.1.1.2 节规定的 DM-RS 缩放因子 $\beta_{PDSCH}^{DMRS}$ 为
>
> $$\beta_{PDSCH}^{DMRS} = 10^{-\frac{\beta_{DMRS}}{20}}.$$

### 解读

> **⚠️ 重要提醒：本节其实没有直接定义 $\beta_{PDCCH}$。**

通读 §4.1 会发现它只覆盖了 **SS/PBCH、CSI-RS、PDSCH DM-RS、PT-RS** 的 EPRE 关系——**PDCCH 的功率在 3GPP 物理层规范里没有显式公式**。

它对 PDCCH 的意义是**间接的、但不可或缺的**，提供了两块基石：

**1. EPRE 这个基准量本身**，以及 *ss-PBCH-BlockPower* 作为全小区的功率锚点。所有其他信道的功率都是相对 SSB EPRE 的偏移。

**2. $\beta = 10^{(\cdot)/20}$ 这个 dB→线性幅度的换算范式**（注意分母是 20 不是 10，因为 $\beta$ 乘的是**幅度**而非功率）。

PDCCH 的实际功率约束散落在两处：

| 出处 | 内容 |
|---|---|
| **38.213 §4.1** | "UE 可假设 PDCCH DMRS EPRE 与 SSS EPRE 之比在 **−8 dB 到 8 dB** 之间"（针对 SI-RNTI/P-RNTI/RA-RNTI 加扰的 DCI format 1_0）|
| **SCF FAPI Table 3.4.2.1-2** | `powerControlOffsetSS-ProfileNR`，取值 −8…8 dB，1 dB 步进；−127 表示用 ProfileSSS |

**cuPHY 的换算**（`scf_5g_slot_commands_pdcch.cpp:169`）——注意两个 FAPI 版本公式不同：

```c
#ifdef SCF_FAPI_10_04
    dci.beta_qam  = std::pow(10.0, (pwr_info.power_control_offset_ss_profile_nr / 20.0));
    dci.beta_dmrs = std::pow(10.0, (pwr_info.power_control_offset_ss_profile_nr / 20.0));
#else
    dci.beta_qam  = std::pow(10.0, (pwr_info.power_control_offset_ss - 1) * 3.0 / 20.0);
    dci.beta_dmrs = std::pow(10.0, (pwr_info.power_control_offset_ss - 1) * 3.0 / 20.0);
#endif
```

- **10.04 起**：字段是有符号 dB 值，直接 $10^{v/20}$
- **10.04 之前**：字段是 3 dB 步进的枚举，需先换算成 dB（$(v-1)\times 3$）

两条分支都体现了 §4.1 的 $/20$ 范式。

> **cuPHY 对数据和 DMRS 用了同一个 $\beta$** ——这与 §7.3.2.5（$\beta_{PDCCH}$）和 §7.4.1.3.2（$\beta_{DMRS}^{PDCCH}$）在协议里是两个独立符号的事实一致：
> FAPI 只给一个 `powerControlOffsetSS`，因此二者恒等，即 PDCCH 数据与其 DMRS **等功率**。

---

# 9. TS 38.213 §10 / §12 / §13

> **摘抄范围说明**：§10 与 §13 各约 20 页，全部是 **UE 侧监听/判定过程**，cuPHY 发射链路不实现
> （见 `spec_list.md` 的 ⚠️ 表）。本章只 1:1 摘抄**直接决定 cuPHY 输入字段语义**的段落，
> 其余部分说明其归属，不逐字转录。

## 9.1 §10.1 CCE 索引与 PDCCH 候选（`cce_index` 的来源）

### 原文

> A USS at CCE aggregation level $L \in \{1,2,4,8,16\}$ is defined by a set of PDCCH candidates for CCE aggregation level $L$.
>
> […跨载波调度、carrier indicator field 相关段落…]
>
> For a search space set $s$ associated with CORESET $p$, the CCE indexes for aggregation level $L$ corresponding to PDCCH candidate $m_{s,n_{CI}}^{(L)}$ of the search space set in slot $n_{s,f}^{\mu}$ for an active DL BWP of a serving cell corresponding to carrier indicator field value $n_{CI}$ are given by
>
> $$L \cdot \left\{ \left( Y_{p,n_{s,f}^{\mu}} + \left\lfloor \frac{m_{s,n_{CI}}^{(L)} \cdot N_{CCE,p}}{L \cdot M_{s,max}^{(L)}} \right\rfloor + n_{CI} \right) \bmod \left\lfloor N_{CCE,p}/L \right\rfloor \right\} + i$$
>
> where
>
> for any CSS, $Y_{p,n_{s,f}^{\mu}} = 0$;
>
> for a USS, $Y_{p,n_{s,f}^{\mu}} = \left(A_p \cdot Y_{p,n_{s,f}^{\mu}-1}\right) \bmod D$, $Y_{p,-1} = n_{RNTI} \ne 0$, $A_p = 39827$ for $p \bmod 3 = 0$, $A_p = 39829$ for $p \bmod 3 = 1$, $A_p = 39839$ for $p \bmod 3 = 2$, and $D = 65537$;
>
> $i = 0, \cdots, L-1$;
>
> $N_{CCE,p}$ is the number of CCEs, numbered from 0 to $N_{CCE,p}-1$, in CORESET $p$ and, if any, per RB set;
>
> $n_{CI}$ is the carrier indicator field value if the UE is configured with a carrier indicator field by *CrossCarrierSchedulingConfig* for the serving cell on which PDCCH is monitored, except for scheduling of the serving cell from the same serving cell in which case $n_{CI} = 0$; otherwise, including for any CSS, $n_{CI} = 0$;
>
> $m_{s,n_{CI}}^{(L)} = 0, \cdots, M_{s,n_{CI}}^{(L)} - 1$, where $M_{s,n_{CI}}^{(L)}$ is the number of PDCCH candidates the UE is configured to monitor for aggregation level $L$ of a search space set $s$ for a serving cell corresponding to $n_{CI}$;
>
> for any CSS, $M_{s,max}^{(L)} = M_{s,0}^{(L)}$;
>
> for a USS, $M_{s,max}^{(L)}$ is the maximum of $M_{s,n_{CI}}^{(L)}$ over all configured $n_{CI}$ values for a CCE aggregation level $L$ of search space set $s$;
>
> the RNTI value used for $n_{RNTI}$ is the C-RNTI.

### 译文

> 聚合等级 $L \in \{1,2,4,8,16\}$ 的 USS（UE 专用搜索空间）由该聚合等级下的一组 PDCCH 候选定义。
>
> 对于与 CORESET $p$ 关联的搜索空间集 $s$，在时隙 $n_{s,f}^{\mu}$、对应载波指示字段值 $n_{CI}$ 的服务小区激活 DL BWP 上，聚合等级 $L$ 的 PDCCH 候选 $m_{s,n_{CI}}^{(L)}$ 所对应的 CCE 索引由下式给出：
>
> $$L \cdot \left\{ \left( Y_{p,n_{s,f}^{\mu}} + \left\lfloor \frac{m_{s,n_{CI}}^{(L)} \cdot N_{CCE,p}}{L \cdot M_{s,max}^{(L)}} \right\rfloor + n_{CI} \right) \bmod \left\lfloor N_{CCE,p}/L \right\rfloor \right\} + i$$
>
> 其中
>
> 对任何 CSS（公共搜索空间），$Y_{p,n_{s,f}^{\mu}} = 0$；
>
> 对 USS，$Y_{p,n_{s,f}^{\mu}} = \left(A_p \cdot Y_{p,n_{s,f}^{\mu}-1}\right) \bmod D$，初值 $Y_{p,-1} = n_{RNTI} \ne 0$，且 $A_p = 39827$（$p \bmod 3 = 0$）、$A_p = 39829$（$p \bmod 3 = 1$）、$A_p = 39839$（$p \bmod 3 = 2$），$D = 65537$；
>
> $i = 0, \cdots, L-1$（一个候选占 $L$ 个连续 CCE）；
>
> $N_{CCE,p}$ 是 CORESET $p$ 内的 CCE 数量，编号 0 到 $N_{CCE,p}-1$；
>
> $n_{CI}$ 为载波指示字段值（未配置跨载波调度时为 0，任何 CSS 下也为 0）；
>
> $m_{s,n_{CI}}^{(L)} = 0, \cdots, M_{s,n_{CI}}^{(L)} - 1$，其中 $M_{s,n_{CI}}^{(L)}$ 是 UE 被配置监听的候选数量；
>
> 对任何 CSS，$M_{s,max}^{(L)} = M_{s,0}^{(L)}$；
>
> 对 USS，$M_{s,max}^{(L)}$ 为该搜索空间集在所有已配置 $n_{CI}$ 上的最大候选数；
>
> $n_{RNTI}$ 使用的 RNTI 值为 C-RNTI。

### 解读

这个公式回答：**"这条 DCI 应该放在 CORESET 的哪几个 CCE 上？"** ——它的输出就是 cuPHY 收到的 `cce_index`。

拆解成三块：

```
起始 CCE = L · { ( Y  +  ⌊m·N_CCE/(L·M_max)⌋  +  n_CI ) mod ⌊N_CCE/L⌋ }  +  i
                 ↑         ↑                                              ↑
            随时隙跳变    候选在 CORESET 内均匀铺开              占 L 个连续 CCE
```

**(1) $Y$ ——伪随机跳变**

$Y_{p,n} = (A_p \cdot Y_{p,n-1}) \bmod 65537$ 是一个**线性同余发生器**，初值是 UE 的 C-RNTI。它让**同一个 UE 在不同时隙的候选位置来回跳**。

**比喻**：像考场每场考试重新排座位。若座位固定，两个 UE 一旦在某处碰撞，就会**永久碰撞**（每个时隙都撞）——这叫"阻塞（blocking）"。伪随机跳变把永久碰撞变成偶发碰撞。

$A_p$ 取三个不同素数（按 $p \bmod 3$ 选），让**不同 CORESET 的跳变序列互不相关**。

**(2) CSS 的 $Y=0$**

公共搜索空间**不跳变**——因为 UE 在读 SIB1/寻呼时还没有 C-RNTI，位置必须是确定的。

**(3) $\lfloor m \cdot N_{CCE}/(L \cdot M_{max}) \rfloor$**

把 $M_{max}$ 个候选**均匀撒在** $\lfloor N_{CCE}/L \rfloor$ 个可能位置上，避免候选挤在一起。

#### 与 cuPHY 的边界

**这个公式由 L2 计算，cuPHY 不实现。** 结果经 FAPI `cce-Index` 字段传入，cuPHY 侧只做范围检查：

```c
// scf_5g_fapi_dl_validate.cpp:276
if(ptr_dci[dci_idx].cce_index > 135) { … return INVALID_FAPI_PDU; }
```

> 上界 135 的来由：$N_{CCE}$ 最大 = 每符号最多 45 个 RB 组 × 3 符号 ÷ ... 实际按 FAPI 规定 `cce-Index` 值域 0→134，
> cuBB 放宽到 135。cuPHY kernel 直接用 `cce_index` 去索引 bundle，**不校验它是否落在实际分配的 CCE 范围内**——
> 越界配置会静默产生错误映射。

---

## 9.2 §12 Bandwidth part operation

### 原文（与 PDCCH 相关的核心段落）

> A UE configured for operation in bandwidth parts (BWPs) of a serving cell, is configured by higher layers for the serving cell a set of at most four bandwidth parts (BWPs) for receptions by the UE (DL BWP set) in a DL bandwidth by parameter *BWP-Downlink* or by parameter *initialDownlinkBWP* with a set of parameters configured by *BWP-DownlinkCommon* and *BWP-DownlinkDedicated*, and a set of at most four BWPs for transmissions by the UE (UL BWP set) in an UL bandwidth by parameter *BWP-Uplink* or by parameter *initialUplinkBWP* with a set of parameters configured by *BWP-UplinkCommon* and *BWP-UplinkDedicated*.

### 译文

> 为服务小区的带宽部分（BWP）操作而配置的 UE，由高层为该服务小区配置：下行带宽内**最多 4 个** BWP 用于 UE 接收（DL BWP 集），通过参数 *BWP-Downlink* 或 *initialDownlinkBWP*（其参数集由 *BWP-DownlinkCommon* 和 *BWP-DownlinkDedicated* 配置）；上行带宽内最多 4 个 BWP 用于 UE 发射（UL BWP 集），通过参数 *BWP-Uplink* 或 *initialUplinkBWP*（由 *BWP-UplinkCommon* 和 *BWP-UplinkDedicated* 配置）。

### 解读

BWP = **载波内的一段连续频率子集**。UE 不必一直监听整个 100 MHz，只在当前激活的 BWP 上工作，省电。

对 PDCCH 的意义：**CORESET 的频域位置是相对 BWP 起点定义的**。FAPI 的 `coreset-BWP-Start` / `coreset-BWP-Size` 出自本节，是 cuPHY 里 `start_rb` 的来源：

```c
// scf_5g_slot_commands_pdcch.cpp:143
coreset.start_rb = msg.bwp.bwp_start;
```

校验范围也源于此（FAPI 规定 `coreset-BWP-Size` 值域 1→275、`coreset-BWP-Start` 值域 0→274）：

```c
// scf_5g_fapi_dl_validate.cpp:152
if ((pdcch_pdu.bwp.bwp_size < 1) || (pdcch_pdu.bwp.bwp_size > MAX_N_PRBS_SUPPORTED)) { … }
```

> **cuBB 的简化**：`coreset.n_f` 直接取 `cell_params.nPrbDlBwp * 12`（**小区级**载波栅格），
> 而 `start_rb` 取 BWP 起点。也就是说 cuPHY 在**整个载波的网格**里按 BWP 偏移写 RE，
> 没有独立的 per-BWP 网格概念——对单 BWP 部署等价，多 BWP 场景需 L2 保证偏移自洽。

---

## 9.3 §13 UE procedure for monitoring Type0-PDCCH CSS sets

### 原文（章首）

> If during cell search a UE determines from MIB that a CORESET for Type0-PDCCH CSS set is present, as described in clause 4.1, the UE determines a number of consecutive resource blocks and a number of consecutive symbols for the CORESET of the Type0-PDCCH CSS set from *controlResourceSetZero* in *pdcch-ConfigSIB1*, as described in Tables 13-1 through 13-10, for operation without shared spectrum channel access in FR1 and FR2-1, or as described in Tables 13-1A and 13-4A for operation with shared spectrum channel access in FR1, or as described in Table 13-10A for FR2-2, and determines PDCCH monitoring occasions from *searchSpaceZero* in *pdcch-ConfigSIB1*, included in MIB, as described in Tables 13-11 through 13-15A.

### 译文

> 如果 UE 在小区搜索期间根据 MIB 判定存在 Type0-PDCCH CSS 集的 CORESET（如 4.1 节所述），则 UE 从 *pdcch-ConfigSIB1* 中的 *controlResourceSetZero* 确定该 CORESET 的**连续资源块数量**和**连续符号数量**——对 FR1 和 FR2-1 的非共享频谱操作按表 13-1 至 13-10，对 FR1 共享频谱操作按表 13-1A 和 13-4A，对 FR2-2 按表 13-10A；并从 MIB 所含 *pdcch-ConfigSIB1* 中的 *searchSpaceZero* 按表 13-11 至 13-15A 确定 PDCCH 监听时机。

> **摘抄说明**：Tables 13-1 ~ 13-15A 共 20 余张查找表（CORESET#0 的 RB 数/符号数/偏移、
> SSB 与 CORESET 的复用图样、监听时机），横跨原文约 20 页。这些表由 **L2/RRC 在配置阶段查表**，
> 结果以 `coreset-BWP-Start/Size`、`startSymbolIndex`、`durationSymbols`、`freqDomainResource`
> 等具体数值经 FAPI 下发，cuPHY 不查表。故此处不逐字转录。

### 解读

本节是 **CORESET#0（Type0-PDCCH CSS）** 的定义所在——即 UE 读完 MIB 后，用来接收调度 SIB1 的那个 PDCCH。

它对 cuPHY 只有**一个但很关键**的影响：**决定 `coreset_type` 取值**，进而决定 PDCCH DMRS 序列的频域参考点（见本文档 §2.2）。

```
MIB (38.331)
  └─ pdcch-ConfigSIB1 (8 bit)
       ├─ controlResourceSetZero (4 bit) ──查表13-1~13-10──→ CORESET#0 的 RB 数/符号数/偏移
       └─ searchSpaceZero        (4 bit) ──查表13-11~13-15─→ 监听时机
                                                    │
                                                    ▼
                              FAPI: coreset-Type = 0  →  cuPHY: coreset_type = 0
                                                    │
                                                    ▼
                      38.211 §7.4.1.3.2：DMRS 序列参考点 = CORESET 最低 RB
                                                    │
                                                    ▼
                          embed_pdcch_tf_signal.cu: gold_start_bit = 0
```

**为什么 CORESET#0 的 DMRS 参考点必须特殊**：UE 读 MIB 时还不知道 CRB 0 在哪里（那需要 SIB1 里的 `offsetToPointA`）。若 DMRS 序列从 CRB 0 起算，UE 就无法生成本地参考序列——**先有鸡还是先有蛋**。所以协议规定 CORESET#0 的序列从 CORESET 自身最低 RB 起算，打破循环依赖。

这正是 cuPHY 那一行三元表达式的全部理由：

```c
uint32_t gold_start_bit = (coreset_type == 0) ? 0 : start_rb * 6;
```

---

# 10. TS 38.331 §6.3.2 `ControlResourceSet`

> **摘抄范围说明**：§6.3.2 中与 PDCCH 相关的 IE 有 `ControlResourceSet`、`SearchSpace`、
> `PDCCH-Config`、`PDCCH-ConfigCommon`、`PDCCH-ConfigSIB1`。其中**只有 `ControlResourceSet`
> 的字段逐一对应 cuPHY 的 `cuphyPdcchCoresetDynPrm_t`**，故本章完整摘抄该 IE；
> 其余 IE 控制的是"何时监听/监听几个候选"，属 L2/RRC 调度域，仅在解读中说明。

### 原文（ASN.1 定义）

> The IE *ControlResourceSet* is used to configure a time/frequency control resource set (CORESET) in which to search for downlink control information (see TS 38.213 [13], clause 10.1). […MBS multicast 相关说明…]
>
> ```asn1
> ControlResourceSet ::=              SEQUENCE {
>     controlResourceSetId                ControlResourceSetId,
>     frequencyDomainResources            BIT STRING (SIZE (45)),
>     duration                            INTEGER (1..maxCoReSetDuration),
>     cce-REG-MappingType                 CHOICE {
>         interleaved                         SEQUENCE {
>             reg-BundleSize                      ENUMERATED {n2, n3, n6},
>             interleaverSize                     ENUMERATED {n2, n3, n6},
>             shiftIndex                          INTEGER(0..maxNrofPhysicalResourceBlocks-1)       OPTIONAL -- Need S
>         },
>         nonInterleaved                      NULL
>     },
>     precoderGranularity                 ENUMERATED {sameAsREG-bundle, allContiguousRBs},
>     tci-StatesPDCCH-ToAddList           SEQUENCE(SIZE (1..maxNrofTCI-StatesPDCCH)) OF TCI-StateId OPTIONAL, -- Cond NotSIB-initialBWP
>     tci-StatesPDCCH-ToReleaseList       SEQUENCE(SIZE (1..maxNrofTCI-StatesPDCCH)) OF TCI-StateId OPTIONAL, -- Cond NotSIB-initialBWP
>     tci-PresentInDCI                        ENUMERATED {enabled}                                  OPTIONAL, -- Need S
>     pdcch-DMRS-ScramblingID                 INTEGER (0..65535)                                    OPTIONAL, -- Need S
>     ...,
>     [[
>     rb-Offset-r16                       INTEGER (0..5)                                            OPTIONAL, -- Need S
>     tci-PresentDCI-1-2-r16              INTEGER (1..3)                                            OPTIONAL, -- Need S
>     coresetPoolIndex-r16                INTEGER (0..1)                                            OPTIONAL, -- Need S
>     controlResourceSetId-v1610          ControlResourceSetId-v1610                                OPTIONAL  -- Need S
>     ]],
>     [[
>     followUnifiedTCI-State-r17           ENUMERATED {enabled}                                      OPTIONAL  -- Need R
>     ]]
> }
> ```

### 原文（字段说明，节选与 PDCCH 发射相关者）

> **cce-REG-MappingType**
> Mapping of Control Channel Elements (CCE) to Resource Element Groups (REG) (see TS 38.211 [16], clauses 7.3.2.2 and 7.4.1.3.2).
>
> **controlResourceSetId**
> Identifies the instance of the *ControlResourceSet* IE. Value 0 identifies the common CORESET configured in MIB and in *ServingCellConfigCommon* (*controlResourceSetZero*) and is hence not used here in the *ControlResourceSet* IE. Other values identify CORESETs configured by dedicated signalling or in SIB1 or SIB20. The *controlResourceSetId* is unique among the BWPs of a serving cell.
>
> **duration**
> Contiguous time duration of the CORESET in number of symbols (see TS 38.211 [16], clause 7.3.2.2).
>
> **frequencyDomainResources**
> Frequency domain resources for the CORESET. **Each bit corresponds a group of 6 RBs**, with grouping starting from the first RB group in the BWP or MBS CFR where the CORESET is configured. […freqMonitorLocation 相关…] The first (left-most / most significant) bit corresponds to the first RB group in the BWP or MBS CFR where the CORESET is configured, and so on. A bit that is set to 1 indicates that this RB group belongs to the frequency domain resource of this CORESET. Bits corresponding to a group of RBs not fully contained in the bandwidth part within which the CORESET is configured are set to zero (see TS 38.211 [16], clause 7.3.2.2).
>
> **interleaverSize**
> Interleaver-size (see TS 38.211 [16], clause 7.3.2.2).
>
> **pdcch-DMRS-ScramblingID**
> PDCCH DMRS scrambling initialization (see TS 38.211 [16], clause 7.4.1.3.1). **When the field is absent the UE applies the value of the *physCellId* configured for this serving cell.**
>
> **precoderGranularity**
> Precoder granularity in frequency domain (see TS 38.211 [16], clauses 7.3.2.2 and 7.4.1.3.2).
>
> **reg-BundleSize**
> Resource Element Groups (REGs) can be bundled to create REG bundles. This parameter defines the size of such bundles (see TS 38.211 [16], clause 7.3.2.2).
>
> **shiftIndex**
> **When the field is absent the UE applies the value of the *physCellId* configured for this serving cell** (see TS 38.211 [16], clause 7.3.2.2).

### 译文

> IE *ControlResourceSet* 用于配置一个时频控制资源集（CORESET），UE 在其中搜索下行控制信息（见 TS 38.213 第 10.1 节）。
>
> （ASN.1 定义同上，字段类型与可选性保持原文。）
>
> **cce-REG-MappingType** —— 控制信道单元（CCE）到资源单元组（REG）的映射方式（见 38.211 §7.3.2.2 和 §7.4.1.3.2）。
>
> **controlResourceSetId** —— 标识 *ControlResourceSet* IE 的实例。**值 0 标识 MIB 和 *ServingCellConfigCommon*（*controlResourceSetZero*）中配置的公共 CORESET，因此不在本 IE 中使用**。其他值标识由专用信令、SIB1 或 SIB20 配置的 CORESET。*controlResourceSetId* 在一个服务小区的各 BWP 之间唯一。
>
> **duration** —— CORESET 的连续时间长度，以符号数计（见 38.211 §7.3.2.2）。
>
> **frequencyDomainResources** —— CORESET 的频域资源。**每个比特对应一组 6 个 RB**，分组从配置该 CORESET 的 BWP（或 MBS CFR）的第一个 RB 组开始。第一个（最左/最高有效）比特对应该 BWP 中的第一个 RB 组，依此类推。比特置 1 表示该 RB 组属于本 CORESET 的频域资源。对于未完全包含在该 BWP 内的 RB 组，相应比特置零（见 38.211 §7.3.2.2）。
>
> **interleaverSize** —— 交织器大小（见 38.211 §7.3.2.2）。
>
> **pdcch-DMRS-ScramblingID** —— PDCCH DMRS 加扰初始化（见 38.211 §7.4.1.3.1）。**该字段缺省时，UE 使用为该服务小区配置的 *physCellId* 值。**
>
> **precoderGranularity** —— 频域预编码粒度（见 38.211 §7.3.2.2 和 §7.4.1.3.2）。
>
> **reg-BundleSize** —— REG 可被捆绑成 REG bundle，本参数定义此类 bundle 的大小（见 38.211 §7.3.2.2）。
>
> **shiftIndex** —— **该字段缺省时，UE 使用为该服务小区配置的 *physCellId* 值**（见 38.211 §7.3.2.2）。

### 解读

这个 IE 与 cuPHY 结构体几乎是**一一映射**：

| RRC 字段 | FAPI 字段 | cuPHY (`cuphyPdcchCoresetDynPrm_t`) |
|---|---|---|
| `frequencyDomainResources` (45 bit) | `freqDomainResource[6]` | `freq_domain_resource` (uint64) |
| `duration` | `durationSymbols` | `n_sym` |
| `cce-REG-MappingType` | `cce-REG-MappingType` | `interleaved` |
| `reg-BundleSize` | `reg-BundleSize` | `bundle_size` |
| `interleaverSize` | `interleaverSize` | `interleaver_size` |
| `shiftIndex` | `shiftIndex` | `shift_index` |
| `pdcch-DMRS-ScramblingID` | `n-ID-PDCCH-DMRS-v3` | `dmrs_id`（cuBB 用 `scrambling_id` 兼任）|
| `precoderGranularity` | `precoderGranularity` | **未使用** ⚠️ |
| `controlResourceSetId` | `coreset-Type`（0/非0）| `coreset_type` |

#### 三个值得注意的点

**1. "每个比特对应 6 个 RB" ——45 bit × 6 = 270 RB**

这解释了 cuPHY 里所有 `× 6` 和 `/ 6` 的由来：

```c
// scf_5g_slot_commands_pdcch.cpp:136 —— 6 字节大端拼成 uint64
for(int i = 0; i < 6; i++)
    freqDomainResource |= static_cast<uint64_t>(msg.freq_domain_resource[i]) << (56 - (i * 8));
```

注意 FAPI 用 **6 字节 = 48 bit** 承载 45 bit 位图，左对齐到 uint64 的高位。cuPHY 随后：

```c
// 最右侧置位的位置决定 CORESET 跨越多少个 RB 组
params[coreset_idx].rb_coreset = 64 - find_rightmost_bit(params[coreset_idx].freq_domain_resource);
// 置位总数 × 符号数 = CCE 总数
params[coreset_idx].n_CCE = count_set_bits(params[coreset_idx].freq_domain_resource) * n_sym;
```

**2. `controlResourceSetId = 0` 被排除在本 IE 之外**

CORESET#0 **不能**用 `ControlResourceSet` 配置——它只能来自 MIB 的 `controlResourceSetZero`。这条规定正是 FAPI `coreset-Type` 字段存在的理由，也就是 cuPHY 里 `coreset_type` 分支的上游依据（见本文档 §2.2、§9.3）。

**3. 两个字段"缺省即用 PCI"**

`shiftIndex` 和 `pdcch-DMRS-ScramblingID` 缺省时都退化为 `physCellId`。**这个默认值在 FAPI 层就已被解析掉**——L2 必须填具体数值，PHY 收到的永远是显式值。所以 cuPHY 里看不到"缺省回退"逻辑。

> **关于 `SearchSpace` / `PDCCH-Config` / `PDCCH-ConfigCommon`**：
> 这些 IE 配置的是 `nrofCandidates`（各聚合等级的候选数 $M_s^{(L)}$）、`monitoringSlotPeriodicityAndOffset`、
> `searchSpaceType`（CSS/USS）等——它们喂给 §10.1 的 CCE 索引公式，产出 `cce-Index`。
> **cuPHY 只接收计算结果，不接触这些 IE。**

---

# 11. SCF-222 5G FAPI §3.4.2.1 PDCCH PDU

## 11.1 Table 3.4.2.1-1 PDCCH PDU（旧编号 Table 3-36）

### 原文

> Each DCI PDU includes the information that the L2/L3 software must provide the PHY so it may transmit the DCI described in [3GPP TS 38.212 [3], section 7.3.1], however, the L2/L3 constructs the DCI message.
>
> Each CORESET is related to a specific BWP and there may be more than 1 CORESET per BWP. If there is more than 1 CORESET per BWP, then multiple PDCCH PDUs should be included in the DL_TTI.request.
>
> | Field | Type | Description |
> |---|---|---|
> | **BWP [3GPP TS 38.213 [4], sec 12]** | | |
> | coreset-BWP-Size | uint16_t | Bandwidth part size [3GPP TS 38.213 [4], sec12]. Number of contiguous PRBs allocated to the BWP. Note: coreset-BWP-Start and coreset-BWP-Size are expected to correspond to BWP signalled in RRC. Value: 1->275 |
> | coreset-BWP-Start | uint16_t | bandwidth part start RB index from reference CRB [3GPP TS 38.213 [4], sec 12]. Note: CoresetBWPStart and CoresetBWPSize are expected to correspond to BWP signalled in RRC. Value: 0->274 |
> | subcarrierSpacing | uint8_t | subcarrierSpacing [3GPP TS 38.211 [2], sec 4.2] Value:0->4 |
> | cyclicPrefix | uint8_t | Cyclic prefix type [3GPP TS 38.211 [2], sec 4.2] 0: Normal; 1: Extended |
> | **Coreset [3GPP TS 38.211 [2], sec 7.3.2.2]** | | |
> | startSymbolIndex | uint8_t | Starting OFDM symbol for the CORESET. Value: 0->13 |
> | durationSymbols | uint8_t | Contiguous time duration of the CORESET in number of symbols. Corresponds to L1 parameter $N_{symb}^{CORESET}$ […] |
> | freqDomainResource | uint8_t[6] | Notes: If this CORESET is defined by *ControlResourceSet* IE for a UE, L2/L3 signals this parameter in a way consistent with *frequencyDomainResources* IE for the *ControlResourceSet* IE for the BWP, per 38.331 [6], section 6.3.2. If this CORESET is defined by *controlResourceSetZero* IE for a UE, L2/L3 signals this parameter in a way consistent with the RBs indicated by *controlResourceSetZero* IE for BWP#0, by setting CoresetBWPStart and CoresetBWPSize to start point and size of CORESET 0 directly, per 3GPP TS 38.213 [4], section 13. For this case, freqDomainResource always have bit value 1 at start of bitmap. |
> | cce-REG-MappingType | uint8_t | CORESET-CCE-to-REG-mapping-type [3GPP TS 38.211 [2], sec 7.3.2.2] 0: non-interleaved 1: interleaved |
> | reg-BundleSize | uint8_t | The number of REGs in a bundle. **Must be 6 for cce-REG-MappingType = nonInterleaved.** For cce-REG-MappingType = interleaved, must belong to {2,6} if duration = 1,2 and must belong to {3,6} if duration = 3. Corresponds to parameter L. [3GPP TS 38.211 [2], sec 7.3.2.2] Value: 2,3,6 |
> | interleaverSize | uint8_t | The interleaver size. For interleaved mapping belongs to {2,3,6} and for non-interleaved mapping is NA. Corresponds to parameter R. [3GPP TS 38.211 [2], sec 7.3.2.2] Value: 2,3,6 |
> | coreset-Type | uint8_t | [3GPP TS 38.211 [2], sec 7.3.2.2 and sec 7.4.1.3.2] 0: CORESET is configured by […PBCH/SIB1…] |
> | shiftIndex | uint16_t | […] |
> | precoderGranularity | uint8_t | […] |
> | num-DCI | uint16_t | Number of DCIs in this CORESET. Value: 0->MaxDciPerSlot |
> | For num-DCI { dci | structure | See Table 3.4.2.1-2 } | |

### 译文

> 每个 DCI PDU 包含 L2/L3 软件必须提供给 PHY 的信息，使其能够发送 [38.212 §7.3.1] 所述的 DCI；但 **DCI 消息本身由 L2/L3 构造**。
>
> 每个 CORESET 关联一个特定的 BWP，且每个 BWP 可以有多于 1 个 CORESET。若一个 BWP 有多个 CORESET，则应在 DL_TTI.request 中包含多个 PDCCH PDU。
>
> | 字段 | 类型 | 说明 |
> |---|---|---|
> | coreset-BWP-Size | uint16_t | 带宽部分大小，分配给 BWP 的连续 PRB 数。应与 RRC 信令的 BWP 一致。取值 1→275 |
> | coreset-BWP-Start | uint16_t | 相对参考 CRB 的带宽部分起始 RB 索引。取值 0→274 |
> | subcarrierSpacing | uint8_t | 子载波间隔（38.211 §4.2）取值 0→4 |
> | cyclicPrefix | uint8_t | 循环前缀类型。0：常规；1：扩展 |
> | startSymbolIndex | uint8_t | CORESET 的起始 OFDM 符号。取值 0→13 |
> | durationSymbols | uint8_t | CORESET 的连续时间长度（符号数），对应 L1 参数 $N_{symb}^{CORESET}$ |
> | freqDomainResource | uint8_t[6] | 若该 CORESET 由 *ControlResourceSet* IE 定义，L2/L3 按 38.331 §6.3.2 的 *frequencyDomainResources* 一致地填写；**若由 *controlResourceSetZero* 定义，则将 CoresetBWPStart/Size 直接设为 CORESET 0 的起点和大小（按 38.213 §13），此时位图起始位恒为 1** |
> | cce-REG-MappingType | uint8_t | 0：非交织；1：交织 |
> | reg-BundleSize | uint8_t | bundle 内 REG 数。**非交织时必须为 6**；交织时：duration=1,2 → {2,6}；duration=3 → {3,6}。对应参数 $L$ |
> | interleaverSize | uint8_t | 交织器大小，交织时 ∈{2,3,6}，非交织时不适用。对应参数 $R$ |
> | coreset-Type | uint8_t | 0：CORESET 由 PBCH/SIB1 配置（即 CORESET#0）|
> | num-DCI | uint16_t | 本 CORESET 内的 DCI 数量 |

### 解读

FAPI 这张表**几乎是 38.331 `ControlResourceSet` 的扁平化转录**，但补了两处物理层直接需要的东西：`startSymbolIndex`（RRC 里在 `SearchSpace` 中）和 `coreset-Type`。

`scf_fapi_pdcch_pdu_t` 与本表逐字段对应（`scf_5g_fapi.h:651`）：

```c
typedef struct {
    scf_fapi_bwp_ts38_213_sec_12_t bwp;      // coreset-BWP-Size/Start, scs, cyclicPrefix
    uint8_t  start_sym_index;                 // startSymbolIndex
    uint8_t  duration_sym;                    // durationSymbols
    uint8_t  freq_domain_resource[6];         // freqDomainResource
    uint8_t  cce_reg_mapping_type;            // cce-REG-MappingType
    uint8_t  reg_bundle_size;                 // reg-BundleSize
    uint8_t  interleaver_size;                // interleaverSize
    uint8_t  coreset_type;                    // coreset-Type
    uint16_t shift_index;                     // shiftIndex
    uint8_t  precoder_granularity;            // precoderGranularity
    uint16_t num_dl_dci;                      // num-DCI
    scf_fapi_dl_dci_t dl_dci[0];
} __attribute__ ((__packed__)) scf_fapi_pdcch_pdu_t;
```

**FAPI 明文写出的约束，cuBB 全部实现为校验**（`validate_pdcch_pdu()`）：

```c
if((pdcch_pdu.cce_reg_mapping_type == 0) && (pdcch_pdu.reg_bundle_size != 6)) → 拒绝
if(duration_sym==1||2) reg_bundle_size ∈ {2,6}                                → 否则拒绝
if(duration_sym==3)    reg_bundle_size ∈ {3,6}                                → 否则拒绝
if(interleaver_size ∉ {2,3,6})                                                → 拒绝
```

> **注意 CORESET#0 那条注释的份量**：它说明当 `coreset-Type = 0` 时，
> `coreset-BWP-Start/Size` 的语义**被重定义**为 CORESET#0 自身的起点和大小，
> 且位图首位恒为 1。这与 38.211 §7.4.1.3.2 的参考点切换是同一件事的两个侧面。

---

## 11.2 Table 3.4.2.1-2 DCI PDU（旧编号 Table 3-37）

### 原文

> | Field | Type | Description |
> |---|---|---|
> | rnti | uint16_t | The RNTI used for identifying the UE when receiving the PDU. Value: 1 -> 65535. |
> | n-ID-PDCCH-Data | uint16_t | Parameter $n_{ID}$ used for PDCCH Data scrambling in [3GPP TS 38.211 [2], sec 7.3.2.3]. Value: 0->65535 |
> | n-RNTI-PDCCH-Data | uint16_t | Parameter $n_{RNTI}$ used for PDCCH data scrambling, in [3GPP TS 38.211 [2], sec 7.3.2.3]. Value: 0 -> 65535 |
> | cce-Index | uint8_t | CCE start Index used to send the DCI. Value: 0->134 |
> | aggregationLevel | uint8_t | Aggregation level used [3GPP TS 38.211 [2], sec 7.3.2.1]. Value: 1,2,4,8,16 |
> | **Beamforming info** | | |
> | Precoding and Beamforming | structure | See Table 3.4.2.5-1. […] |
> | **Tx Power info** | | |
> | powerControlOffset-SS-Profile-NR | int8_t | PDCCH power value used for all PDCCH Formats. This is ratio of PDCCH and PDCCH DMRS EPRE to SSB/PBCH block EPRE 3GPP TS 38.214 [5]. […] "UE may assume that the ratio of PDCCH DMRS EPRE to SSS EPRE is within -8 dB and 8 dB when the UE monitors PDCCHs for a DCI format 1_0 with CRC scrambled by SI-RNTI, P-RNTI, or RA-RNTI." [3GPP TS 38.213 [4], section 4.1]. Value range: -8 … 8 representing -8 to 8 dB in 1dB steps. -127: L1 is configured with ProfileSSS |
> | **Payload** | | |
> | payloadSizeBits | uint16_t | The total DCI length (in bits) including padding bits [3GPP TS 38.212 [3], sec 7.3.1]. Range 0-> DCI_PAYLOAD_BTYE_LEN*8 |
> | payload | uint8_t[…] | DCI payload per [3GPP TS 38.212 [3], sec 7.3.1], where the actual size is defined by payloadSizeBits. Bit mapping is per bit significance rules, as described in section 3.1.6.1. Padding bits shall be set to zero. |

### 译文

> | 字段 | 类型 | 说明 |
> |---|---|---|
> | rnti | uint16_t | 接收该 PDU 时用于标识 UE 的 RNTI。取值 1→65535 |
> | n-ID-PDCCH-Data | uint16_t | 用于 PDCCH **数据加扰**的参数 $n_{ID}$（38.211 §7.3.2.3）。取值 0→65535 |
> | n-RNTI-PDCCH-Data | uint16_t | 用于 PDCCH 数据加扰的参数 $n_{RNTI}$（38.211 §7.3.2.3）。取值 0→65535 |
> | cce-Index | uint8_t | 用于发送该 DCI 的起始 CCE 索引。取值 0→134 |
> | aggregationLevel | uint8_t | 所用聚合等级（38.211 §7.3.2.1）。取值 1,2,4,8,16 |
> | powerControlOffset-SS-Profile-NR | int8_t | 用于所有 PDCCH 格式的功率值，即 PDCCH 与 PDCCH DMRS 的 EPRE 相对 SSB/PBCH block EPRE 之比。取值范围 −8…8，表示 −8 到 8 dB，步进 1 dB；−127 表示 L1 使用 ProfileSSS |
> | payloadSizeBits | uint16_t | DCI 总长度（比特），含填充比特（38.212 §7.3.1）|
> | payload | uint8_t[] | DCI 负荷（38.212 §7.3.1），实际大小由 payloadSizeBits 定义。比特映射按 3.1.6.1 的**比特有效性规则**。填充比特应置零 |

### 解读

**注意 FAPI 区分了三个 ID，而 cuPHY 只有两个字段**：

| FAPI | 协议出处 | cuPHY |
|---|---|---|
| `rnti` | 38.212 §7.3.2（CRC 加扰）| `rntiCrc` |
| `n-RNTI-PDCCH-Data` | 38.211 §7.3.2.3（数据加扰 $n_{RNTI}$）| `rntiBits` |
| `n-ID-PDCCH-Data` | 38.211 §7.3.2.3（数据加扰 $n_{ID}$）| `dmrs_id` ⚠️ |
| `n-ID-PDCCH-DMRS-v3`（Table 3.4.2.1-3）| 38.211 §7.4.1.3.1（DMRS $N_{ID}$）| **同一个 `dmrs_id`** ⚠️ |

```c
// scf_5g_slot_commands_pdcch.cpp:160
dci.rntiCrc  = msg_dci.rnti;             // → CRC 加扰
dci.rntiBits = msg_dci.scrambling_rnti;  // → 数据加扰 n_RNTI
dci.dmrs_id  = msg_dci.scrambling_id;    // → 数据加扰 n_ID **和** DMRS N_ID
```

协议上 §7.3.2.3 的 $n_{ID}$ 与 §7.4.1.3.1 的 $N_{ID}$ 是**两个独立参数**（虽然默认都等于 PCI）。cuBB 用一个字段兼任二者——在两者被配置成不同值时会不符合协议。这是 `spec_list.md` ⚠️ 表中已记录的实现限制。

**`payload` 的比特序**（FAPI §3.1.6.1 "bit significance"）是 `pdcchReverseBitInByte()` 存在的原因：FAPI 按 MSB-first 递送，而 Polar 编码器按 LSB-first 消费，故 setup 阶段逐字节翻转位序：

```c
// embed_pdcch_tf_signal.cu:62
outputByte[idxByte] <<= 1;
outputByte[idxByte] |= ((inputByte[idxByte] >> idxBit) & 1);
```

---

# 12. 全链路小结

## 12.1 协议章节 → cuPHY 代码 的完整映射

```
┌─ L2/RRC 域（cuPHY 不实现）────────────────────────────────────┐
│ 38.331 §6.3.2  ControlResourceSet / SearchSpace              │
│ 38.213 §10.1   CCE 索引哈希 → cce_index                       │
│ 38.213 §13     CORESET#0 查表 → coreset_type                  │
│ 38.212 §7.3.1  DCI 格式 → payload 内容                        │
└──────────────────────────┬───────────────────────────────────┘
                           │ SCF FAPI  DL_TTI.request
                           │ Table 3.4.2.1-1 / -2
                           ▼
┌─ cuPHY setup（CPU）──────────────────────────────────────────┐
│ cuphyPdcchPipelinePrepare()                                  │
│   ├ 38.212 §7.3.2  前置 24 个 1 → gCRC24C → CRC ⊕ RNTI       │
│   ├ FAPI §3.1.6.1  逐字节位序翻转                              │
│   └ 38.211 §7.3.2.2  推导 rb_coreset / n_CCE / coreset_map    │
└──────────────────────────┬───────────────────────────────────┘
                           ▼
┌─ cuPHY run（GPU，3 个 kernel）────────────────────────────────┐
│ ① encodeRateMatchMultipleDCIsKernel                          │
│     38.212 §5.3.1    N 的确定（n_max=9, R_min=1/8）           │
│     38.212 §5.3.1.1  输入交织（Table 5.3.1.1-1，164 项）      │
│     38.212 §5.3.1.2  子信道分配 + d = u·G_N                   │
│     38.212 §5.4.1.1  32 子块交织（Table 5.4.1.1-1）           │
│     38.212 §5.4.1.2  比特选择（重复/打孔/缩短）                │
│ ② genScramblingSeqKernel                                     │
│     38.211 §7.3.2.3  c_init = (n_RNTI·2^16 + n_ID) mod 2^31   │
│     38.211 §5.2.1    Gold 序列                                │
│ ③ genPdcchTfSignalKernel                                     │
│     38.211 §7.3.2.2  compute_map() 交织 f(x)                  │
│     38.211 §7.4.1.3.1 DMRS c_init（含符号号 l）                │
│     38.211 §7.4.1.3.2 DMRS 映射 k=12n+4k'+1；参考点切换        │
│     38.211 §5.1.3     QPSK（1/√2）                            │
│     38.211 §7.3.2.5   跳过 DMRS 的 RE 映射、β_PDCCH、端口 2000 │
│     38.214 §4.1       β = 10^(dB/20) 换算范式                  │
└──────────────────────────────────────────────────────────────┘
```

## 12.2 关键常数的协议出处

| cuPHY 常数 | 值 | 协议出处 |
|---|---|---|
| `G_CRC_24_C` | `0x01B2B117` | 38.212 §5.1 $g_{CRC24C}$（已逐位验证）|
| `CUPHY_PDCCH_N_CRC_BITS` | 24 | 38.212 §7.3.2 $L=24$ |
| `CUPHY_POLAR_ENC_MAX_INFO_BITS` | 164 | 38.212 §5.3.1.1 $K_{IL}^{max}=164$ |
| `N_MIN_CODED_BITS` | 32 | 38.212 §5.3.1 $n_{min}=5$ |
| `N_MAX_CODED_BITS` | 512 | 38.212 §7.3.3 $n_{max}=9$ |
| `MIN_CODE_RATE_INV` | 8 | 38.212 §5.3.1 $R_{min}=1/8$ |
| `INFO_TX_BITS_RATIO_THD` | 9/16 | 38.212 §5.3.1 判据 |
| `POLAR_ENC_INFO_BIT_INTERLEAVER_IDX[164]` | — | 38.212 Table 5.3.1.1-1（已逐项核对）|
| `POLAR_ENC_CODED_BIT_INTERLEAVER_IDX[32]` | — | 38.212 Table 5.4.1.1-1（已逐项核对）|
| `POLAR_REL_SEQ_IDXS_{32..512}` | — | 38.212 Table 5.3.1.2-1（按 $N$ 截断）|
| `0.70710678f` | $1/\sqrt{2}$ | 38.211 §5.1.3 QPSK |
| `Nc = 1600` | 1600 | 38.211 §5.2.1 $N_C$ |
| `2*9*6*AL` | $M_{bit}$ | 38.211 §7.3.2.1 + §7.3.2.5（9 数据 RE + 3 DMRS RE）|
| `(tid & 0x3) == 1` | — | 38.211 §7.4.1.3.2 $k=12n+4k'+1$ |
| `0x7fffffff` | mod $2^{31}$ | 38.211 §7.3.2.3 / §7.4.1.3.1 |

## 12.3 本文档的摘抄覆盖情况

| 章节 | 处理方式 | 说明 |
|---|---|---|
| 38.211 §7.3.2.1 ~ .5 | ✅ **完整 1:1** | §7.3.2.3 的 $c_{init}$ 公式在本地 ETSI PDF 中**缺失**，已标注并补注 |
| 38.211 §7.4.1.3.1/.2 | ✅ **完整 1:1** | |
| 38.211 §5.1.3 / §5.2.1 | ✅ **完整 1:1** | |
| 38.212 §5.1 | ✅ **完整 1:1** | 6 个生成多项式全录 |
| 38.212 §5.3.1 | ✅ **完整 1:1** | |
| 38.212 §5.3.1.1 | ✅ **完整 1:1** | 含 Table 5.3.1.1-1 全部 164 项 |
| 38.212 §5.3.1.2 | ⚠️ **核心 1:1 + 说明** | Table 5.3.1.2-1（1024 项，4 页）未逐字转录；PC 比特段落因 $n_{PC}=0$ 从略 |
| 38.212 §5.4.1.1 | ✅ **完整 1:1** | 含 Table 5.4.1.1-1 全部 32 项 |
| 38.212 §5.4.1.2 | ✅ **完整 1:1** | |
| 38.212 §5.4.1.3 | ⚠️ **摘要** | $I_{BIL}=0$，DCI 路径不执行 |
| 38.212 §7.3.2 / .3 / .4 | ✅ **完整 1:1** | |
| 38.212 §7.3.1 DCI 格式 | ❌ **未摘抄** | 原文约 80 页；cuPHY 不解析 payload 内容 |
| 38.213 §10.1 | ⚠️ **核心段 1:1** | 只摘 CCE 索引公式（`cce_index` 语义来源），其余为 UE 监听过程 |
| 38.213 §12 | ⚠️ **核心段 1:1** | |
| 38.213 §13 | ⚠️ **章首 1:1 + 说明** | Tables 13-1~13-15A 共 20 余张查找表由 L2 使用，未转录 |
| 38.213 §10.2 ~ §10.5 | ❌ **未摘抄** | UE 侧校验/监听过程，见 `spec_list.md` ⚠️ 表 |
| 38.214 §4.1 | ⚠️ **相关段 1:1** | 本节未直接定义 $\beta_{PDCCH}$，已在解读中说明 |
| 38.331 §6.3.2 `ControlResourceSet` | ✅ **完整 1:1** | ASN.1 + 相关字段说明 |
| 38.331 其余 PDCCH IE | ❌ **未摘抄** | `SearchSpace` / `PDCCH-Config` 等属 L2/RRC 调度域 |
| FAPI Table 3.4.2.1-1 / -2 | ✅ **完整 1:1** | |
| FAPI Table 3.4.2.1-3 / -4、3.4.2.5-1 | ❌ **未摘抄** | 维护字段与波束赋形结构，待补 |
| FAPI §3.3.2.4 CONFIG TLV | ❌ **未摘抄** | 待补 |
| O-RAN WG4 CUS-Plane | ❌ **无本地文档** | 协议文件夹内不存在该规范 |

> **待补优先级**：FAPI Table 3.4.2.5-1（预编码/波束赋形，对应 `cuphyPdcchPmWOneLayer_t`）
> ＞ FAPI §3.3.2.4 CONFIG TLV ＞ FAPI Table 3.4.2.1-3（`n-ID-PDCCH-DMRS-v3`）
> ＞ 38.212 §7.3.1 常用 DCI 格式（1_0 / 1_1 / 0_0 / 0_1）＞ O-RAN CUS-Plane（需先获取文档）
