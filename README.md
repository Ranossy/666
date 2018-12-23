脚本编写说明
=======================================================

## 目录
  * [游戏对象](#游戏对象)
  * [常量](#常量)
  * [基础函数](#基础函数)
  * [角色信息](#角色信息)
  * [技能相关函数](#技能相关函数)
  * [Buff](#Buff)
  * [物品](#物品)
  * [门派相关](#门派相关)
  * [周围玩家](#周围玩家)
  * [队伍和关系](#队伍和关系)
  * [距离位置](#距离位置)
  * [目标](#目标)
  * [移动控制](#移动控制)
  * [地图和场景](#地图和场景)
  * [杂项](#杂项)
  * [回调函数](#回调函数)
  * [脚本完整结构](#脚本完整结构)

## 游戏对象
游戏中有很多种对象，我们只关注玩家对象和NPC对象。函数说明中参数如果是角色对象，那两者都可以，如果指明了是玩家对象，只能传入玩家，如果指明是NPC对象，就只能传入NPC对象。要注意，对象和对象ID是不同的，对象ID是一个数值，不是对象。

### 玩家对象
你和其他玩家控制的游戏角色都是玩家对象。下面是玩家对象的一些常用属性。<br>

| 成员变量 | 描述
| --- | ---
| dwID | 对象ID
| nCurrentLife | 当前气血
| nMaxLife | 最大气血
| nCurrentMana | 当前内力
| nMaxMana | 最大内力
| nCurrentRage | 苍云怒气，霸刀狂意，藏剑剑气，天策战意
| nCurrentEnergy | 霸刀刀魂，唐门神机值
| nAccumulateValue | 少林禅那，纯阳气点，七秀剑舞层数
| nCurrentSunEnergy | 日灵（是看到的100倍），霸刀气劲
| nCurrentMoonEnergy | 月灵（是看到的100倍）
| nSunPowerValue | 满日（大于0满，否则不满）
| nMoonPowerValue | 满月（大于0满，否则不满）
| nPoseState | 姿态
| nMoveState | 移动状态
| dwForceID | 门派ID
| bFightState | 是否战斗状态
| szName | 名字
| nLevel | 等级
| nX | x坐标
| nY | y坐标
| nZ | z坐标
| nCurrentStamina | 精力
| nCurrentThew | 体力
| nCamp | 阵营（0中立，1浩气，2恶人）
| bOnHorse | 是否在马上
| nRunSpeed | 跑速
| nHasteBase | 加速等级

示例:
```Lua
--脚本中的Main函数，这个函数必须实现，不然载入会报错
--插件每秒调用这个函数16次，1个参数是自己的玩家对象
--下面示例演示如何访问对象的属性
function Main(player)
    --判断自己是否被减速
    if player.nRunSpeed < 20 then
        print("被减速")
    end

    --输出自己的名字，等级
    print(player.szName, player.nLevel)

    --输出自己的位置
    print(player.nX, player.nY, player.nZ)
end
```

### NPC对象
非玩家控制的角色都是NPC对象，例如宠物、机关、气场等等，一部分NPC是没名字的。<br>

| 成员变量 | 描述
| --- | ---
| dwID | 对象ID
| dwTemplateID | 模板ID
| nCurrentLife | 当前气血
| nMaxLife | 最大气血
| nCurrentMana | 当前内力
| nMaxMana | 最大内力
| nLevel | 等级
| bFightState | 是否战斗状态
| szName | 名字
| nIntensity | 强度
| nMoveState | 移动状态
| nX | x坐标
| nY | y坐标
| nZ | z坐标

注意：不要把模板ID和对象ID搞错，模板ID表示一类NPC，对象ID表示一个特定的NPC。比如两个NPC都叫山贼，样子什么都一样，他们的模板ID就是相同的（也有可能不相同，虽然看上去一样，但是模板ID也可能不同），但是他们的对象ID绝不相同。函数中指定参数是模板ID，一定要传入模板ID。

## 常量

### 目标和对象类型

| 常量名 | 说明
| --- | ---
| NONE | 没有目标 |
| COORD | 目标是一个XYZ坐标
| NPC | NPC
| PLAYER | 玩家

示例:
~~~Lua
function Main(player)
    --判断自己的当前目标类型
    local target, tClass = GetTarget(player)        --获取自己的当前目标，返回目标对象（玩家或NPC），类型
    if target then
        if tClass == NPC then
            print("当前目标是NPC")
        else
            print("当前目标是玩家")
        end
    else
        print("当前没有目标")
    end
end
~~~


## 基础函数

---
### print
功能：打印信息，这是最基本的调试手段，脚本出错了，多用这个函数输出变量看看<br>
参数：任意。<br>
没有返回值。<br>
示例：
~~~Lua
print("Hello", 123)
~~~

---
### output
功能：打印表，和上面的区别是不用自己遍历表，可以输出整个表的信息<br>
参数：任意<br>
没有返回值。

---
### GetTickCount
功能：获取当前的一个毫秒数。
没有参数。
1个返回值

---
### GetLogicFrameCount
功能：获取当前游戏的帧计数值。<br>
没有参数。<br>
1个返回值：当前帧数。<br>
时间单位转换：1秒 = 1000 毫秒， 1秒 = 16 帧， 1帧 = 62.5 毫秒 <br>

## 角色信息

---
### GetAttr
功能：获取角色的常用信息。<br>
1个参数：角色对象。<br>
18个返回值：看示例<br>

~~~Lua
--获取自己的常用信息
local life, lifev, mana, manav, rage, energy, qidian, sun, moon, sun_power, moon_power, fight, horse, state, school, mount, pose, mid = GetAttr(player)
print(life, lifev, mana, manav, rage, energy, qidian, sun, moon, sun_power, moon_power, fight, horse, state, school, mount, pose, mid)

--和游戏中变量名字一样的含义相同
--lifev 当前生命值
--manav 当前内力值
--fight 是否战斗状态
--horse 是否在马上
--state 移动状态（看后面说明）
--school 门派名（少林，万花等等）
--mount 内功名（分山劲，冰心诀等等）
--pose 姿态。("擎刀","擎盾","高山流水","阳春白雪","平沙落雁"，"松烟竹雾"，"秀明尘身","雪絮金屏")
--mid 参数中指定对象的ID，这里是自己的对象ID。
~~~

---
### GetState
功能：获取指定角色对象的移动状态。<br>
1个参数：角色对象或ID。<br>
1个返回值：下面的状态名之一。<br>
"未知", "站立", "走路", "跑步", "跳跃", "游泳", "漂浮", "打坐", "击倒", "击退", "击飞", "眩晕", <br> 
"定身", "锁足", "交通", "重伤", "冲刺", "被抓", "爬起", "滑行", "轻功", "位移", "僵直", <br>
"收招", "飞行", "登顶", "浮空"<br>
"交通": 坐马车之类的情况是这个状态。<br>
"浮空": 蓬莱撑伞上天是这个状态。<br>
~~~Lua
--判断目标是不是某些状态，这里假设有目标，并且是玩家，没做判断
local target, tClass = GetTarget(player)
local tstate = GetState(target)
--如果目标是 眩晕定身锁足
if string.find("眩晕|定身|锁足", tstate) then
end
~~~

---
### GetSchool
功能：获取指定玩家对象的门派。<br>
1个参数：玩家对象或ID。<br>
1个返回值：指定玩家对象的门派名。<br>
~~~Lua
local tschool = GetState(target)
--如果目标是万花
if tschool == "万花" then
    ...
end
--如果目标是指定的门派
if string.find("七秀|五毒|长歌", tschool) then
    ...
end
~~~

---
### GetMount
功能：获取指定玩家对象的内功。<br>
1个参数：玩家对象或ID。<br>
1个返回值：内功名。<br>
~~~Lua
--如果指定的玩家对象是指定内功之一
local tmount = GetMount(target)
if string.find("云裳心经|补天诀|相知|离经易道"， tmount) then
    ...
end
~~~

---
### GetPose
功能：获取指定玩家对象姿态。<br>
1个参数：玩家对象或ID。<br>
1个返回值：姿态名。<br>
"擎刀", "擎盾", "高山流水", "阳春白雪", "平沙落雁"， "松烟竹雾"， "秀明尘身", "雪絮金屏"<br>
说明：这个要对应的门派才有用，不是对应门派，姿态没意义。<br>
~~~Lua
--如果自己是擎刀姿态
local pose = GetPose(player)
if pose == "擎刀" then
    ...
end
~~~

## 技能相关函数

---
### Cast
功能：使用技能。<br>
3个参数：<br>
参数1：技能名（字符串）或ID（数值）。<br>
参数2：是否对自己使用(bool)。不同类型的技能这个参数含义不同，需要一个位置的技能（天绝地灭之类），如果为true，是放在自己脚下，否则放在目标脚下。是需要目标的技能，如果为true是对自己释放，否则是对目标释放。如果是蓄力技能，如果为true，是完整释放，否则秒放。<br>
参数3：是否忽略读条。(bool)<br>
1个返回值：是否使用成功。（本地判断所有条件OK，并且向服务端发包成功，返回true，如果要精确判断自己放了什么技能，在回调函数中处理）<br>
说明：如果是读条和蓄力技能，会结束本次Main函数执行，不会再往下执行。
~~~Lua
--对目标释放
Cast("冰蚕牵丝")
--对自己释放
Cast("冰蚕牵丝", true)
--对自己释放，无视读条
Cast("冰蚕牵丝", true, true)
~~~

---
### CastXYZ
功能：在指定位置释放技能。<br>
4个参数：技能名或ID，x坐标，y坐标，z坐标，是否忽略读条。<br>
1个返回值：是否释放成功

---
### CastTo
功能：面向指定角色对象释放技能。<br>
3个参数：技能名或ID，目标角色对象或ID，是否忽略读条<br>
1个返回值：是否释放成功<br>
说明：当前目标不变。

---
### CastBack
功能：背对指定角色对象释放技能。<br>
3个参数：技能名或ID，目标角色对象或ID，是否忽略读条<br>
1个返回值：是否释放成功<br>
说明：当前目标不变。

---
### StopAction
功能：打断自己读条。<br>
没有参数。<br>
没有返回值。<br>

---
### GetPrepare
功能：获取指定角色对象的读条数据。<br>
1个参数：角色对象或ID。（不指定获取自己读条数据）<br>
2个返回值：正在读条的技能名，剩余时间（秒）。<br>
~~~Lua
--获取自己读条读条信息
local mota, motatime = GetPrepare()
--获取指定角色对象的读条数据
local oota, ootatime = GetPrepare(obj)
~~~

---
### GetSkillCD
功能：获取自己技能CD剩余时间。<br>
1个参数：技能名或ID。<br>
1个返回值：CD剩余时间，不在CD返回 0。<br>

---
### GetSkillCN
功能：获取自己充能技能可用次数。<br>
1个参数：技能名或ID。<br>
1个返回值：可用次数。<br>

---
### GetSkillOD
功能：获取自己透支技能可用次数。<br>
1个参数：技能名或ID。<br>
1个返回值：可用次数。<br>

---
### GetCastTime
功能：获取指定对象释放指定技能时间。<br>
2个参数：角色对象或者ID， 技能ID(只能是ID)。<br>
1个参数：技能释放到现在的时间（秒）,没放过返回-1。<br>
~~~Lua
--假设obj是一个角色对象，技能ID是123
local time123 = GetCastTime(obj, 123)
--5秒内放过指定技能，注意要判断大于等于0
if time123 >= 0 and time123 < 5 then
    ...
end
~~~

---
### GetCastCount
功能：获取指定对象指定时间内释放指定技能的次数。<br>
3个参数：角色对象或ID， 技能ID，时间（单位：秒）<br>
1个返回值：释放次数，没放过返回0。<br>

---
### GetTalentInfo
获取自己的奇穴信息。<br>
没有参数。<br>
1个返回值：表。
~~~Lua
local talent = GetTalentInfo()
--判断有没有指定的奇穴
if talent["奇穴名"] then
    ...
end
~~~

## Buff

---
### GetBuff
功能：获取指定角色对象buff数据。<br>
1个参数：角色对象或ID。只能获取和自己有关系的角色的buff数据（自己，目标，目标的目标，队友）<br>
1个返回值：buff数据表。作为下面函数的参数，或者你自己想在里面找指定数据也行。<br>
~~~Lua
--获取自己的buff数据
local mbuff = GetBuff(player)
--获取目标的buff数据
local tbuff = GetBuff(target)
~~~

---
### GetBuffTime
功能：获取指定buff的剩余时间。<br>
3个参数：buff数据表，buff名，是否我造成的（可选）。<br>
1个返回值：剩余时间（秒），多个buff的话返回最长时间，没有返回 0。<br>
~~~Lua
--接上面的示例，获取目标免控buff剩余时间
local mktime = GetBuffTime(tbuff, "免控名字1|免控名字2|免控名字3")

--获取我对目标造成的buff剩余时间
local bufftime = GetBuffTime(tbuff, "buff名", true)
~~~

---
### GetBuffStack
功能：获取buff堆叠层数<br>
3个参数：和上面函数一样。（buff名不支持列表，只能是单个）。<br>
1个返回值：层数，没有返回 0。<br>

---
### CancelBuff
功能：取消自己的指定buff。<br>
两个参数：自己的buff数据表，buff名字或ID。<br>
没有返回值。<br>
~~~Lua
--下马
CancelBuff(mbuff, "骑御")
~~~

---
### 判断角色对象有没有指定buff
这是角色对象的一个方法，有时候只想判断对象有没有特定的某个buff，可以调用这个函数，不用再先GetBuff，然后再判断时间。只能用buffid。<br>
2个参数：buffid，等级（如果不关心等级，传0）。<br>
1个返回值：true 或 false<br>
~~~Lua
---判断某个角色对象有没有buffid为123的buff，等级基本上都传 0
if obj.IsHaveBuff(123, 0) then  --如果有指定buff
    ...
end
~~~


## 物品

---
### Use
功能：使用物品。<br>
1个参数：物品名称。<br>
1个返回值：是否使用成功。<br>
~~~Lua
Use("金疮药")
~~~

---
### UseEquip
功能：穿上背包中的装备。<br>
1个参数：装备名。<br>
没有返回值。<br>

## 门派相关

---
### FindNpc
功能：获取符合条件的NPC对象和数量。<br>
4个参数：<br>
参数1：玩家对象或者ID（以这个对象为中心）<br>
参数2：NPC名字（支持多个，没有名字用模板ID，名字和模板ID在调试信息里看）<br>
参数3：范围（尺） <br>
参数4：关系（"自己", "友好", "敌对", "同盟", "队友"）<br>
2个返回值：最近的1个npc对象（没有返回nil），符合条件NPC的数量<br>
气场、影子、各种圈都可以用这个函数。<br>

---
### 获取指定五毒玩家的宠物NPC对象
这是一个玩家对象的方法。没有参数，1个返回值是五毒的宠物NPC对象，如果没有返回nil。
~~~Lua
--获取自己的宠物
local pet = player.GetPet()
--如果自己没有宠物
if not pet then
    ...
end
--判断自己当前招的哪种宠物
if pet.szName == "圣蝎" then

elseif pet.szName == "天蛛" then

elseif ... then
    ...
end
~~~

---
### GetPuppet
功能：获取自己千机变数据。<br>
没有参数。<br>
2个返回值：千机变NPC对象（没有返回nil），剩余时间（秒）。<br>
~~~Lua
local puppet, puppettime = GetPuppet()
--如果没有千机变
if not puppet then
    ...
end
--判断类型
if puppet.szName == "机关千机变底座" then
    ...
elseif puppet.szName == "机关千机变连弩" then
    ...
elseif puppet.szName == "机关千机变重弩" then
    ...
end

--如果剩余时间小于5秒
if puppettime < 5 then

end
~~~

---
### GetBomb
功能：获取指定对象周围6尺自己暗藏杀机机关的数量<br>
1个参数：角色对象或者ID（以他为中心）<br>
1个返回值：数量。<br>

---
### GetShadow
功能：获取自己影子和飞星遁影机关信息。<br>
没有参数。<br>
2个返回值：数量，影子数据表（键是序号，值是表）。<br>

| 键 | 说明
|--- | ---
| nDist | 和自己的距离
| nLeftTime | 剩余时间（秒）
| bCanUse | 是否能使用（距离太远或实现不可达就不能用）
| nIndex | 索引
| dwSkillID | 对应的技能ID

<b>注释：这个接口觉得不是很方便，你们有什么需求再添加。</b>

---
### GetBody
功能：获取指定长歌玩家的本体NPC对象。<br>
1个参数：玩家对象或ID。<br>
1个返回值：本体NPC对象，没有返回nil。<br>

---
### GetControlPlayer
获取被自己控制（平沙）的玩家对象。<br>
没有参数。<br>
1个返回值：玩家对象，没有返回nil。<br>

## 周围玩家

---
### GetSeeMe
获取指定范围内目标是我的敌对玩家信息。<br>
1个参数：范围（单位：尺）。<br>
2个返回值：数量，表。<br>
~~~Lua
--获取20尺内目标是我的敌对玩家信息
local seemecount, seemet = GetSeeMe(20)
--如果有超过3个人目标是我
if seemecount > 3 then
    ...
end
--目标是我的敌人里有毒经
if seemet["毒经"] then

end
--有两个明教目标都是我（先判断有，再判断数量）
if seemet["焚影圣诀"] and seemet["焚影圣诀"] >= 2 then

end
~~~

## 队伍和关系

---
### IsAlly
是否同盟。<br>
1个参数：角色对象或ID。<br>
1个返回值：是(true)否(false)<br>

---
### IsEnemy
是否敌对。同上。

---
### IsNeutrality
是否中立。同上

### IsParty
是否队友。同上。

---
### GetRela
获取任意两个角色对象的关系。<br>
2个参数：第一个角色对象或ID，第二个角色对象或ID。<br>
1个返回值：关系。("自己", "友好", "敌对", "同盟", "队友")<br>

---
### 是否有队伍
玩家对象的方法。没有参数。
~~~Lua
if player.IsInParty() then
    ...
end
~~~

---
### GetTeamInfo
获取自己的队伍信息。<br>
没有参数。<br>
2个返回值：队伍中的人物，表。<br>
~~~Lua
local teamCount, teamInfo = GetTeamInfo()
print(teamCount)    --输出队伍人数
--如果队伍中有惊羽
if teamInfo["惊羽诀"] then
end
~~~

---
### FindTeamMember
遍历自己队伍中的玩家。<br>
1个参数：函数。<br>
1个返回值：队伍总人数（无论死活是否在线）。<br>
~~~Lua
--下面代码演示如果获取队伍中符合条件的玩家

--先定义一些变量
local pMinHp = nil    --血量百分比最少的队友
local nMinHp = nil    --血量百分比最小值

local pCount = 0         --符合条件的玩家数量
local pCountHp = 0       --符合条件的玩家血量百分比和
local pCountAVG = 1      --血量百分比平均值
local pbuffxxx = nil     --有个某个特定buff的队友

--定义一个函数，FindTeamMember会调用这个函数，每次传入一个队友的玩家对象
local findfunc = function(member)        --member是团队中的玩家对象
    if GetDist(player, member) > 20 then return end  --距离大于20尺，不处理
    if not IsVisible(player, member) then return end     --视线不可达，不处理
    if member.nMaxLife > 200000 then return end       --最大血量大于20万不处理（对奶妈来说，不想选上了小车的人）
	
	if member.IsHaveBuff(9695, 0) then return end  --有清绝影歌，不处理
    if member.IsHaveBuff(10212, 0) then return end  --有扬旌沙场（决斗），不处理
    
    if member.IsHaveBuff(xxx, 0) then      --有某个重要的的buff或debuff的队友
        pbuffxxx  = member
    end

    --记录血量百分比最少的队友
    local memberHp = member.nCurrentLife / member.nMaxLife
    if not nMinHp or memberHp < nMinHp then
        nMinHp = memberHp       --记录最小血量百分比的队友
        pMinHp = member
    end

    --记录符合条件的人数和血量百分比和
    pCount = pCount + 1
    pCountHp = pCountHp + memberHp
end

--遍历队伍成员，把团都中所有成员的玩家对象作为参数(自己、离线、挂了的除外)，
--调用指定的函数（这里是findfunc）
local teamcount = FindTeamMember(findfunc)
--返回的队伍总人数，无论是否在线，是否活着

--处理团队平均血量
if pCount > 0 then        --必须判断大于0，不然可能立即崩溃
    pCountAVG = pCountHp / pCount
    if pCountAVG < 0.4 then     --团队整体血量较低，可能要开爆发了
        ...
    end
end

--如果有某个指定buff的队友，对他进行处理
if pbuffxxx then
    ...
end

if pMinHp then    --如果有最低血量的队友
    if nMinHp < 0.3 then        --如果他血量低于30%

    elseif nMinHp < 0.4 then   --低于40%

    elseif nMinHp < 0.5 then

    else ... then

    end
end

--输出信息
print(pMinHp, nMinHp, mCount, mCountHp, mCountAVG)
~~~

## 距离位置

---
### GetDist
获取两个对象的距离。<br>
2个参数：对象1，对象2.<br>
1个返回值：距离（尺）。<br>

---
### GetDist2D
获取两个对象的二维距离（不计算Z轴）。<br>
参数同上。<br>
返回值同上。<br>

---
### IsVisible
判断两个对象是否视线可达。<br>
2个参数：对象1，对象2.<br>
1个返回值：true 或 false<br>

---
### GetMyHeight(废弃，用GetHeight)
获取自己离地面的高度。<br>
没有参数。<br>
1个返回值：高度（尺）。<br>

---
### GetTarHeight(废弃，用GetHeight)
获取目标离地面的高度。<br>
没有参数。<br>
1个返回值：高度（尺）。<br>

---
### GetHeight
获取任意玩家对象距离地面的高度。<br>
1个参数：玩家对象或ID。<br>
1个返回值：高度（尺）。<br>

---
### GetDiffZ
获取自己和指定对象的高度差。<br>
1个参数：角色对象。<br>
1个返回值：高度差。<br>

---
### IsFront
自己是否在指定对象的前方（他是否面向自己）。<br>
1个参数：角色对象或ID。
1个返回值：是(true)或否(false)。

---
### IsBack
自己是否在指定对象背后（他是否背对自己）。<br>
1个参数：角色对象或ID。
1个返回值：是(true)或否(false)。

---
### TurnTo
面向指定对象。<br>
1个参数：角色对象或ID。<br>
没有返回值。<br>

---
### BackTo
背对指定对象。<br>
1个参数：角色对象或ID。<br>
没有返回值。<br>

## 目标

---
### GetTarget
获取指定对象的目标。<br>
1个参数：角色对象或ID。<br>
2个返回值：角色对象，对象类型。<br>
~~~Lua
local target, tclass = GetTarget(player)    --获取我的目标对象和类型
if not target then return end
if tclass == PLAYER then    --如果自己的当前目标是玩家
    ...
end
local ttarget, ttclass = GetTarget(target)  --获取目标的目标对象和类型
if not ttarget then return end
if ttarget.dwID = player.dwID then  --如果目标的目标是自己
    ...
end

local tttarget, tttclass = GetTarget(ttarget)   --获取目标的目标的目标对象和类型
~~~

---
### SetTarget
设置自己的当前目标。<br>
1个参数：角色对象或ID。<br>
没有返回值。
~~~Lua
--假设obj是任意一个角色对象
SetTarget(obj)  --设置自己的当前目标为指定对象
~~~

---
### SetPrevTarget
选择上一个目标，选了A，然后选B，调用这个函数就会选回A。<br>
没有参数，没有返回值。<br>

---
### GetMyID
没有参数，1个返回值：自己的对象ID。<br>

---
### GetMyPlayer
没有参数，1个返回值：自己的玩家对象。<br>

---
### GetTargetID
没有参数，1个返回值：自己当前目标的对象ID。<br>

## 移动控制
下面这些函数都没有参数和返回值。

---
### MoveToTarget
向当前目标移动。

---
### FollowTarget
跟随当前目标。

---
### MoveForwardStart
开始向前移动。

---
### MoveForwardStop
停止向前移动。

---
### MoveBackwardStart
开始向后移动。

---
### MoveBackwardStop
停止向后移动。

---
### StrafeLeftStart
开始向左移动。

---
### StrafeLeftStop
停止向左移动。

---
### StrafeRightStart
开始向右移动。

---
### StrafeRightStop
停止向右移动。

---
### Jump
跳

---
### MoveAction_StopAll
停止所有移动。

## 地图和场景
下面这些函数都没有参数，1个返回值。

---
### GetMapID
获取地图ID。

---
### GetMapName
获取地图名。

---
### IsInArena
是否在JJC。

---
### IsInDungeon
是否在副本。

---
### IsInBattle
是否在战场。

---
### IsInTreasure
是否在吃鸡。

---
### IsInZombie
是否在李渡。

## 杂项

---
### AddOption
在脚本选项菜单中添加一个选项。<br>
1个参数：选项名。<br>
没有返回值。<br>
<b>注意：不要在函数里面调用这个函数，在放在函数外面，载入脚本的时候只执行一次。</b>
~~~Lua
--添加一个选项
AddOption("选项一")
~~~

---
### GetOption
获取选项的选中状态。<br>
1个参数：选项名。<br>
1个返回值：如果勾选了返回true, 否则返回 false。<br>
~~~Lua
--如果指定的选项是选中状态
if GetOption("选项一") then

end
~~~

---
### Delay
延时。<br>
1个参数：时间（毫秒）。<br>
没有返回值。<br>
说明：调用后会结束Main函数执行，超时之后从Main函数的第一行开始执行。<br>
~~~Lua
Delay(500)      --等待半秒钟
~~~

## 回调函数

---
### OnPrepare
任意角色对象开始读条，会调用脚本中这个函数。
~~~Lua
function OnPrepare(CasterID, dwSkillID, dwLevel, nLeftFrame, tClass, tIDnX, nY, nZ)
    --CasterID 开始读条的角色ID
    --dwSkillID 技能ID
    --dwLevel 技能等级
    --nLeftFrame 剩余帧数
    --tClass 技能释放的目标类型(COORD, NPC, PLAYER)，这个和技能类型有关
    --tIDnX 如果是需要目标的技能，这个是技能的目标ID，如果是需要一个位置释放的技能，这个是x坐标
    --nY y坐标，不是COORD类型无效
    --nZ z坐标，不是COORD类型无效
    --比如你知道一个技能ID是123，是需要目标的技能，判断是不是对自己释放
    local myid = GetMyID()
    if dwSkillID == 123 and tIDnX == myid then  --如果有人开始读条123技能，目标是自己
        CastTo("某个打断技能", CasterID, true)  --打断他读条
    end

    print(CasterID, dwSkillID, dwLevel, nLeftFrame, tClass, tIDnX, nY, nZ)  --输出读条信息
    
end
~~~

---
### OnCast
任意角色对象释放技能，会调用脚本中这个函数。
~~~Lua
function OnCast(CasterID, dwSkillID, dwLevel, nPastFrame, tClass, tIDnX, nY, nZ)
    --CasterID 释放技能的角色ID
    --dwSkillID 技能ID
    --dwLevel 技能等级
    --nPastFrame 从服务端发出到现在已过去的帧数
    --tClass 技能释放的目标类型(COORD, NPC, PLAYER)，这个和技能类型有关
    --tIDnX 如果是需要目标的技能，这个是技能的目标ID，如果是需要一个位置释放的技能，这个是x坐标
    --nY y坐标，不是坐标类型的技能无效
    --nZ z坐标，不是坐标类型的技能无效
    --比如有个技能123，类似撼地之类的技能，其实是个二段技能，起跳，落地之后才会造成眩晕
    --起跳到落地有8帧（0.5秒），你想躲这个技能（聂云，后跳什么的）
    local myid = GetMyID()
    if dwSkillID == 123 and tIDnX == myid then  --如果是对我释放，这个是起跳阶段的技能ID
        --如果过去的帧数小于4，总共8帧，大于4说明网速太差，已经躲不掉了
        --这里设为4只是演示，实战中要自己尝试
        if nPastFrame < 4 then
            Cast(9007)  --后跳
        end
    end

    print(CasterID, dwSkillID, dwLevel, nPastFrame, tClass, tIDnX, nY, nZ)  --输出释放信息
end
~~~

## 脚本完整结构
~~~Lua
--初始化部分，载入的时候会执行，不要放在函数里面
--设置一些选项
AddOption("选项一")
AddOption("选项二")
AddOption("选项三")

--脚本描述和说明，比如设置哪些些奇穴之类的，换行用\n
--这个在插件菜单中的脚本说明中会显示
szScriptDesc = "作者：某某某\n说明：脚本说明\n奇穴：奇穴1，奇穴2"


--Main函数，1个参数是自己的玩家对象，每秒调用16次
function Main(player)

end


--释放技能回调函数，任意对象释放技能时调用
function OnCast(CasterID, dwSkillID, dwLevel, nPastFrame, tClass, tIDnX, nY, nZ)

end


--开始读条回调函数，任意对象开始读条时调用
function OnPrepare(CasterID, dwSkillID, dwLevel, nLeftFrame, tClass, tIDnX, nY, nZ)

end
~~~
