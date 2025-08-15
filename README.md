# QCirMat简介

QCirMat可用于在Mathematica中辅助构建量子线路的矩阵表示。结合Mathematica的符号计算能力，用户可实现量子线路的矩阵表示计算。

- 提供常见量子态、量子门的矩阵定义；
- 提供在指定量子比特上应用量子门，并计算其矩阵表示的函数；
- 提供构造受控量子门的函数。

## 一、开始使用

可采用如下两种方式之一来使用本软件包：

1. 用Mathematica打开`QCirMat.nb`，并运行。
2. 将`QCirMat.txt`中的所有内容复制到Mathematica中，并运行。

## 二、功能简介

### 2.1 基本量子态的矩阵表示

QCirMat提供若干基本态的矩阵表示

- Ket形式（列向量）
  - $\ket{0}$ ：`Ket0`
  - $\ket{1}$ ：`Ket1`
  - $\ket{+}$ ：`KetPlus`
  - $\ket{-}$ ：`KetMinus`
- Bra形式（行向量）
  - $\bra{0}$ ：`Bra0`
  - $\bra{1}$ ：`Bra1`
  - $\bra{+}$ ：`BraPlus`
  - $\bra{-}$ ：`BraMinus`
- Bell态的Ket形式
  - $\beta_{00} = \frac{1}{\sqrt 2}\left(\ket{00}+\ket{11}\right)$ ： `KetBell`
  - $\beta_{01} = \frac{1}{\sqrt 2}\left(\ket{01}+\ket{10}\right)$ ：`KetBell01`
  - $\beta_{10} = \frac{1}{\sqrt 2}\left(\ket{00}-\ket{11}\right)$ ：`KetBell10`
  - $\beta_{11} = \frac{1}{\sqrt 2}\left(\ket{01}-\ket{10}\right)$ ：`KetBell11`
- Bell态的Bra形式：`BraBell`, `BraBell01`, `BraBell10`, `BraBell11`
- Bloch球上的一般量子态的Ket形式 $e^{i\gamma}\left( \cos\frac{\theta}{2}\ket{0} + e^{i\phi}\sin\frac{\theta}{2}\ket{1} \right)$ ：`KetBloch[theta_,phi_,gamma_]`
- Bloch球上的一般量子态的Bra形式：`BraBloch[theta_,phi_,gamma_]`

### 2.2 基本量子门的矩阵表示

QCirMat提供若干基本量子门的矩阵表示

#### 单比特门

- I门：`Id` （在Mathematica中`I`已被用于虚数单位）
- X门：`X`
- Y门：`Y`
- Z门：`Z`
- S门：`S`
- T门：`T`
- H门：`H`
- $S^{\dagger}$ 门：`Sdag`
- $T^{\dagger}$ 门：`Tdag`

#### 多比特门

- CNOT门：`CNOT`
- 以第二个量子比特为控制位的CNOT门：`CNOT21`
- CZ门：`CZ`
- SWAP门：`SWAP`
- iSWAP门：`ISWAP`

#### 含参数门

- $R_x(\theta)$ 门：`Rx[theta_]`
- $R_y(\theta)$ 门：`Ry[theta_]`
- $R_z(\theta)$ 门：`Rz[theta_]`
- $R_1(\theta)$ 门：`R1[theta_]`
- $U_3(\theta,\phi,\lambda)$ 门：`U3[theta_,phi_,lambda_]`

### 2.3 用于构造复杂量子线路的矩阵表示的函数

#### ApplyGate函数

`ApplyGate`函数在指定下标的量子比特上应用指定的量子门，返回对应的矩阵表示。函数原型为：

```
ApplyGate[gate_, nqbits_, indexlist_]
```

- 参数`gate_`是一个 $2^k\times 2^k$ 矩阵，其中 $k$ 代表了量子门的比特数，例如单比特量子门（$k=1$）是 $2\times 2$ 矩阵，双比特量子门（$k=2$）是 $4\times 4$ 矩阵。
- 参数`nqbits_`是一个正整数，指定总量子比特数 $n$ ，该参数不能小于 $k$ 。
- 参数`indexlist_`是一个列表，按顺序指定要作用量子门的比特的下标，**注意在Mathematica中下标从1开始**。列表长度必须为 $k$ ，即与参数`gate_`的维数匹配。
- 该函数的返回值为一个 $2^n\times 2^n$ 维矩阵。

例：设总共有5个量子比特，以第4个量子比特为控制位，第2个量子比特为目标位应用一个CNOT门。可用如下方式调用：

```
In[*]:= ApplyGate[CNOT, 5, {4, 2}]
```

返回结果为一个 $32\times 32$（即 $2^5\times 2^5$）维矩阵。

#### ControlledGate函数

`ControlledGate`函数返回一个指定量子门的受控形式的矩阵表示。函数原型为：

```
ControlledGate[gate_, nctrlbits_]
```

- 参数`gate_`是一个 $2^k\times 2^k$ 矩阵，其中 $k$ 代表了量子门的比特数。
- 参数`nctrlbits_`是一个正整数，指定控制量子比特个数 $n$ 。
- 该函数的返回值为一个 $2^{n+k}\times 2^{n+k}$ 维矩阵，其中对应的前 $n$ 个量子比特为控制位， 后 $k$ 个量子比特为目标位。

联合使用`ControlledGate`和`ApplyGate`函数，可以生成以其中一些量子比特为控制位，其他一些量子比特为目标位的受控门的矩阵表示，见后文例2。

#### MultiGate函数

`MultiGate`函数生成在每个量子比特上分别应用一个单比特量子门的矩阵表示，即给定 $2\times 2$ 矩阵 $U$ 和总量子比特数 $n$ ，函数生成矩阵 $U^{\otimes n}$ 。函数原型为：

```
MultiGate[sqgate_, nqbits_]
```

- 参数`sqgate_`是个一个 $2\times 2$ 矩阵，表示单比特量子门。
- 参数`nqbits_`是一个正整数，表示总量子比特数 $n$ 。
- 函数返回一个 $2^n\times 2^n$ 维矩阵。

`MultiGate[U, 2]`等价于`KroneckerProduct[U, U]`；`MultiGate[U, 3]`等价于`KroneckerProduct[U, U, U]`。

## 三、示例

这里给出两个使用QCirMat在Mathematica中构造量子线路的实际案例。

### 例1. 检验下图两个量子线路的等价性

![](./_readme_imgs/twocir.jpg)

#### 1. 构造Cir1的矩阵表示

先分析一下Cir1的结构。如下图所示，Cir1可视为由5层组成，包含3种结构。从左数的第一层是一个 $I\otimes H$ 结构（第一个量子比特不作用门，即Identity；第二个量子比特作用H门），第二层是 $CNOT$ ，第三层是 $Z\otimes Z$ ，第四层是 $CNOT$ ，第五层是 $I\otimes H$ 。

![](./_readme_imgs/analyze_cir1.jpg)

在Mathematica中，矩阵乘法用`.`运算符，而矩阵的张量积（克罗内克积）则使用`KroneckerProduct`函数。因此，对于 $I\otimes H$ 和 $Z\otimes Z$ ，可分别使用

`KroneckerProduct[Id, H]`

`KroneckerProduct[Z, Z]` 或 `MultiGate[Z, 2]`

语句构造矩阵表示，其中`Id`、`H`、`Z`均为QCirMat中定义的量子门矩阵表示，`KroneckerProduct`为Mathematica内置函数，`MultiGate`为QCirMat中定义的函数。最终的量子线路的矩阵是将这五层的矩阵表示相乘。**请注意，矩阵乘法需要按照量子线路从右到左的顺序，** 即量子线路图最右边一层在乘积表达式中需出现在最左边。

```
In[*]:= Cir1 = KroneckerProduct[Id, H] . CNOT . KroneckerProduct[Z, Z] . CNOT . KroneckerProduct[Id, H]
Out[*]= {{0, 1, 0, 0}, {1, 0, 0, 0}, {0, 0, 0, 1}, {0, 0, 1, 0}}
```

#### 2. 构造Cir2的矩阵表示

Cir2较简单，只有一层 $I\otimes X$ ：

```
In[*]:= Cir2 = KroneckerProduct[Id, X]
Out[*]= {{0, 1, 0, 0}, {1, 0, 0, 0}, {0, 0, 0, 1}, {0, 0, 1, 0}}
```

#### 3. 利用矩阵相减比较两个量子线路

```
In[*]:= Simplify[Cir1 - Cir2]
Out[*]= {{0, 0, 0, 0}, {0, 0, 0, 0}, {0, 0, 0, 0}, {0, 0, 0, 0}}
```

输出是一个零矩阵，因此量子线路`Cir1`和`Cir2`是等价的。

### 例2. 构造受控双比特量子门，分别以不同量子比特为控制位



首先定义一个一般的双比特量子门`U`，它是 $4\times 4$ 矩阵，矩阵的每个元素都设为变量：

```
In[*]:= U = {{a11,a12,a13,a14}, {a21,a22,a23,a24}, {a31,a32,a33,a34}, {a41,a42,a43,a44}}
```

使用`ControlledGate`函数生成`U`的包含一个控制位的受控版本`CU`（$8\times 8$ 矩阵）：

```
In[*]:= CU = ControlledGate[U, 1]
```

根据`ControlledGate`函数的定义，返回的`CU`门的第一个量子比特为控制位，后两个量子比特为目标位。因此在使用`ApplyGate`函数时，通过指定`indexlist_`参数的下标顺序，即可将控制位指定为不同的下标。例如，指定为`{1,2,3}`则保持第一个量子比特为控制位：

![](./_readme_imgs/cu1.jpg)

指定为`{2,1,3}`则以第二个量子比特作为控制位：

![](./_readme_imgs/cu2.jpg)

指定为`{3,1,2}`则以第三个量子比特作为控制位：

![](./_readme_imgs/cu3.jpg)

## 四、联系我们

QCirMat由中国科学院高能物理研究所计算中心研发。

项目负责人：龙沛洵
longpx@ihep.ac.cn

## 五、版本历史

2025/8/14 第一个公开版本
