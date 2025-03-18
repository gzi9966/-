
# 设计要求
本项目旨在开发一个基于 Java Swing 的连连看小游戏，用于展示图形界面编程及基本游戏逻辑的实现。游戏以 6×8 棋盘为核心，每个格子显示一个两字中文小说名称（如“红楼”、“西游”等），每种名称均以成对形式出现。玩家需通过点击消除相同图块。

开发目标：

+ 实现一个界面友好、响应灵敏的连连看小游戏。
+ 采用模块化设计，逻辑清晰、便于后续扩展（如增加两拐角逻辑、计时得分统计等）。

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741871899950-cbe993c2-6e91-4fcf-9a65-6b65d1a71365.png)

# 设计思路
## 棋盘初始化
+ **功能描述**  
在游戏启动时，根据棋盘大小（6×8）计算所需的小图块对数。预定义 12 个两字中文小说名称，并按需循环使用，保证每个图块成对出现。接着使用 `Collections.shuffle` 打乱图块的顺序后，填充到 `board` 数组中。
+ **关键代码**

```java
private void initializeBoard() {
    // 2个字的中文小说名称
    String[] elements = {
        "红楼", "西游", "水浒", "三国", "聊斋", "金瓶",
        "封神", "儒林", "边城", "围城", "活着", "平凡"
    };
    // 此处省略至少20行代码
    // 此处省略至少20行代码
    // 填充棋盘
    for (int i = 0; i < ROWS; i++) {
        for (int j = 0; j < COLS; j++) {
            board[i][j] = elementList.remove(0);
        }
    }
}
```

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741613825169-b6bbc6dc-16fa-4f35-a1dc-322ef9a30ea5.png)

## 界面生成与事件监听
+ **功能描述**  
使用 `GridLayout` 布局生成棋盘，每个格子用 `JButton` 来表示。按钮点击时，通过 `MouseAdapter` 监听事件，根据先后点击的两个按钮判断是否消除。
+ **界面部分关键代码**

```java
private void createUI() {
    JPanel panel = new JPanel(new GridLayout(ROWS, COLS));
    buttons = new JButton[ROWS][COLS];

    for (int i = 0; i < ROWS; i++) {
        for (int j = 0; j < COLS; j++) {
            // 此处省略至少20行代码
            // 此处省略至少20行代码
        }
    }

    add(panel, BorderLayout.CENTER);
    JLabel hintLabel = new JLabel("选择两个相同的小说元素进行消除", SwingConstants.CENTER);
    hintLabel.setFont(new Font("Serif", Font.BOLD, 16));
    add(hintLabel, BorderLayout.SOUTH);
}
```

+ **点击事件逻辑**  
用户点击图块时，如果是第一个图块则将其背景设置为黄色表示选中；若是第二个图块，则检查双方内容是否相同，并判断直线或折线（L 型）连接是否畅通。若满足条件，则“消除”这两个图块（清空文字、禁用按钮，并保持背景为黄色）。

```java
private class TileClickListener extends MouseAdapter {
    private final int row;
    private final int col;

    public TileClickListener(int row, int col) {
        this.row = row;
        this.col = col;
    }

    @Override
    public void mouseClicked(MouseEvent e) {
        if (!buttons[row][col].isEnabled()) {
            // 此处省略至少20行代码
        } else {
            // 此处省略至少20行代码
            selectedFirst = null;
        }
    }
}
```

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741871903330-47049b23-032a-464f-aa2a-5ec878e6734f.png)

## 消除规则与路径检测
+ **直线路径检测**  
当两个图块在同一行或同一列时，检查这两者之间是否有其他未消除的图块。  
关键方法为 `isStraightLineClear`：

```java
private boolean isStraightLineClear(int fixed, int start, int end, boolean isRow) {
    if (start > end) {
        int temp = start;
        start = end;
        end = temp;
    }
    // 此处省略至少20行代码
    // 此处省略至少20行代码
    return true;
}
```

+ **折线型折线路径检测**  
当直线路径不成立时，尝试使用折线型连接，候选拐角为 (x1, y2) 和 (x2, y1)：
    - 检查拐角处是否为空；
    - 检查两段直线（从起点到拐角及从拐角到目标点）是否畅通。

```java
private boolean isLShapeClear(int x1, int y1, int x2, int y2) {
    // 候选拐点1：(x1, y2)
    if (board[x1][y2].isEmpty()) {
        if (isStraightLineClear(x1, y1, y2, true) &&
            isStraightLineClear(y2, x1, x2, false)) {
            return true;
        }
    }
    // 候选拐点2：(x2, y1)
    // 此处省略至少20行代码
    return false;
}
```

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741613919833-76cbf296-0536-4ed8-822a-a6b6d7d89458.png)

最终，`canEliminate` 方法同时判断图块内容是否相同，并调用 `checkPath` 方法检测是否存在直线或 L 型连接路径：

```java
private boolean canEliminate(int x1, int y1, int x2, int y2) {
    // 此处省略至少20行代码
    // 此处省略至少20行代码
    return checkPath(x1, y1, x2, y2);
}

private boolean checkPath(int x1, int y1, int x2, int y2) {
    if (x1 == x2 && isStraightLineClear(x1, y1, y2, true)) {
    // 此处省略至少20行代码
    // 此处省略至少20行代码
    return false;
}
```

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741871906492-240bd9c7-7438-4b97-bb07-7e1a42cee034.png)

![](https://cdn.nlark.com/yuque/0/2025/gif/26188759/1741614261923-6bd5b396-a0ac-46b3-9a83-4f1231716018.gif)

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741871909490-8361350f-76d7-4d1d-b4e0-033888bc01a3.png)

