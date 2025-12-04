# 🎬 VTS 视觉设计系统

**Document ID:** 03_VISUAL_DESIGN.md  
**Status:** Draft for Review  
**Last Updated:** 2025-12-04

---

## 目录

1. [3D 卡片动画](#1-3d-卡片动画)
2. [雷达图 (Radar Chart)](#2-雷达图-radar-chart)
3. [中文字体与多语言](#3-中文字体与多语言)
4. [色彩系统](#4-色彩系统)
5. [游戏特定 Cartridge 设计](#5-游戏特定-cartridge-设计)

---

## 1. 3D 卡片动画

### 1.1 动画概述

**目标：** 当用户点击排行榜中的角色头像时，触发**电影级的 3D 卡片飞出动画**，展示角色详情卡。

**技术栈：**
- **R3F** (React Three Fiber) 处理 3D 渲染
- **Framer Motion** 协调动画时序
- **Three.js** 基础数学库

### 1.2 动画序列详解

#### 阶段 1: 触发 (0-50ms)

当用户点击头像时，触发以下变化：

```
头像动画：
  scale: 1.0 → 0.8 (缩小视觉反馈)
  opacity: 1.0 → 0.7
  
背景开始渐变：
  opacity: 0 → 0.2 (半透明遮罩淡入)
  blur: 0px → 8px
```

**实现代码示例：**
```javascript
// src/components/TierlistCard.tsx
const handleCharacterClick = async (characterId) => {
  // 播放头像缩小动画
  await animate(headerRef.current, 
    { scale: 0.8, opacity: 0.7 },
    { duration: 0.05 }
  );
};
```

#### 阶段 2: 飞出 (50-150ms)

3D 卡片从头像位置飞出，旋转侧放：

```
卡片路径：
  初始位置：点击的头像中心
  最终位置：屏幕右侧 (X: 60% of viewport width, Y: center)
  
卡片旋转：
  rotateY: 0° → 15°   (Y 轴旋转，制造"侧放"效果)
  rotateZ: 0° → -5°   (Z 轴微调，增加动感)
  
卡片缩放：
  scale: 0.6 → 1.0 (从小到大展开)
  
视觉效果：
  阴影变化：阴影渐变更深，制造立体感
  光源方向：从上-左方射来
```

**时间曲线：** `easeOut` (先快后慢，符合物理直觉)

**实现代码示例：**
```typescript
// src/components/CharacterDetailCard/DetailCardAnimation.tsx

const CardFlyOutAnimation = ({ 
  sourcePosition, 
  onAnimationComplete 
}: Props) => {
  const cardRef = useRef(null);
  const [isAnimating, setIsAnimating] = useState(true);
  
  useEffect(() => {
    const animation = animate(
      cardRef.current,
      {
        x: sourcePosition.x + (window.innerWidth * 0.6 - sourcePosition.x),
        y: sourcePosition.y + (window.innerHeight * 0.5 - sourcePosition.y),
        rotateY: 15,
        rotateZ: -5,
        scale: 1.0,
      },
      {
        duration: 0.1, // 100ms
        easing: "easeOut",
        onComplete: () => {
          setIsAnimating(false);
          onAnimationComplete();
        }
      }
    );
  }, [sourcePosition]);
  
  return (
    <motion.div ref={cardRef} style={{ perspective: 1200 }}>
      <div className="character-card">
        {/* 卡片内容 */}
      </div>
    </motion.div>
  );
};
```

#### 阶段 3: 停靠 (150-250ms)

卡片停靠到屏幕右侧，同时背景完全淡入：

```
卡片最终状态：
  位置：屏幕右侧，距右边缘 60px
  垂直：屏幕中心（Y: 50%）
  旋转：保持 rotateY: 15°, rotateZ: -5°（模拟停靠角度）
  
背景遮罩：
  opacity: 0.2 → 0.6 (完全遮罩)
  blur: 8px → 12px
  
卡片阴影：
  shadow: (10, 20, 30) 黑色 0.4 透明度
  目的：制造卡片"浮起"感，与背景分离
```

**实现代码示例：**
```typescript
// 最终卡片位置
const finalCardPosition = {
  right: 60, // 距右边缘
  top: '50%',
  transform: 'translateY(-50%) rotateY(15deg) rotateZ(-5deg)',
};
```

### 1.3 关闭动画 (反向 200ms)

当用户点击背景或关闭按钮时：

```
反向播放飞出过程：
  1. 卡片从停靠位置回到头像位置
  2. 旋转恢复到 0°
  3. 缩放回 0.8
  4. 背景透明度回 0
  5. 头像恢复正常大小

时间：200ms
曲线：easeIn (慢快快)
```

### 1.4 光源与阴影设计

**光源设置（R3F）：**

```typescript
// src/components/Canvas/CardLighting.tsx

const CardLighting = () => {
  return (
    <>
      {/* 主光源：从左上方照射 */}
      <directionalLight 
        position={[10, 10, 5]} 
        intensity={1.2}
        castShadow 
      />
      
      {/* 填充光：右下方柔和光 */}
      <pointLight 
        position={[-10, -5, 3]}
        intensity={0.6}
        color="#aaccff"
      />
      
      {/* 背景光：整体补光 */}
      <ambientLight intensity={0.4} />
    </>
  );
};
```

**卡片阴影效果：**

```css
.character-card {
  box-shadow: 
    10px 20px 30px rgba(0, 0, 0, 0.4),      /* 主阴影 */
    0px 8px 16px rgba(0, 0, 0, 0.2),        /* 轮廓阴影 */
    inset -2px -2px 8px rgba(0, 0, 0, 0.1); /* 内部阴影 */
  
  /* 3D 透视 */
  perspective: 1200px;
  transform-style: preserve-3d;
}
```

---

## 2. 雷达图 (Radar Chart)

### 2.1 设计规范

**用途：** 在角色详情卡底部展示角色属性的 6 维度对比。

**尺寸：** 200×200px（可缩放）

**技术方案：** 使用 SVG + Recharts / Visx（轻量级图表库）或自绘

### 2.2 游戏特定的雷达维度

#### P5X (Persona 5: The Phantom X)

```
维度：6 个
  1. 攻击 (Attack) - 顶部
  2. 命中 (Hit Rate) - 右上
  3. 暴击 (Crit) - 右下
  4. 防御 (Defense) - 底部
  5. 回避 (Evasion) - 左下
  6. 生命 (HP) - 左上

数值范围：0-100
颜色：深红色（#8B0000） + 半透明填充
背景网格：灰色 (#CCCCCC)
```

**可视化示例：**
```
        攻击
       / \
      /   \
  生命      命中
    \   /
     \ /
   回避-防御-暴击
```

#### Re1999 (重返未来 1999)

```
维度：6 个
  1. 攻击 (ATK)
  2. 防御 (DEF)
  3. 治疗 (Healing)
  4. 控制 (Control)
  5. 生存 (Survival)
  6. 效果抵抗 (Effectiveness)

数值范围：0-100
颜色：紫色（#9932CC） + 半透明填充
背景网格：浅灰色
```

#### 空之轨迹 (Trails - Polemica)

```
复用 Re1999 样式，可选更改颜色为蓝色（#0066CC）
```

### 2.3 实现代码

```typescript
// src/components/RadarChart/RadarChart.tsx

import { Radar, RadarChart, PolarGrid, PolarAngleAxis, PolarRadiusAxis, ResponsiveContainer } from 'recharts';

interface RadarChartProps {
  gameId: 'p5x' | 're1999' | 'trails-polemica';
  data: Array<{ name: string; value: number }>;
  size?: number;
}

export const CharacterRadarChart: React.FC<RadarChartProps> = ({ 
  gameId, 
  data, 
  size = 200 
}) => {
  const colorMap = {
    'p5x': '#8B0000',
    're1999': '#9932CC',
    'trails-polemica': '#0066CC',
  };
  
  return (
    <ResponsiveContainer width={size} height={size}>
      <RadarChart data={data}>
        <PolarGrid stroke="#CCCCCC" strokeDasharray="3 3" />
        <PolarAngleAxis dataKey="name" fontSize={12} />
        <PolarRadiusAxis angle={90} domain={[0, 100]} />
        <Radar 
          name="属性" 
          dataKey="value" 
          stroke={colorMap[gameId]}
          fill={colorMap[gameId]}
          fillOpacity={0.6}
        />
      </RadarChart>
    </ResponsiveContainer>
  );
};
```

---

## 3. 中文字体与多语言

### 3.1 字体选择策略

**目标：** 确保中文显示清晰，支持繁体字，加载速度快。

**推荐方案：** Web Font (Google Fonts + 自托管)

#### 标题字体 (Title)

**选择：** 思源黑体 (Source Han Sans)

- 简体中文支持完整
- 繁体字支持
- 提供 7 种字重（400-700）
- 文件大小：~2.5MB（可分解为多个文件）

```css
@font-face {
  font-family: 'SourceHanSans';
  src: url('/fonts/SourceHanSans-Bold.woff2') format('woff2');
  font-weight: 700;
}

.card-title {
  font-family: 'SourceHanSans', 'Microsoft YaHei', sans-serif;
  font-weight: 700;
  font-size: 32px;
  line-height: 1.2;
  letter-spacing: 0.5px;
}
```

#### 正文字体 (Body)

**选择：** 微软雅黑 (Microsoft YaHei) 系统字体 + 思源宋体备选

```css
@font-face {
  font-family: 'SourceHanSerif';
  src: url('/fonts/SourceHanSerif-Regular.woff2') format('woff2');
  font-weight: 400;
}

.card-body {
  font-family: 'Microsoft YaHei', 'SourceHanSerif', sans-serif;
  font-size: 16px;
  line-height: 1.5;
  color: #333;
}
```

### 3.2 多语言支持

**当前阶段：** 仅支持中文

**未来支持预案：**
- 英文：使用系统字体（Arial, Helvetica）
- 日文：使用 Noto Sans JP
- 韩文：使用 Noto Sans KR

```typescript
// src/config/fonts.ts

export const getFontFamily = (language: 'zh' | 'en' | 'ja' | 'ko') => {
  const fontMap = {
    'zh': "'SourceHanSans', 'Microsoft YaHei', sans-serif",
    'en': "'Inter', 'Helvetica', sans-serif",
    'ja': "'Noto Sans JP', sans-serif",
    'ko': "'Noto Sans KR', sans-serif",
  };
  return fontMap[language];
};
```

### 3.3 字体加载优化

**问题：** 中文字体文件过大，影响首屏加载

**解案：**

1. **子集化 (Subsetting)**
   - 仅加载项目中用到的字符
   - 使用工具（如 FontForge）剔除无用字符
   - 减少文件大小至 500KB 以内

2. **分阶段加载**
   ```css
   @font-face {
     font-family: 'SourceHanSans';
     src: url('/fonts/SourceHanSans-Bold.woff2') format('woff2');
     font-weight: 700;
     font-display: swap; /* 先显示备选字体，字体加载完后替换 */
   }
   ```

3. **动态加载**
   - 只在需要时加载特定游戏的字体

---

## 4. 色彩系统

### 4.1 游戏主题色

#### P5X (Persona 5: The Phantom X)

```
Primary:      #D7000F (深红，P5 标志色)
Accent:       #FFD700 (金色，强调色)
Background:   #1A1A1A (深灰)
Text Primary: #FFFFFF
Text Secondary: #CCCCCC

Tier Colors:
  T0:    #D7000F (红)
  T0.5:  #FF6B35 (橙红)
  T1:    #FFA500 (橙)
  T2:    #FFD700 (金)
  T3:    #90EE90 (绿)
  T4:    #87CEEB (蓝)
```

#### Re1999 (重返未来 1999)

```
Primary:      #9932CC (紫色)
Accent:       #FF1493 (深粉红)
Background:   #0A0A0A (纯黑)
Text Primary: #F0F0F0
Text Secondary: #A0A0A0

Tier Colors:
  T0:    #9932CC (紫)
  T0.5:  #BA55D3 (浅紫)
  T1:    #DA70D6 (深粉)
  T2:    #FF69B4 (热粉)
  T3:    #32CD32 (绿)
  T4:    #00CED1 (青)
```

#### 空之轨迹 (Trails - Polemica)

```
复用 Re1999 的色系，改 Primary 为蓝色 #0066CC
```

### 4.2 CSS 变量定义

```css
/* src/styles/colors.css */

:root[data-game="p5x"] {
  --color-primary: #D7000F;
  --color-accent: #FFD700;
  --color-background: #1A1A1A;
  --color-text-primary: #FFFFFF;
  --color-text-secondary: #CCCCCC;
  
  --tier-t0: #D7000F;
  --tier-t0-5: #FF6B35;
  --tier-t1: #FFA500;
  --tier-t2: #FFD700;
  --tier-t3: #90EE90;
  --tier-t4: #87CEEB;
}

:root[data-game="re1999"] {
  --color-primary: #9932CC;
  --color-accent: #FF1493;
  --color-background: #0A0A0A;
  --color-text-primary: #F0F0F0;
  --color-text-secondary: #A0A0A0;
  
  --tier-t0: #9932CC;
  --tier-t0-5: #BA55D3;
  --tier-t1: #DA70D6;
  --tier-t2: #FF69B4;
  --tier-t3: #32CD32;
  --tier-t4: #00CED1;
}
```

---

## 5. 游戏特定 Cartridge 设计

### 5.1 P5X Cartridge

**特殊设计：**
- 雷达图维度：攻击、命中、暴击、防御、回避、生命
- 特殊卡片样式：P3 风格的几何边框
- 特殊组件：影卡标记（如果有 Persona 角色）

**配置文件：** `src/config/games/p5x.ts`

```typescript
export const P5XConfig: GameConfig = {
  id: 'p5x',
  name: 'Persona 5: The Phantom X',
  displayName: 'P5X',
  
  theme: {
    primary: '#D7000F',
    accent: '#FFD700',
    // ...
  },
  
  characterSchema: [
    {
      key: 'position',
      label: '定位',
      type: 'select',
      options: [
        { value: 'attacker', label: '输出' },
        { value: 'defender', label: '防守' },
        { value: 'healer', label: '治疗' },
        { value: 'support', label: '辅助' },
      ],
      displayPriority: 1,
      showInTierlist: true,
    },
    // ... 其他字段
  ],
  
  features: {
    hasRadarChart: true,
    radarDimensions: [
      { label: '攻击', key: 'attack' },
      { label: '命中', key: 'hit' },
      { label: '暴击', key: 'crit' },
      { label: '防御', key: 'defense' },
      { label: '回避', key: 'evasion' },
      { label: '生命', key: 'hp' },
    ],
  },
};
```

### 5.2 Re1999 Cartridge

**特殊设计：**
- 雷达图维度：攻击、防御、治疗、控制、生存、效果抵抗
- 特殊卡片样式：赛博朋克风格的玻璃卡
- 特殊组件：唱片效果（如果有概念设定）

**配置文件：** `src/config/games/re1999.ts`

### 5.3 空之轨迹 Cartridge

**特殊设计：**
- 复用 Re1999 的大部分样式（减少开发成本）
- 改色系为蓝色
- 简化某些特殊组件

---

## 待确认项

- [ ] 字体是否需要本地打包还是使用 CDN？
- [ ] 是否需要暗黑模式支持？
- [ ] 卡片 3D 旋转角度是否需要调整？
- [ ] 雷达图是否有额外的交互需求（如 hover 展示数值）？

**请反馈调整意见。** 💬
