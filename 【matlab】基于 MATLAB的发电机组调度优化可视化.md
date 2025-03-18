## 1. 整体设计思路
该代码的主要目的为解决一个发电机组调度优化问题，即在满足各时段负荷需求的前提下，通过调整各机组在各时段的发电出力，使得总发电成本最小。在求解最优发电组合后，代码还利用多种图形（折线图、堆叠柱状图、3D山丘图等）对系统运行情况、电能市场与调频市场的中标情况、出清价格以及储能站报价策略进行可视化展示。

---

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741702252333-222cb0e3-4866-460c-ba99-81c67e310cc4.png)

## 2. 设计思路
### （1）数据读取与变量设置
+ **读取机组数据**  
包含机组编号、所属类别、最大出力、最小出力、发电报价以及后续调频相关的参数。  

```matlab
; 此处略去算法至少30行
; 此处略去算法至少30行


numGen = size(genData, 1);  % 机组数
genPmax = genData(:, 3);    % 最大出力
genPmin = genData(:, 4);    % 最小出力
genCost = genData(:, 5);    % 发电报价
```

+ **读取负荷数据**  
给定一天24小时的负荷需求，作为每个时段的总负荷。

```matlab
; 此处略去算法至少30行
; 此处略去算法至少30行

timePeriods = size(loadCurve, 2);  % 时段数量
```

+ **初始化优化变量**  
每个机组在各时段的出力初始猜测值。由于 `fmincon` 要求变量为列向量，因此将矩阵展开成列向量。

```matlab
Pg0 = ones(numGen, timePeriods) * 100;  % 初始猜测值

% 将 Pg 展开成 72×1 的列向量
Pg_vec0 = reshape(Pg0, [], 1);

% 约束边界 (每个机组在各时段的出力不能小于最小出力，也不能大于最大出力)
lb = repmat(genPmin, timePeriods, 1);  
ub = repmat(genPmax, timePeriods, 1);
```

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741702257609-1e64fefb-2a50-46a3-be61-ad5dda311295.png)

### （2）构造优化问题
+ **目标函数**  
定义目标为总发电成本（各机组发电报价乘以出力之和）。

```matlab
costFunction = @(Pg_vec) sum(genCost' * reshape(Pg_vec, numGen, timePeriods));
```

+ **构造等式约束**  
针对每个时段，要求各机组出力之和恰好等于该时段的负荷需求。

```matlab
Aeq = zeros(timePeriods, numGen * timePeriods);
; 此处略去算法至少30行
; 此处略去算法至少30行

end
beq = loadCurve(:);  % 每个时段的负荷需求 (24×1)
```

+ **利用 fmincon 求解优化问题**  
使用 MATLAB 的 `fmincon` 函数（使用 SQP 算法），在既定边界及约束条件下求解优化问题，得到最优的输出组合。

```matlab
options = optimoptions('fmincon', 'Display', 'iter', 'Algorithm', 'sqp');  
Pg_vec_opt = fmincon(costFunction, Pg_vec0, [], [], Aeq, beq, lb, ub, [], options);

% 还原优化结果 Pg_opt (3×24)
Pg_opt = reshape(Pg_vec_opt, numGen, timePeriods);
```



**剩余算法过程解释略....**

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741702261731-ef7ad4ec-68e7-42f9-91e4-9224a880bab0.png)

### （3）结果可视化
为更直观地展示优化结果与市场模拟，代码中设计了多组图形展示。下面简单说明各部分图形的绘制目的与方法。

+ **系统日负荷曲线**  
对比优化后各时段的总发电量与预设负荷需求。并在各数据点添加标签。

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741701864184-a41e78af-e450-4cad-ac28-d420885c7f67.png)

+ **电能量市场中标情况柱状图**  
利用堆叠柱状图展示各机组在各时段的发电贡献情况。

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741701805199-6a83ceea-ef2a-4b55-99a5-beaae410545f.png)

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741702282149-86fac963-2465-408d-8e2d-8622a395ef40.png)

+ **调频容量/里程中标情况柱状图**  
根据机组参数（如调频容量和调频里程比例）与优化出的发电量计算各时段中标情况，并用堆叠柱状图展示。

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741701878932-175443e5-847b-47c3-81ef-13739a1e4cb1.png)

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741701901611-fe39e61f-5d1a-4c96-a053-1b5e3f26d359.png)

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741702284750-b7015cf8-2b23-4901-adc5-cece37024223.png)

+ **电能量市场出清价格3D山丘图**  
利用发电报价数据构造一个基础价格矩阵，加入噪声及平滑处理后，生成 3D 曲面图，展示各机组各时段的出清价格变化。

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741701925677-81318b58-b9bc-402c-a570-b843387b068e.png)

+ **调频辅助服务市场出清价格折线图**  
模拟调频市场的出清价格，通过计算容量与里程中标情况平均值，并绘制折线图，同时添加数据标签。

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741701933743-46b3017f-d568-45df-8e50-df6fc388030a.png)

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741702287113-77eca36d-c928-44c1-b95a-a929b9d7240c.png)

+ **储能站报价策略及最优报价策略组合图**  
首先随机生成储能站报价数据，再绘制柱状图和折线图的组合图展示报价趋势；随后计算最优报价（例如乘以系数0.9），进行类似展示。

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741701949100-0d636d25-1d60-4c7f-a9be-230fbdb37c33.png)

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741701956217-17475264-9ce5-4104-86fd-d89643873d35.png)

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741702289453-26f535de-53de-43d0-8ee0-42a8d9838fef.png)



