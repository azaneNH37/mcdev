# 刷怪笼文件

接下来我们将逐层分析如何产出一个自定义刷怪笼配置。

本部分所有文件位&#x4E8E;**（以<命名空间>层为根目录）**`./spcurs/spawner`文件夹下。

#### Json文件命名

任意命名，符合MC命名空间ID需求即可。需要注意的是，任何情况下，spawner 文件夹内的文件夹结构不会反映至游戏内，**即仅有json文件的文件名以及该文件所在数据包命名空间组成其游戏内命名空间ID**。

#### ScSpawner

```json
{
  "active_range": <float>
  "display": <DisplayConext>,
  "global_effects": <ScEffects>,
  "creatures": <ScCreatures>,
  "loot_table": <string>
}
```

* active\_range：一个常数字段，可填入整数或小数，默认值为64，用于描述当刷怪笼附近多少格范围内存在玩家时，刷怪笼将处于激活状态
* loot\_table：一个字符串，填入一个指向任意战利品列表的**命名空间ID**，默认值为原版古城箱子的战利品列表，用于描述当该刷怪笼生命周期结束后，生成的战利品箱内容
* display：见DisplayContext
* global\_effects: 见ScEffects
* creatures: 见ScCreatures

#### DisplayContext

```json
{
    "img": <string>,
    "entity_color": <int>,
    "line_color": <int>,
    "head_color": <int>,
    "leg_color": <int>,
    
    "name": <string>,
    "rarity":<int>
}
```

* img：一个指向任意texture的命名空间ID，默认值为spcurs:spawner/default，本模组在`assets/spcurs/textures/spawner`文件夹中预先装载了部分可用纹理，通过以下方式使用：

```
“spcurs:spawner/x”, xy 应被替换为 default,drown,empty,port,recycle,stop
```

* entity\_color，line\_color，head\_color，leg\_color：一个ARGB/RGB颜色的十进制表示的整数；entity\_color用于描述游戏内的刷怪笼实体的渲染颜色；line\_color，head\_color，leg\_color分别描述一个刷怪笼配置对应于游戏中的物品的边框，上部，下部颜色（参考原版刷怪蛋物品）
* name：一个字符串，用于描述该刷怪笼配置在游戏内的显示名称；**注意，该名称将被直接显示，不作为一个可翻译键值存在**
* rarity：一个1\~6的整数（包含），用于描述刷怪笼配置的稀有度，影响物品显示颜色
