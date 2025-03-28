---

> # 系列文章目录
> @[TOC](目录)
>

---



---

# 一、设计要求
本设计要求利用MATLAB平台实现两个正弦信号的合成、采样及频谱分析。要求生成频率分别为900Hz和920Hz的正弦波，经叠加后构成复合信号，并在1秒内以8000Hz的采样频率采集充分数据，从而满足奈奎斯特采样定理要求。程序须计算最小采样频率、最短采集时长及最少采样点数，并在控制台输出相关参数，保证采样参数的合理性。设计要求对信号分别采用不同点数的FFT处理，包括440点、1024点DFT分析，绘制幅频图观察频谱走势及分辨率特点。此外，还需通过截取300、400、1000个采样点数据，采用2048点FFT进行频谱分析，研究截取长度对窗函数效果、谱泄露现象和分辨率表现的影响。最终验证各参数效应。



![](https://i-blog.csdnimg.cn/direct/00a187238df14794a92722928ca0a8aa.png)

---

# 二、设计思路
![](https://i-blog.csdnimg.cn/direct/2aba9042a61447d6bb974f2753e17752.png)

## 1. 设计思路概述
该代码主要实现以下功能：

1. **信号生成与基本参数计算**  
    - 生成一个由两个正弦波（频率分别为 900 Hz 与 920 Hz）叠加而成的信号。  
    - 根据奈奎斯特采样定理计算最小采样频率、最短采集时间及最少采样点数，确保信号采样满足理论要求。
2. **信号采样与时域显示**  
    - 将连续时间信号离散化，得到离散信号序列，并绘制离散时间信号的时域波形。
3. **频谱分析（DFT/FFT）**  
    - 利用 FFT 对信号进行离散傅里叶变换（DFT）分析。分别采用 440 点和 1024 点的DFT，观察不同 FFT 点数对频谱分辨率的影响。
4. **窗长（采样点数）对DFT结果的影响**  
    - 从离散信号中截取不同长度的样本（M=300, 400, 1000），并对这些数据进行 2048 点的 FFT 分析，观察截取的数据点数（相当于窗长）对频谱图结果的影响。

---

## 2. 代码块解析
### 2.1 初始设置与参数定义
```matlab
clc;
clear;
close all;
```

**说明：**  

+ 用于清空命令窗口、清除变量、关闭所有图形窗口，保证每次运行代码时环境干净。

---

### 2.2 参数设置与信号生成
```matlab
%% 参数设置
f1 = 900;   % 信号频率1，单位Hz
f2 = 920;   % 信号频率2，单位Hz（其实 f2 = f1+20）
fs = 8000;  % 采样频率，单位Hz
T = 1;      % 信号时长，单位s
t = 0:1/fs:T-1/fs; % 生成采样时间序列
```

**说明：**  

+ 定义信号的两个组成频率：900 Hz 与 920 Hz。  
+ 设置采样频率为 8000 Hz（高于最小采样要求），信号时长为 1 秒，并生成采样时刻向量 `t`。

接下来生成模拟的连续时间信号：

```matlab
%% 1. 频谱分析基本参数计算
% 生成模拟信号
xa = cos(2*pi*f1*t) + cos(2*pi*(f1+20)*t);
```

**说明：**  

+ 信号 `xa` 为两个频率正弦波的叠加，即 cos(2π·900·t) 与 cos(2π·920·t) 的合成。

随后计算基本采样参数：

```matlab
% 最小采样频率满足奈奎斯特采样定理
min_fs = 2 * (f1 + 20); % 最小采样频率
min_time = 1 / (f1 + 20); % 最短采集时间（对应高频分量的一个周期）
    % 此处略去至少10行
    
    % 此处略去至少10行
```

**说明：**  

+ 根据奈奎斯特定理，最小采样频率应不低于信号中最高频率的两倍，此处取 (f1+20) 作为最高频率。  
+ 最短采集时间取高频分量周期的倒数；对应在采样频率 `fs` 下计算所需的最少采样点数。

最后将基本参数输出到命令窗口：

```matlab
disp('频谱分析参数:');
disp(['最小采样频率: ', num2str(min_fs), ' Hz']);
disp(['最短采集时间: ', num2str(min_time), ' s']);
disp(['最少采样点数: ', num2str(min_samples)]);
```

---

![](https://i-blog.csdnimg.cn/direct/e04930521cd54eafabb675ad4c296d51.png)

### 2.3 离散信号采样与时域图绘制
```matlab
%% 2. 采样信号
xk = cos(2*pi*f1*(0:length(t)-1)/fs) + cos(2*pi*(f1+20)*(0:length(t)-1)/fs);

    % 此处略去至少10行
    
    % 此处略去至少10行
ylabel('幅值');
grid on;
```

**说明：**  

+ 采用与之前类似的方式生成离散信号 `xk`，这里利用采样下标 `k=0,1,...,length(t)-1` 来计算对应的采样值。  
+ 使用 `stem` 函数绘制离散信号的时域波形，使得每个采样点以离散点显示，直观展示采样过程。

---

### 2.4 不同点数的DFT分析：440点与1024点
```matlab
%% 3. 440点和1024点DFT分析
N1 = 440; % DFT点数1
N2 = 1024; % DFT点数2
    % 此处略去至少10行
    
    % 此处略去至少10行
grid on;

% 1024点DFT
X2 = fft(xk(1:N2), N2);
f2_axis = (0:N2-1) * fs / N2;
figure;
plot(f2_axis, abs(X2));
title('1024点DFT的幅频图');
xlabel('频率 (Hz)');
ylabel('幅值');
grid on;
```

**说明：**  

+ **DFT点数不同的影响：**  
    - 首先选取前 440 个采样点，对信号做 440 点 FFT 分析，得到频谱图；  
    - 接着选取前 1024 个采样点进行 FFT 分析。
+ **频率分辨率：**  
    - 采样点数不同会导致频频率分辨率不同（频率分辨率约为采样频率除以 FFT 点数）。
+ 绘制的幅频图展示了不同 FFT 点数下，信号的频谱形状以及分量（900 Hz 和 920 Hz）的分辨能力。

---

![](https://i-blog.csdnimg.cn/direct/860d8f8d7dd24435a999224721231c68.png)

### 2.5 不同采样窗长下的2048点DFT分析
```matlab
    % 此处略去至少10行
    
    % 此处略去至少10行
    figure;
    plot(f3_axis, abs(X3));
    title(['M = ', num2str(M), ' 的幅频图']);
    xlabel('频率 (Hz)');
    ylabel('幅值');
    grid on;
end
```

**说明：**  

+ **截取数据长度的影响：**  
    - 本部分针对采样信号 `xk`，分别截取不同数量（300、400、1000）个点作为分析数据，即相当于改变窗长。
+ **零填充与DFT点数：**  
    - 无论实际截取多少点，均进行 2048 点 FFT 分析，这里对不足2048点的数据进行了零填充操作。
+ **目的：**  
    - 观察窗长（即数据点数）的变化如何影响频谱图的分辨率、幅值变化，以及可能出现的谱泄露等现象。

### 2.6 总结提示
```matlab
%% 总结
disp('分析完成：从频谱图观察信号分量的区分情况及FFT点数和窗长对结果的影响。');
```

**说明：**  

+ 给出公示结束提示，提醒用户通过观察各幅频图可以看出 FFT 点数和窗长对信号分量区分效果的影响。

---

## 3. 整体设计思路总结
+ **信号构造：** 通过将两正弦波叠加构造一个频率非常接近（900 Hz 与 920 Hz）的信号，为后续频谱分析提供试验信号。  
+ **理论参数验证：** 根据奈奎斯特采样定理计算采样系统的基本参数，确保采样条件满足信号还原要求。  
+ **时域与频域分析：**  
    - 在时域上展示离散信号。  
    - 采用 FFT 将时域信号转换到频域，从频谱图中可以直观地看到信号的两个频率分量。
+ **FFT参数影响探究：**  
    - 分别采用不同的 FFT 点数（440点与1024点）对相同的数据进行变换，比较它们在频率分辨率上的差异。  
    - 通过改变采样数据的窗口长度（M值），结合固定的FFT长度（2048点），进一步观察窗长对频谱分析的影响，如谱泄露和频谱分辨率等问题。

这种设计方法非常适合于实际信号处理工程中对采样参数与 FFT 分析参数进行优化调试，既可以验证理论上的采样要求，又能直观地评估不同参数设置下的频谱分析效果。

---

通过上述解释，希望能帮助你更好地理解代码的整体设计思路及各个部分的作用。  
![](https://i-blog.csdnimg.cn/direct/6b76e4af7f374b2e8bec3fa720ad93dd.png)

![](https://i-blog.csdnimg.cn/direct/fd239168014145b8bbcb73cdcef5ccbe.png)

![](https://i-blog.csdnimg.cn/direct/46a8afd7fbcb428eaf663371dda7bb4f.png)

![](https://i-blog.csdnimg.cn/direct/62d02623f36646cf8804f4baa36f7b46.png)  
![](https://i-blog.csdnimg.cn/direct/2f597919ed1b44059a75788454239a5d.png)

![](https://i-blog.csdnimg.cn/direct/46f90f8de56245d8853a2d591f16ea9c.png)

![](https://i-blog.csdnimg.cn/direct/f7cf471c96f24c9d8f9f2c46ca93d306.png)

![](https://i-blog.csdnimg.cn/direct/561116406dc14bf080ec2b1a55594f40.png)



![](https://i-blog.csdnimg.cn/direct/ec60afd3063141429e98a1cd3cc8a8c5.png)

