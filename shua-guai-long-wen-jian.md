---
description: 万物的起源？
---

# 刷怪笼文件

接下来我们将逐层分析如何产出一个自定义刷怪笼配置。

本部分文件位&#x4E8E;**（以<命名空间>层为根目录）**<kbd>./spcurs/spawner</kbd>文件夹下。

#### Json文件命名

任意命名，符合MC命名空间ID需求即可。需要注意的是，任何情况下，spawner 文件夹内的文件夹结构不会反映至游戏内，**即仅有json文件的文件名以及该文件所在数据包命名空间组成其游戏内命名空间ID**。

#### ScSpawner

你在该部分所创建的所有json文件的**最顶层结构应当为ScSpawner**，所有其余的结构均隶属于ScSpawner中的某一个字段或子字段。

如果你需要一个对整体刷怪笼文件格式的概览，可以参考[一个例子](shua-guai-long-wen-jian.md#example)

```json
{
  "active_range": <float>
  "display": <DisplayConext>,
  "global_effects": <ScEffects>,
  "creatures": <ScCreatures>,
  "loot_table": <ResourceLocation>
}
```

* active\_range：一个常数字段，可填入整数或小数，默认值为64，用于描述当刷怪笼附近多少格范围内存在玩家时，刷怪笼将处于激活状态
* loot\_table：一个字符串，填入一个指向任意战利品列表的**命名空间ID**，默认值为原版古城箱子的战利品列表，用于描述当该刷怪笼生命周期结束后，生成的战利品箱内容
* display：见[DisplayContext](shua-guai-long-wen-jian.md#displaycontext)
* global\_effects: 见[ScEffects](sceffect.md)
* creatures: 见[ScCreatures](shua-guai-long-wen-jian.md#sccreatures)

#### DisplayContext

```json
{
    "img": <ResourceLocation>,
    "entity_color": <int>,
    "line_color": <int>,
    "head_color": <int>,
    "leg_color": <int>,
    
    "name": <string>,
    "rarity":<int>
}
```

* img：一个指向任意texture的命名空间ID，默认值为spcurs:spawner/default，本模组在<kbd>assets/spcurs/textures/spawner</kbd>文件夹中预先装载了部分可用纹理，通过以下方式使用：

```
“spcurs:spawner/x”, xy 应被替换为 default,drown,empty,port,recycle,stop
```

* entity\_color，line\_color，head\_color，leg\_color：一个ARGB/RGB颜色的十进制表示的整数；entity\_color用于描述游戏内的刷怪笼实体的渲染颜色；line\_color，head\_color，leg\_color分别描述一个刷怪笼配置对应于游戏中的物品的边框，上部，下部颜色（参考原版刷怪蛋物品）
* name：一个字符串，用于描述该刷怪笼配置在游戏内的显示名称；**注意，该名称将被直接显示，不作为一个可翻译键值存在**
* rarity：一个1\~6的整数（包含），用于描述刷怪笼配置的稀有度，影响物品显示颜色

#### ScCreatures

ScCreatures主要用于存储[ScCreature](shua-guai-long-wen-jian.md#sccreature)，即一个刷怪笼中每一个生物生成单元的配置信息。

```json
{
  "set": {
    <name1>:<ScCreature1>
    <name2>:<ScCreature2>
      }
}
```

* name: 一个任意字符串，符合MC命名空间ID要求即可

#### ScCreature

```
{
  "creature": <ResourceLocation>,
  "spawn_config": <SpawnConfig>,
  "effects": <ScEffects>
}
```

* creature: 一个命名空间ID，指向需要被生成的实体的**注册ID**；例如：原版的僵尸应当使用 <kbd>"minecraft:zombie"</kbd>或<kbd>"zombie"</kbd>；模组灾变的炎魔应当使用 <kbd>"cataclysm:ignis"</kbd>
* spawn\_config: 见[SpawnConfig](shua-guai-long-wen-jian.md#spawnconfig)
* effects: 见ScEffects

#### SpawnConfig

```
{
    "strategy": <enum.string>,
    "kill_amt": <int>,
    "create_amt": <int>,
    "existing_amt": <int>,
    "spawn_cnt": <int>,
    "first_spawn": <int>,
    "interval": <long>,
    "range": <float>
}
```

* strategy：一个字符串，应当且仅应当提供如下数个字段其一：
  * kill：代表该生成单元会持续生成生物，直至由该单元生成的生物的被击杀数量达到kill\_amt
  * create：代表该生成单元会持续生成生物，直至该单元生成生物数量达到create\_amt
  * limit：代表该生成单元会持续在**被刷怪笼追踪到的该生成单元生成的生物数量**（一般情况下，该值等同于场上存在的数量）小于existing\_amt时生成生物，直至由该单元生成的生物的被击杀数量达到kill\_amt
* kill\_amt，create\_amt，existing\_amt：一个整数，仅会受strategy的影响被使用，即可以仅定义strategy需要的键值
* spawn\_cnt：一个整数，该生成单元单次生成生物的生成尝试数，即单次生成的生物数量小于等于spawn\_cnt；**注意：strategy的判断是以生成单元每次生成为单位进行判断，这意味着生成单元实际生成量和实际存在量可能超出create\_amt，existing\_amt（最多超过spawn\_cnt）**
* first\_spawn：一个整数，在刷怪笼处于活动状态经过多少游戏刻（tick）后，该生成单元进行首次生成
* interval：一个整数，在刷怪笼处于活动状态时，每经过多少游戏刻（tick），该生成单元进行生成
* range：一个浮点数，该生成单元会尝试在以刷怪笼为中心，半径range的格数内尝试**随机**位置进行生成

#### ScEffects

ScEffects主要用于存储[ScEffect](sceffect.md)，其主要用于给刷怪笼产出的生物进行属性，效果，AI等等实体内容进行修正。

目前ScEffects主要由[ScSapwner](shua-guai-long-wen-jian.md#scspawner)中的global\_effects，[ScCreature](shua-guai-long-wen-jian.md#sccreature)中的effects和代码层面的effects追加进行提供，并以上述顺序**有序(包括set中的ScEffect排列顺序)**&#x5E94;用于实体。

```json
{
  "set": {
    <name1>:<ScEffect1>
    <name2>:<ScEffect2>
      }
}
```

* name: 一个**根据添加的effect种类不同，具有特殊要求**的命名空间ID，详细内容见具体的某个[ScEffect](sceffect.md)

### Example

```json
{
  //此处描述了该刷怪笼会在附近32格内有玩家，则进入激活状态
  //采用原版远古城市的战利品列表作为奖励箱内容物
  //采用如下纹理和颜色作为刷怪笼在世界中的显示；如下颜色，名称，稀有度作为物品显示
  "active_range": 32,
  "loot_table": "chests/ancient_city",
  "display": {
    "img": "spcurs:spawner/port",
    "entity_color": 16711680,
    "head_color": 9746258,
    "leg_color": 8941201,
    "name": "一个例子",
    "rarity": 4
  },
  //此处描述了由该刷怪笼生成的生物移动速度均会变为2倍
  "global_effects": {
    "set":{
      "attr-spd1": {
          "attr": "generic.movement_speed",
          "op": "MULTIPLY_TOTAL",
          "val": 2.0
      }
    }
  },
  "creatures": {
  //该刷怪笼具有2个生成单元
    "set": {
      //该生成单元描述了一组需要在刷怪笼16个半径内，以首次在第20个tick
      //每隔80tick尝试生成4只，总计需要产生30只的具有20点血量增益，60s的力量II的原版僵尸
      "c1":{
        "creature": "zombie",
        "spawn_config": {
          "create_amt": 30,
          "spawn_cnt": 4,
          "interval": 80,
          "strategy": "create",
          "range": 16,
          "first_spawn": 20
        },
        "effects": {
          "set": {
            "attr-life1": {
              "attr": "generic.max_health",
              "op": "ADDITION",
              "val": 20.0
            },
            "effc-power1": {
              "effectID": "strength",
              "level": 2,
              "duration": 1200,
              "visible": true
            }
          }
        }
      },
      //该生成单元描述了一组需要在刷怪笼24格半径内，以首次在第40个tick
      //每隔60tick尝试生成7只，总计需要杀死60只，最多同时在场10只的
      //优先攻击羊的原版普通骷髅
      "c2":{
        "creature": "skeleton",
        "spawn_config": {
          "kill_amt": 60,
          "existing_amt": 10,
          "spawn_cnt": 7,
          "interval": 60,
          "strategy": "limit",
          "range": 8,
          "first_spawn": 40
        },
        "effects": {
          "set": {
            "taradd-1": {
              "priority": 0,
              "target": "sheep",
              "must_see": false
            }
          }
        }
      }
    }
  }
}
```

