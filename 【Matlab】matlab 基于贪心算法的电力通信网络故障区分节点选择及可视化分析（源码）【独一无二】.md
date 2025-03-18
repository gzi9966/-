---

> # 系列文章目录
> @[TOC](目录)
>

---



---

# 一、设计要求
本系统设计要求基于电力通信网络拓扑建立随机图模型，并结合贪心算法实现关键监测节点的选取，以便对各类故障进行准确区分。系统首先须采用设定节点数与连通概率构造电力通信网络拓扑，通过相应算法生成故障签名矩阵，每个故障事件由边上两个节点及附加的随机影响节点构成，以模拟实际故障扩散现象。设计要求在保证系统高效运行的前提下，利用贪心策略迭代选取对故障区分贡献最大的节点，逐步提升整体故障区分度，并记录每次迭代后区分度的提升情况。当所有故障均能被唯一区分后，系统应输出最终监测节点集合，并在无法进一步提升时作出合理提示。与此同时，系统需提供直观的可视化展示，包括网络拓扑示意图、故障矩阵热力图、区分度迭代变化曲线以及各监测节点贡献增益的柱状图，以便用户全面评估和优化选点策略，实现实时故障监测和高效网络管理。



---

# 二、设计思路
## 1. 设计思路概述
![](https://i-blog.csdnimg.cn/direct/5ec52c71a7774f2e8d4be816cddebb09.png)



1. **网络拓扑构造与故障建模**  
    - 随机生成一个具有指定连通概率的无向图，代表电力通信网络的节点和边。
    - 针对图中每条边定义一个故障，构建故障签名矩阵。对于每个故障，不仅标记边的两个端点，还随机增加额外的受影响节点，从而模拟故障的扩散效应。
2. **故障区分问题建模**  
    - 通过选取部分关键节点（或监测点），利用它们在故障签名矩阵中的信息来区分不同故障。  
    - 定义了一个“区分度”的指标——若利用选定节点的故障签名能使所有故障状态互不相同，则认为故障均能被区分。
3. **改进的贪心节点选取算法**  
    - 利用贪心算法迭代选取节点，每一步选择能带来最大“区分度提升”的节点，直到所有故障均能被区分或无进一步提升为止。
    - 记录每次迭代后的区分度发展，用于后续的可视化分析。
4. **数据可视化**  
    - 绘制网络拓扑图、故障特征矩阵热力图，以及描述区分度提升过程的折线图和选点贡献的柱状图，直观展示选点过程及其效果。
5. **辅助函数**  
    - 定义辅助函数 `pairwiseDistinguish`，用于判断给定故障签名矩阵中，所有行是否唯一，从而检测是否所有故障都能被区分。

---

## 2. 代码块详细解析
### 2.1 参数设置与网络生成
```matlab
clear; close all; clc;

%% 参数设置与网络生成
numNodes = 10;
p = 0.3; 

A = rand(numNodes) < p; 
A = triu(A,1); 
A = A + A';    
G = graph(A);

% 提取边信息
edges = G.Edges.EndNodes; 
numEdges = size(edges,1);
```

**作用：**

+ **网络构造：**  
使用 `triu` 只取上三角矩阵，然后加上转置矩阵以保证对称性，构造出图的邻接矩阵。
+ **图对象构造：**  
用 `graph(A)` 创建 MATLAB 图对象 `G`，便于后续拓扑可视化与分析。
+ **边信息提取：**  
提取所有边的信息，用于后续故障签名的构造。

---

### 2.2 构建故障签名矩阵
```matlab
%% 构建故障签名矩阵
faultSignatures = zeros(numEdges, numNodes);
for e = 1:numEdges
    affected = edges(e,:);
    otherNodes = setdiff(1:numNodes, affected);
        % 代码略。。。。。
    faultSignatures(e, affected) = 1;
end
```

**作用：**

+ **故障建模：**  
对于每一条边（代表一个故障事件），首先确定该故障必然影响边的两个端点。
+ **扩展受影响节点：**  
随机选择1至2个其他节点将其也视为受影响，增加故障消息的不确定性和复杂度。
+ **构造矩阵：**  
构造一个大小为 [numEdges x numNodes] 的二值矩阵，每行表示一个故障事件下哪些节点受影响（签名为1）。

---

![](https://i-blog.csdnimg.cn/direct/5ec52c71a7774f2e8d4be816cddebb09.png)



### 2.3 贪心算法选取关键监测节点
```matlab
canDistinguish = @(selected) all(pairwiseDistinguish(faultSignatures(:,selected)));

%% 改进贪心算法实现
selectedNodes = [];
distinguishHistory = []; 

while ~canDistinguish(selectedNodes)
    bestNode = 0;
    bestGain = -Inf;
    candidateNodes = setdiff(1:numNodes, selectedNodes);
    
    % 代码略。。。。。
    
    if bestNode == 0
        warning('无法进一步提高区分度，可能存在不可区分故障或需改进策略。');
        break;
    end
    
    selectedNodes = [selectedNodes, bestNode];
    distinguishHistory(end+1) = sum(pairwiseDistinguish(faultSignatures(:,selectedNodes)));
end

fprintf('最终选择的测点节点集合为: ');
disp(selectedNodes);
```

**作用：**

+ **定义区分函数：**  
匿名函数 `canDistinguish` 利用辅助函数 `pairwiseDistinguish` 判断在当前选定节点下，所有故障签名的行是否唯一（即所有故障是否都能被区分）。
+ **贪心选点流程：**  
    - 初始化选中节点集合为空，通过循环不断评估未选节点的“增益”，即加入后对故障区分度的提升。
    - 对每个候选节点进行测试，计算其加入后的“新”区分度，与基础区分度的差值作为增益。
    - 选择增益最大的节点加入监测节点集合。
    - 循环直到区分所有故障（或无法再提高为止），并记录每次选点后的区分度变化历史。
+ **输出：**  
最终输出被选中的监测节点集合，代表在这些节点上进行观测可以达到全局故障区分目的。

---

### 2.4 可视化展示
#### 图1：网络拓扑图
```matlab
%% 图1：可视化电力通信网络拓扑
    % 代码略。。。。。
title('图1：电力通信网络拓扑示意图');
xlabel('X坐标'); ylabel('Y坐标'); grid on;
```

**作用：**

+ 利用 MATLAB 内置图形函数 `plot(G)` 展示网络节点及其连边，直观反映网络通信拓扑结构。

#### 图2：故障特征矩阵热力图
```matlab
%% 图2：故障特征矩阵热力图
figure;
    % 代码略。。。。。
xlabel('节点编号'); ylabel('故障编号');
title('图2：故障特征矩阵(Heatmap)');
```

![](https://i-blog.csdnimg.cn/direct/5ec52c71a7774f2e8d4be816cddebb09.png)



**作用：**

+ 使用 `imagesc` 将故障签名矩阵以热力图形式展示，便于观察各故障对各节点的影响分布。

#### 图3：区分度随选点迭代过程变化
```matlab
%% 图3：区分度随选点迭代过程的变化
iterations = 1:length(distinguishHistory);

figure;
plot(iterations, distinguishHistory,'-o','LineWidth',2);
xlabel('迭代次数（已选节点数）');
ylabel('可区分度(1表示全部故障均可区分)');
title('图3：贪心选点过程中可区分度的变化');
grid on;
```

**作用：**

+ 绘制折线图展示随着选取更多监测节点，故障区分度的逐步提升，为算法效果提供直观量化指标。

#### 图4：每个选中节点对区分度提升的贡献
```matlab
%% 图4：查看每个被选中节点的贡献
selectedNodesCopy = [];
baseDistinguish = 0;
gains = zeros(1,length(selectedNodes));
for i = 1:length(selectedNodes)
    nd = selectedNodes(i);
    % 代码略。。。。。
end

figure;
bar(1:length(selectedNodes), gains);
xlabel('节点选择次序');
ylabel('增益（区分度提升）');
title('图4：每个选中节点对故障区分度提升的贡献');
grid on;
```

**作用：**

+ 通过柱状图展示每个被选监测节点在加入后带来的区分度提升量（增益），帮助理解各节点对最终故障区分性能的贡献大小。

### 2.5 辅助函数
```matlab
    % 代码略。。。。。
function out = pairwiseDistinguish(signatures)
    [~,ia,~] = unique(signatures,'rows','stable');
    out = (length(ia) == size(signatures,1));
end
```

**作用：**

+ `pairwiseDistinguish` 函数通过 `unique` 函数判断传入故障签名矩阵的所有行是否各不相同。如果所有行唯一（即区分所有故障），则返回 `true`；否则返回 `false`。此函数是评估当前选点效果的关键。



![](https://i-blog.csdnimg.cn/direct/a767fa6239cb450ea7e44837c58f3a8d.png)



![](https://i-blog.csdnimg.cn/direct/6671de3addc94208b7f05695dd69c149.png)

![](https://i-blog.csdnimg.cn/direct/dd9f5bb2c4964052aa662be4131a5c5c.png)

![](https://i-blog.csdnimg.cn/direct/2d7b1f359bff4607ae09c4ebcc78db0c.png)



![](https://i-blog.csdnimg.cn/direct/36b1079d3f39423e9b6c79afe9f67be9.png)



![](https://i-blog.csdnimg.cn/direct/5ec52c71a7774f2e8d4be816cddebb09.png)

