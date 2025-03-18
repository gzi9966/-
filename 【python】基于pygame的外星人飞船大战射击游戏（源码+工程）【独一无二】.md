# 设计需求
本项目旨在开发一款基于 Python Pygame 库的街机射击游戏，提供流畅激烈的太空战斗体验。游戏画面以不断滚动的星空为背景，玩家通过 WASD 键平滑操控飞船，在屏幕内自由移动，并利用空格键发射导弹、B 键投放炸弹，抵御以正弦、斜线等多种运动方式来袭的外星人。整个游戏采用模块化设计，主程序负责初始化、事件监控与游戏流程控制，主菜单支持“开始游戏”、“高分榜”、“音效开关”和“退出”功能，并通过键盘 KEYDOWN 事件实现菜单项的切换；同时，游戏核心部分通过持续侦测键盘状态确保飞船移动的平滑性。各类游戏对象（飞船、外星人、导弹、爆炸效果和 powerup 道具）均采用面向对象设计，并利用对象池技术实现复用，从而降低内存消耗并提升运行效率。此外，游戏还通过 SQLite 数据库实现高分记录和音效设置的持久化存储，保证玩家数据在多次运行间得以保留。整体系统结构清晰、功能模块分工明确，不仅满足实时性和操作流畅性的要求，同时也为后续扩展新外星人类型和功能增强打下坚实基础。

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741870315561-2882e6d8-5861-4f60-bee4-c1d2bef5875a.png)

# 游戏控制
菜单

+ w/s：移动光标
+ 输入/返回：确认选择或提交高分

游戏

+ w/a/s/d：移动船只
+ 空格：发射导弹
+ b：投下一颗炸弹

![](https://cdn.nlark.com/yuque/0/2025/gif/26188759/1741620231845-ff8b4614-74a6-40c4-a0c3-2445bd52ae31.gif)

# 整体设计思路
该游戏基于 Pygame 库实现，属于一个横向或纵向卷动的射击游戏。主要思路是将游戏分为多个模块，每个模块负责特定功能，通过对象池技术（object pooling）、精灵组（Sprite Groups）和事件循环来实现游戏整体逻辑。

1. **模块划分**  
游戏采用模块化设计，核心模块包括：
    - **shooting_game**：主程序入口，包含游戏初始化、主菜单与游戏主循环、事件监听与碰撞检测等；
    - **sprites**：所有游戏对象的定义，包括飞船、外星人、导弹、爆炸、炸弹、能量道具（powerup）等，采用面向对象的设计方式；
    - **database**：使用 SQLite 数据库对游戏得分、高分名单以及音效设定进行持久化存储；
    - **load**：用于加载图片和声音资源，将资源加载和处理与业务代码分离。
2. **面向对象设计**  
每个游戏元素都通过独立类来封装，如：
    - `MasterSprite`：作为所有精灵类的基类，定义了全局属性（如速度、精灵组引用）。
    - `Ship`：玩家控制的飞船，负责处理行动、移动边界限制以及炸弹功能；
    - `Alien` 及其派生类（ `Siney`、`Spikey`、`Fasty` 等）：不同类型的外星人，利用多态实现各自运动方式；
    - `Missile` 和 `Explosion`：射击和爆炸效果的实现，同时利用对象池技术复用对象，避免频繁创建和销毁对象带来的性能开销；
    - `Powerup` 及其子类：炸弹和护盾增强道具，提供游戏中的增益效果。

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741870321608-05b38528-1594-4a8b-9d3b-517739bb124e.png)

```python
import os
import pygame

main_dir = os.path.split(os.path.abspath(__file__))[0]
data_dir = os.path.join(main_dir, 'data')
#  此处略去20+行代码
#  此处略去20+行代码

def load_music(name):
    pygame.mixer.music.load(os.path.join(data_dir, name))


def load_sound(name):
    class NoneSound:
    #  此处略去20+行代码
    #  此处略去20+行代码
    try:
        sound = pygame.mixer.Sound(fullname)
    except pygame.error:
        print('Cannot load sound: %s' % fullname)
        raise SystemExit(str(pygame.get_error()))
    return sound


def load_image(name, colorkey=None):
    fullname = os.path.join(data_dir, name)
    try:
        image = pygame.image.load(fullname)
    except pygame.error:
        print('Cannot load image:', fullname)
        raise SystemExit(str(pygame.get_error()))
    #  此处略去20+行代码

```

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741619378438-c2455cb8-5bad-42bf-8dad-b1c5fc0796b1.png)

3. **事件与游戏主循环**  
代码采用 Pygame 事件循环和定时器控制游戏帧率，主要逻辑分为两大部分：
    - **主菜单循环**：通过键盘选择菜单项（例如开始游戏、查看高分、调节音效、退出），并利用 KEYDOWN 事件检测来实现菜单切换；
    - **游戏主循环**：在游戏进行时，持续监听用户键盘输入（使用 `pygame.key.get_pressed()` 实现平滑移动），定时生成导弹、炸弹、powerup和外星人，并进行碰撞检测（如导弹与外星人、飞船与外星人、powerup与飞船等）。

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741870326220-f9ad4fb3-fbdf-46d5-aec2-ca17a8730e9e.png)

```python
import pygame
import random
from collections import deque

from sprites import (MasterSprite, Ship, Alien, Missile, BombPowerup,
                     ShieldPowerup, Explosion, Siney, Spikey, Fasty,
                     Roundy, Crawly)
from database import Database
from load import load_image, load_sound, load_music

if not pygame.mixer:
    print('Warning, sound disabled')
if not pygame.font:
    print('Warning, fonts disabled')

BLUE = (0, 0, 255)
RED = (255, 0, 0)

class Keyboard(object):
    keys = {pygame.K_a: 'A', pygame.K_b: 'B', pygame.K_c: 'C', pygame.K_d: 'D',
            pygame.K_e: 'E', pygame.K_f: 'F', pygame.K_g: 'G', pygame.K_h: 'H',
            pygame.K_i: 'I', pygame.K_j: 'J', pygame.K_k: 'K', pygame.K_l: 'L',
            pygame.K_m: 'M', pygame.K_n: 'N', pygame.K_o: 'O', pygame.K_p: 'P',
            pygame.K_q: 'Q', pygame.K_r: 'R', pygame.K_s: 'S', pygame.K_t: 'T',
            pygame.K_u: 'U', pygame.K_v: 'V', pygame.K_w: 'W', pygame.K_x: 'X',
            pygame.K_y: 'Y', pygame.K_z: 'Z'}

def main():
    # 初始化
    pygame.mixer.pre_init(11025, -16, 2, 512)
    pygame.init()
    #  此处略去20+行代码
    #  此处略去20+行代码
    backgroundLoc = 1500
    finalStars = deque()
    for y in range(0, 1500, 30):
        size = random.randint(2, 5)
        x = random.randint(0, 500 - size)
        if y <= 500:
            finalStars.appendleft((x, y + 1500, size))
        pygame.draw.rect(background, (255, 255, 0), pygame.Rect(x, y, size, size))
```

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741619388798-126971e8-30c4-482a-b65e-ec98e08a867e.png)

4. **碰撞检测与波次管理**  
    - **碰撞检测**：游戏中主要利用 Pygame 内置的碰撞检测方法（`collide_rect`、`collide_circle`）来判断各个精灵之间的交互。
    - **波次管理**：外星人以波次出现，每波之间有间隔时间，当当前波的外星人全部消灭后，系统会进入倒计时状态，然后增加外星人数量或者提升移动速度，使游戏不断变难。

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741870330462-1af31fc5-e88a-46e0-a287-fd07eb8dadba.png)

```python
import pygame
import random
import math
from load import load_image


class MasterSprite(pygame.sprite.Sprite):
    allsprites = None
    speed = None


#  此处略去20+行代码
#  此处略去20+行代码


class BombPowerup(Powerup):
    def __init__(self):
        super().__init__('bomb')
        self.pType = 'bomb'


class ShieldPowerup(Powerup):
    def __init__(self):
        super().__init__('shield')
        self.pType = 'shield'


class Ship(MasterSprite):
    def __init__(self):
        super().__init__()
        self.image, self.rect = load_image('ship.png', -1)
        self.original = self.image
    #  此处略去20+行代码
    #  此处略去20+行代码


class Alien(MasterSprite):
    pool = pygame.sprite.Group()
    active = pygame.sprite.Group()

    def __init__(self, color):
        super().__init__()
        self.image, self.rect = load_image(
            'space_invader_' + color + '.png', -1)
        self.initialRect = self.rect
        screen = pygame.display.get_surface()
        self.area = screen.get_rect()
        self.loc = 0
        self.radius = min(self.rect.width // 2, self.rect.height // 2)

    #  此处略去20+行代码
    #  此处略去20+行代码

#  此处略去20+行代码
#  此处略去20+行代码

```

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741619438202-b5d5015b-0d39-426e-8251-3cc61c2257ed.png)

5. **资源管理与持久化**
    - **资源加载**：图片和声音资源由 `load.py` 模块统一管理，通过指定路径及颜色键处理，实现图片转换和声音对象的创建。
    - **数据库管理**：高分记录、音效和音乐设置通过 `database.py` 模块存储到 SQLite 数据库中，各模块通过静态方法完成插入、删除与查询操作，保证高分记录和设置在程序重启后能保留。

```python
import os
import sqlite3

main_dir = os.path.split(os.path.abspath(__file__))[0]
data_dir = os.path.join(main_dir, 'data')


class Database(object):
    path = os.path.join(data_dir, 'hiScores.db')
    numScores = 15

    @staticmethod
    def getSound(music=False):
        conn = sqlite3.connect(Database.path)
        c = conn.cursor()
        if music:
    #  此处略去20+行代码
    #  此处略去20+行代码
        return bool(setting[0][0]) if len(setting) > 0 else False

    @staticmethod
    def setSound(setting, music=False):
        conn = sqlite3.connect(Database.path)
        #  此处略去20+行代码
        #  此处略去20+行代码
        conn.commit()
        conn.close()

#  此处略去20+行代码
#  此处略去20+行代码
```

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741870335511-dc46ca99-aee4-4754-97ce-88d3352dfa1b.png)

![](https://cdn.nlark.com/yuque/0/2025/png/26188759/1741619395544-5b06fac8-a728-41d7-864d-f0fea2b00e35.png)

# 总结
总体来说，该游戏代码采用面向对象和模块化设计模式，将游戏的各个功能分层分离。主循环负责整体的更新和事件监听；Sprites 模块负责所有游戏对象的行为和呈现；Database 和 Load 模块则分别处理数据持久化和资源加载。这样不仅使代码逻辑清晰、易于维护，还为今后功能扩展（例如添加新类型外星人、设计更多增益道具或调整游戏难度）提供了便利的基础。











