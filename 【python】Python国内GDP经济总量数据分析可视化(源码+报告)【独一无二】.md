

---

> # Python国内GDP经济总量数据分析可视化(源码+报告)【独一无二】
> @[TOC](目录)
>

---



---

# 一、设计思路
1. 初始化与数据导入:  
引入库:  
导入 pyecharts用于数据处理。  
导入 matplotlib.pyplot 用于基础的图形绘制。  
导入 seaborn 用于高级图形绘制。  
导入 WordCloud 用于生成词云图。  
导入 pandas 用于数据处理。

设置:使用 plt.rcParams 修改默认设置，以确保图形中的中文可以正常显示。  
数据读取:使用 pandas 的 read_csv 方法从 data.csv 文件中读取数据。

```python
import matplotlib.pyplot as plt
from wordcloud import WordCloud
import seaborn as sns
import pandas as pd
```

```python
plt.rcParams["font.sans-serif"] = ["SimHei"]
```



![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742034717854-b07ee067-6904-4b3d-bb1a-d50d9513d117.png)





2. 绘制总GDP的条形图:  
数据排序:对数据按 '总GDP (亿元)' 进行降序排序。图形绘制:设定图形的大小为15x8。使用 plt.barh 绘制条形图。设置X轴和Y轴的标签以及图形的标题。翻转Y轴的顺序使得GDP最高的省份在最上方。使用 plt.show 展示图形。

```python
plt.figure(figsize=(15, 8))
data = data.sort_values(by='总GDP (亿元)', ascending=False)
plt.barh(data['省份'], data['总GDP (亿元)'], color='skyblue')
plt.xlabel('总GDP (亿元)')
plt.ylabel('省份')
plt.title('各省份总GDP数据条形图')
plt.gca().invert_yaxis()
plt.show()
```

3. 绘制GDP实际增速的折线图:  
图形绘制:设定图形的大小为15x8。使用 sns.lineplot 绘制折线图，设置数据点的标记以及颜色。旋转X轴标签以避免重叠。设置图形的标题。使用 plt.show 展示图形。

```python
# 生成各省份GDP实际增速的折线图：
plt.figure(figsize=(15, 8))
sns.lineplot(data=data, x='省份', y='实际增速', marker='o', color='green')
plt.xticks(rotation=45)
plt.title('各省份GDP实际增速折线图')
plt.show()
```



![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742034722909-77ba141e-b5b6-4f06-bf03-eb923e03fb3d.png)



4. 绘制总GDP、人均GDP与常住人口的气泡图:图形绘制:设定图形的大小为15x8。使用 sns.scatterplot 绘制气泡图，设置气泡大小与色调。设置图形的标题和图例位置。使用 plt.show 展示图形。

```python
# 生成气泡图：
plt.figure(figsize=(15, 8))
sns.scatterplot(data=data, x='总GDP (亿元)', y='人均GDP (元)', size='常住人口 (万)', hue='省份', sizes=(50, 1000), palette='viridis')
plt.title('各省份总GDP、人均GDP、常住人口关系的气泡图')
plt.legend(bbox_to_anchor=(1.05, 1), loc='upper left')
plt.show()
```

5. 绘制总GDP的词云图:词云图设置:指定字体路径，用于支持中文。使用 WordCloud 创建词云对象并从频率生成词云。图形绘制:设定图形的大小为10x6。使用 plt.imshow 显示词云图。关闭坐标轴显示。设置图形的标题。使用 plt.show 展示图形。保存图像:使用 wordcloud.to_file 将词云图保存为PNG格式的图片文件。

```python
# 各省份总GDP数据的词云图：
font_path = 'msyh.ttc'
wordcloud = WordCloud(font_path=font_path, width=800, height=400, background_color='white').generate_from_frequencies(data.set_index('省份')['总GDP (亿元)'])
plt.figure(figsize=(10, 6))
plt.imshow(wordcloud, interpolation='bilinear')
plt.axis('off')
plt.title('各省份总GDP数据词云图')
plt.show()
wordcloud.to_file("wordcloud_image.png")
```



![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742034727379-6cd9d10f-945b-4a9c-a857-d1021436bcb7.png)

# 二、可视化分析
## 2.1 柱状图分析
 从柱状图中可以看出，每个月的数值有所不同，其中一些月份的数值明显高于其他月份。显示了这些月份的出色表现。某些月份，数值则相对较低，不到2500。一般来说，可以观察到一个周期性的模式，某些月份的数值往往会上升，而其他月份则会下降。

![](https://i-blog.csdnimg.cn/blog_migrate/75ef60ea8896c2e4ecf89729d922b71c.png)

## 2.2 折线图分析
 柱状图表示的是每月的销售额、产量或其他相关指标的变化。而折线图可能表示的是累计值或年度总和的变化。柱状图显示了明显的季节性或月度波动，而折线图则更多地显示了长期趋势。两图结合，为我们提供了对某一指标在短期和长期内的完整视图。

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742034733390-b74a5ce5-f5de-4d00-af85-f73a46e4172f.png)

![](https://i-blog.csdnimg.cn/blog_migrate/715d0254426e3c602f2868a98a31e695.png)

## 2.3 气泡图分析
在总GDP较高，且人均GDP也较高的区域有几个较大的气泡，这意味着这些省份在经济产值和人均财富方面均表现优越，并且有着较大的人口规模。在左下角区域有几个较小的气泡，表示这些省份的总GDP和人均GDP相对较低，同时城乡人口数也相对较少。在人均GDP相对较高但总GDP不是很高的区域也有一些省份，这可能表示这些省份的经济效率或人均生产力很高，但由于总人口不多，所以总GDP并不是很高。

![](https://i-blog.csdnimg.cn/blog_migrate/9f8f5d93faa86d0e244cc4814bee6120.png)

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742034742086-c2d2c91f-7f82-41e8-b260-275302a2f5a0.png)

## 2.4 词云图
![](https://i-blog.csdnimg.cn/blog_migrate/a516d0b7603aacbc91c901baa1171439.png)

## 2.5 可视化大屏
![](https://i-blog.csdnimg.cn/blog_migrate/a20028341911e4ea11e6a5937e07584f.png)



![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1742034746004-776c367d-9579-4f27-8aa4-e9fa21414555.png)

