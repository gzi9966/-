# 设计需求
本项目是基于 Python 和 Pygame 开发的街机射击游戏，旨在为玩家提供快节奏且充满挑战的飞行射击体验。本项目采用模块化设计，主要分为主程序和对象模块。主程序负责整体流程控制，包括 Pygame 环境初始化、窗口设置（288×512 分辨率或全屏缩放）、资源加载（图像、音频字体）、状态管理（主页、游戏中、得分页面）以及主循环内的事件处理和帧率控制。



# 操作方法
+ **开始游戏**：启动游戏后进入主页，点击屏幕任意区域即可开始游戏。
+ **移动飞船**：在游戏过程中，玩家可通过键盘的左右方向键控制飞船左右移动；同时，也支持鼠标操作，点击屏幕左侧或右侧可以使飞船向对应方向移动。
+ **发射子弹**：按下空格键或者点击飞船，即可发射子弹攻击敌机。
+ **游戏退出**：在游戏过程中，按 ESC 或 Q 键可以退出游戏。
+ **其他操作**：在得分页面中，通过点击主页、重玩或声音按钮，可以分别返回主页、重新开始游戏，或切换声音、音乐的开关。



![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741871218066-f2c5dfde-d775-47fb-93fc-cd80e15aa219.png)



# 开发思路
1. **main.py**  
负责游戏的初始化、资源加载（图像、音频、字体）、窗口设置以及整个游戏的状态管理（主页、游戏中、得分页面）。主循环中处理用户输入（键盘和鼠标）、事件响应（例如开始游戏、左右移动、发射子弹及退出）以及各游戏对象的更新和绘制，并控制帧率确保流畅运行。
2. **objects.py**  
定义所有游戏对象，包括：
    - **Background**：实现背景图的无缝滚动，营造飞行效果；
    - **Player**：玩家操控的飞机，负责加载动画帧、控制移动、更新血量和燃料；
    - **Enemy**：不同类型的敌机，每种类型拥有独特的动画及射击方式；
    - **Bullet、Explosion**：子弹发射和爆炸动画效果，通过精灵组进行管理；
    - **Fuel、Powerup**：补充燃料和增益道具；
    - **Button、Message、BlinkingText**：界面按钮以及文本显示（例如“Game Over”提示或“Tap To Play”闪烁文字）。

## 主要流程及示例代码
### 初始化与资源加载
在 `main.py` 中，先初始化 Pygame、设置屏幕分辨率和窗口模式，再加载所需资源：

```python
pygame.init()
SCREEN = WIDTH, HEIGHT = 288, 512
win = pygame.display.set_mode(SCREEN, pygame.NOFRAME)
# 加载图像
plane_img = pygame.image.load('Assets/plane.png')
logo_img = pygame.image.load('Assets/logo.png')
# 加载按钮及背景图片
home_btn = Button(home_img, (24, 24), WIDTH // 4 - 18, HEIGHT//2 + 120)
```

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741621300259-7021cd74-f10f-4737-9d90-7fcef6875316.png)

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741871221447-68105b9c-89e2-4a38-b85a-3e2e369c7645.png)

### 状态管理与事件处理
主循环根据当前状态（主页、游戏中、得分页面）分别处理用户输入和对象更新。例如，在游戏状态中，监听左右方向键和鼠标点击以控制玩家移动和发射子弹：

```python
if event.type == pygame.KEYDOWN and game_page:
    if event.key == pygame.K_LEFT:
        moving_left = True
    if event.key == pygame.K_RIGHT:
        moving_right = True
    if event.key == pygame.K_SPACE:
        shoot_bullet()

if event.type == pygame.MOUSEBUTTONDOWN:
    if home_page:
        home_page = False
        game_page = True
        click_fx.play()
```

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741621844872-98556655-64aa-44a0-9baa-047860efa7df.png)

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741871226593-c79e695e-434a-4520-a54c-2bc160803217.png)



![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741621824793-850d9f6a-5a38-449f-9d8a-ec9315d053af.png)

### 对象更新与绘制
各个游戏对象由 `objects.py` 中的类实现，其 update() 方法负责更新位置、动画帧及碰撞检测。以玩家飞机为例：

```python
class Player:
    def __init__(self, x, y):
        self.image_list = []
        for i in range(2):
            img = pygame.image.load(f'Assets/player{i+1}.png')
            img = pygame.transform.scale(img, (100, 86))
            self.image_list.append(img)
        self.reset(x, y)

    def reset(self, x, y):
        self.index = 0
        self.image = self.image_list[self.index]
        self.rect = self.image.get_rect(center=(x, y))
        self.health = 100; self.fuel = 100; self.powerup = 0; self.alive = True

    def update(self, moving_left, moving_right, explosion_group):
        if self.alive:
            if moving_left and self.rect.x > 2:
                self.rect.x -= self.speed
            if moving_right and self.rect.x < WIDTH - self.image.get_width():
                self.rect.x += self.speed
            # 切换动画帧
            self.counter += 1
            if self.counter >= 2:
                self.index = (self.index + 1) % len(self.image_list)
                self.image = self.image_list[self.index]
                self.counter = 0
```

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741871232732-1ae06bd7-97a0-4a70-b43e-c14af20bcfa7.png)

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741621291739-6807e994-80f8-4453-81fd-fe83384bbdeb.png)

### 界面与音效控制
在得分页面和主页中，通过 Button 与 Message 类显示和切换界面，同时支持音效和音乐开关。所有按钮绘制、点击检测均封装在 Button 类内部。

## 三、总结
通过将游戏各功能模块（初始化、输入处理、对象更新、碰撞检测、状态切换和资源管理）进行清晰划分，实现了低耦合、易扩展的设计。每个游戏对象（如 Player、Enemy、Bullet 等）都封装了各自的绘制和更新逻辑，再由主循环统一调用，确保了游戏整体的流畅性和良好的用户体验。





![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741871234680-95d2daa0-2b72-461e-8865-010c06c2c213.png)













