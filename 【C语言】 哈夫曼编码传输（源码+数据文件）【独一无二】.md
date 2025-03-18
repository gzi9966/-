> # C语言 哈夫曼编码传输（源码+数据文件）【独一无二】
> @[TOC](目录)
>

---



---

# 一、设计要求
问题4:某系统在通信联络中需要传送一个英文文件，该文件包含26个英文字母和空格等字符。请自行准备一个文本文件，要求这个文件中总符号数不少于10000个。要求运用哈夫曼编码方法，对该文本文件进行编码和解码，并校验解码后的文本正确性。请编写具体的计算机程序

![](https://i-blog.csdnimg.cn/direct/402e45aa405d49d3b6629481ca8e65f5.png)

---

# 二、设计思路
![](https://i-blog.csdnimg.cn/direct/93decd0c4537487fa15fe88322ec9ac3.png)





整个程序可以分为以下几个主要部分：

1. **文件操作与预处理**  
    - 获取文件大小（`getFileSize()`）。
    - 统计输入文件中每个字符的出现频率，为构建哈夫曼树做准备。
2. **核心数据结构与辅助工具**
    - 定义哈夫曼树节点（`HuffmanTreeNode`）和最小堆（`MinHeap`）的数据结构。
    - 实现最小堆的基本操作：插入、交换、向下调整（`minHeapify()`）与提取最小节点（`extractMin()`）。
3. **哈夫曼树构建与编码表生成**
    - 根据字符频率构建哈夫曼树，采用贪心策略不断合并频率最低的两个节点。
    - 遍历哈夫曼树生成每个字符的二进制编码（`buildCodes()`），并存入全局编码表 `huffmanCode` 中。
4. **文件编码与解码**
    - 通过编码表对原始文件进行编码（`encodeFile()`），将字符转换成由 0 和 1 组成的字符串保存到输出文件中。
    - 利用构建好的哈夫曼树对编码后的文件进行解码（`decodeFile()`），还原出原始文本信息。
5. **验证与性能统计**
    - 对比原始文件和解码后的文件（`compareFiles()`）确保解码准确性。
    - 计算文件压缩率，展示压缩效果。

---

## 2. 核心数据结构与辅助函数
### 2.1 数据结构定义
程序中定义了两个核心数据结构，在哈夫曼编码中起到重要作用：

+ **哈夫曼树节点（HuffmanTreeNode）**每个节点存储一个字符及其出现频率，同时包含左右孩子指针。例如：

```c
typedef struct HuffmanTreeNode {
    unsigned char ch;   // 字符
    unsigned freq;      // 字符频率
    struct HuffmanTreeNode *left, *right;
} HuffmanTreeNode;
```

+ **最小堆（MinHeap）**最小堆用于快速找到频率最小的节点，在构建哈夫曼树过程中非常关键：

```c
typedef struct MinHeap {
    int size;
    int capacity;
    HuffmanTreeNode** array;
} MinHeap;
```

### 2.2 最小堆辅助函数
程序实现了常用的最小堆操作，包括节点交换、向下调整（`minHeapify()`）、插入节点（`insertMinHeap()`）和提取最小节点（`extractMin()`）。  
例如，**向下调整**函数保证了堆的性质：

```c
void minHeapify(MinHeap* minHeap, int idx) {
    int smallest = idx;
    int left = 2 * idx + 1;
     // 代码略....
    if (right < minHeap->size &&
        minHeap->array[right]->freq < minHeap->array[smallest]->freq) {
        smallest = right;
    }
    if (smallest != idx) {
        swapNode(&minHeap->array[smallest], &minHeap->array[idx]);
        minHeapify(minHeap, smallest);
    }
}
```

![](https://i-blog.csdnimg.cn/direct/f7827321a8d54b77a113cbd31fa825ca.png)

---

## 3. 哈夫曼树的构建与编码生成
### 3.1 构建哈夫曼树
函数 `buildHuffmanTree()` 根据统计好的字符频率构建哈夫曼树，其基本流程如下：

1. **统计有效字符数量**  
遍历频率表，统计每个出现过的字符个数以确定最小堆的容量。
2. **构建最小堆并插入所有字符节点**  
为每个出现的字符创建一个节点，并插入到最小堆中。
3. **合并最小节点**  
循环执行以下操作：  
    - 提取两个最小频率的节点。
    - 创建一个新节点，其频率为两节点频率之和，新节点左右指针指向这两个节点。
    - 将新节点重新插入堆中，直到堆中只剩下一个节点，该节点即为哈夫曼树的根。

部分代码示例如下：

```c
while (minHeap->size > 1) {
    HuffmanTreeNode* left = extractMin(minHeap);
    HuffmanTreeNode* right = extractMin(minHeap);
    HuffmanTreeNode* top = createNode('\0', left->freq + right->freq);
    top->left = left;
    top->right = right;
    insertMinHeap(minHeap, top);
}
```

### 3.2 生成哈夫曼编码表
利用已经构建好的哈夫曼树，通过递归方式生成每个字符的编码：

+ 当递归向左（代表二进制“0”）或右（代表二进制“1”）不断累积编码，直到遇到叶子节点；
+ 到达叶子节点后，将生成的二进制字符串保存到全局编码表 `huffmanCode` 中。

函数示例：

```c
void buildCodes(HuffmanTreeNode* root, char* code, int length) {
    if (!root) return;
     // 代码略....
    code[length] = '0';
    buildCodes(root->left, code, length + 1);
    code[length] = '1';
    buildCodes(root->right, code, length + 1);
}
```

![](https://i-blog.csdnimg.cn/direct/cd82da5b2c1744489c252783f612b4d1.png)

---

## 4. 文件编码、解码与验证
### 4.1 编码文件
函数 `encodeFile()` 打开原始文件，并对每个字符用对应的哈夫曼编码替换后写入到编码文件中。  
关键代码：

```c
while ((ch = fgetc(fin)) != EOF) {
    fputs(huffmanCode[ch], fout);
}
```

### 4.2 解码文件
函数 `decodeFile()` 根据编码文件中的 0/1 流，利用哈夫曼树反向遍历解码还原每个字符，直到完整还原出原始文本：

```c
HuffmanTreeNode* curr = root;
while ((bit = fgetc(fin)) != EOF) {
    if (bit == '0')
        curr = curr->left;
    else if (bit == '1')
        curr = curr->right;
    // 到达叶子节点，输出字符
    if (!curr->left && !curr->right) {
        fputc(curr->ch, fout);
        curr = root;
    }
}
```

### 4.3 文件一致性对比
函数 `compareFiles()` 将原始文件和解码后的文件进行字节级对比，确保解码过程没有任何错误：

```c
do {
    c1 = fgetc(f1);
    c2 = fgetc(f2);
    if (c1 != c2) { /* 不同 */
        fclose(f1);
        fclose(f2);
        return 0;
    }
} while (c1 != EOF && c2 != EOF);
```

![](https://i-blog.csdnimg.cn/direct/adb31f4211f34b18801340e0e9d3b7b3.png)

---

## 5. 主函数及整体工作流程
主函数 `main()` 整合了上述所有步骤，其主要流程如下：

1. **预处理与频率统计**  
    - 判断原始文件是否存在，读取文件大小。
    - 遍历文件，统计每个字符的出现频率，填充频率表 `freqTable`。
2. **构建哈夫曼树与生成编码表**  
    - 调用 `buildHuffmanTree()` 构建哈夫曼树；
    - 通过 `buildCodes()` 生成用于编码的哈夫曼编码表。
3. **执行编码与解码**  
    - 使用 `encodeFile()` 对原始文件进行编码；
    - 用 `decodeFile()` 将编码后文件解码为还原文件。
4. **结果验证与性能评估**  
    - 调用 `compareFiles()` 检查解码文件与原文件是否一致；
    - 计算压缩率，并打印原始大小、压缩后大小及压缩率。

主函数示例代码：

```c
int main() {
    const char* inputFile    = "input.txt";    // 原始文件
    const char* encodedFile  = "encoded.txt";  // 编码结果(0/1串)
    const char* decodedFile  = "decoded.txt";  // 解码结果

    // 1) 读取原文件大小
        // 代码略....

    // 3) 构建哈夫曼树
    HuffmanTreeNode* root = buildHuffmanTree(freqTable);

    // 代码略....

    printf("原文件大小:%ld 字节\n", originalSize);
    printf("压缩后文件大小:%ld 字节\n", compressedSize);
    printf("压缩率:%.2f%%\n", ratio);
    printf("解压后文本与原文本相同:%s\n", isSame ? "True" : "False");

    // 释放哈夫曼树
    freeHuffmanTree(root);

    return 0;
}

```



运行结果：  
![](https://i-blog.csdnimg.cn/direct/646e965ed93b4da78fde33971df95a84.png)



![](https://i-blog.csdnimg.cn/direct/365108e63b804af99679dca8e4718a65.png)

