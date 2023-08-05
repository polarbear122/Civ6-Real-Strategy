# 文明6真实战略模组核心修改总结 (版本 b5cb3e6)

## 1. 居民点选址策略核心调整
```sql
-- 反向大陆定居惩罚提升至-4分
('StandardSettlePlot', 'Foreign Continent', 1, -4, ...)

-- 邻近友城距离惩罚强化：-8基础分+动态评估
UPDATE PlotEvalConditions SET PoorValue=-40, GoodValue=-16 WHERE ConditionType = 'Nearest Friendly City';

-- 资源优先级强化：
('StandardSettlePlot', 'Specific Resource', 0, 8, 'RESOURCE_URANIUM') -- 铀矿最高权重
('StandardSettlePlot', 'Resource Class', 0, 5, 'RESOURCECLASS_STRATEGIC') -- 战略资源权重提升
```

## 2. AI收益偏好重构
```sql
-- 核心生产倾向强化：
UPDATE AiFavoredItems SET Value = 20 WHERE Item = 'YIELD_PRODUCTION'; (原25 → 调整为更激进生产导向)

-- 伪收益系统重构：
PSEUDOYIELD_CITY_DEFENSES 从200 → 40（早期防御重视度下调）
PSEUDOYIELD_UNIT_AIR_COMBAT 从2.2 → 3.5（空军重要性提升）
PSEUDOYIELD_GPP_MERCHANT 从0.5 → 0.7（商人GPP优先级提升）
```

## 3. 动态策略系统
### 防御策略 (RST_STRATEGY_DEFENSE)
- 激活条件：Lua函数`ActiveStrategyDefense`阈值70%
- 核心调整：
  ```sql
  -- 强化防御建设：
  ('RSTDefensePseudoYields', 'PSEUDOYIELD_CITY_DEFENSES', 1, 200)
  
  -- 战斗单位优先：
  ('RSTDefensePseudoYields', 'PSEUDOYIELD_UNIT_COMBAT', 1, 50)
  
  -- 限制非必要建设：
  ('RSTDefensePseudoYields', 'PSEUDOYIELD_WONDER', 1, -100)
  ```

### 科技优先策略 (RST_STRATEGY_SCIENCE)
```sql
-- 强化科研导向：
('RSTScienceYields', 'YIELD_SCIENCE', 1, 25)

-- 优先建设大学区：
('RSTScienceDistricts', 'DISTRICT_CAMPUS', 1, 0)
```

### 文化扩张策略 (RST_STRATEGY_CULTURE)
```sql
-- 文化收益权重提升：
('RSTCultureYields', 'YIELD_CULTURE', 1, 25)

-- 艺术家GPP优先：
('RSTCulturePseudoYields', 'PSEUDOYIELD_GPP_ARTIST', 1, 15)
```

## 4. 关键行为修正
- **海军平衡调整**：
  ```sql
  UPDATE PlotEvalConditions SET PoorValue = -1, GoodValue = 8 WHERE ConditionType = 'Coastal';
  ```
- **单位建造倾向**：
  ```sql
  PSEUDOYIELD_UNIT_TRADE → 4.0（原1 → 强化贸易单位建设）
  PSEUDOYIELD_UNIT_COMBAT → 1.4（战斗单位基础倾向提升）
  ```

## 5. 版本信息
- 最新提交：`b5cb3e6587e32fae035620de92c403d591dbb561`（2025-09-21）
- GitHub仓库：`git@github.com:polarbear122/Civ6-Real-Strategy.git`
