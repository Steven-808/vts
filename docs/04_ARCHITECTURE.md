# 🏗️ VTS 架构指南 & 开发工作流

**Document ID:** 04_ARCHITECTURE.md  
**Status:** Draft for Review  
**Last Updated:** 2025-12-04

---

## 目录

1. [项目文件结构](#1-项目文件结构)
2. [React 组件树](#2-react-组件树)
3. [状态管理 (Zustand)](#3-状态管理-zustand)
4. [数据流与通信](#4-数据流与通信)
5. [Antigravity + Cursor 工作流](#5-antigravity--cursor-工作流)
6. [开发检查清单](#6-开发检查清单)

---

## 1. 项目文件结构

```
vts-project/
├── public/
│   ├── assets/
│   │   ├── p5x/
│   │   │   ├── characters/          # 角色立绘 (JPG/PNG)
│   │   │   │   ├── joker.png
│   │   │   │   ├── arsene.png
│   │   │   │   └── ...
│   │   │   ├── icons/               # 小图标
│   │   │   │   ├── element_fire.svg
│   │   │   │   └── ...
│   │   │   └── bg/                  # 背景素材
│   │   │
│   │   ├── re1999/
│   │   │   ├── characters/
│   │   │   ├── icons/
│   │   │   └── bg/
│   │   │
│   │   └── trails-polemica/
│   │       └── ...
│   │
│   └── fonts/                       # Web 字体
│       ├── SourceHanSans-Bold.woff2
│       └── ...
│
├── src/
│   │
│   ├── components/
│   │   ├── atomic/                  # 原子级组件 (通用)
│   │   │   ├── Button.tsx
│   │   │   ├── Card.tsx
│   │   │   ├── Modal.tsx
│   │   │   ├── Input.tsx
│   │   │   └── ...
│   │   │
│   │   ├── canvas/                  # R3F 3D 组件
│   │   │   ├── Scene.tsx
│   │   │   ├── CardLighting.tsx
│   │   │   ├── DistortionEffect.tsx
│   │   │   └── ...
│   │   │
│   │   ├── tierlist/                # 排行榜模块
│   │   │   ├── TierlistContainer.tsx
│   │   │   ├── TierRow.tsx
│   │   │   ├── CharacterCard.tsx
│   │   │   └── TierlistToolbar.tsx
│   │   │
│   │   ├── characterDetail/         # 角色详情卡
│   │   │   ├── DetailCard.tsx
│   │   │   ├── DetailCardAnimation.tsx
│   │   │   ├── RadarChart.tsx
│   │   │   └── SkillPriority.tsx
│   │   │
│   │   ├── coverGenerator/          # 封面生成模块
│   │   │   ├── CoverEditor.tsx
│   │   │   ├── CoverPreview.tsx
│   │   │   └── CoverExport.tsx
│   │   │
│   │   ├── dynamic/                 # 游戏特定组件
│   │   │   ├── p5x/
│   │   │   │   ├── P5XRadarChart.tsx
│   │   │   │   └── P5XDetailCard.tsx
│   │   │   │
│   │   │   ├── re1999/
│   │   │   │   └── ...
│   │   │   │
│   │   │   └── trails-polemica/
│   │   │       └── ...
│   │   │
│   │   └── layout/                  # 布局组件
│   │       ├── MainLayout.tsx
│   │       ├── Sidebar.tsx
│   │       └── Header.tsx
│   │
│   ├── canvas/                      # R3F 集成点
│   │   ├── index.tsx
│   │   └── hooks/
│   │       ├── useCardAnimation.ts
│   │       └── ...
│   │
│   ├── stores/                      # Zustand 全局状态
│   │   ├── appStore.ts              # 主应用状态
│   │   ├── tierlistStore.ts         # 排行榜状态
│   │   ├── gameStore.ts             # 游戏配置状态
│   │   └── exportStore.ts           # 导出历史
│   │
│   ├── lib/
│   │   ├── logic/                   # 核心业务逻辑
│   │   │   ├── tierlistUtils.ts     # 排序、重排逻辑
│   │   │   ├── exportUtils.ts       # 导出函数
│   │   │   ├── characterUtils.ts    # 角色数据处理
│   │   │   └── ...
│   │   │
│   │   └── hooks/                   # React Hooks
│   │       ├── useCharacterData.ts
│   │       ├── useTierlistDrag.ts
│   │       ├── useExport.ts
│   │       └── ...
│   │
│   ├── types/                       # TypeScript 类型定义
│   │   ├── index.ts
│   │   ├── app.ts
│   │   ├── character.ts
│   │   ├── tierlist.ts
│   │   ├── gameConfig.ts
│   │   ├── canvas.ts
│   │   ├── export.ts
│   │   ├── validation.ts            # Zod Schema
│   │   └── ...
│   │
│   ├── config/                      # 配置文件
│   │   ├── games/
│   │   │   ├── p5x.ts               # P5X Cartridge
│   │   │   ├── re1999.ts            # Re1999 Cartridge
│   │   │   ├── trails-polemica.ts   # 空之轨迹 Cartridge
│   │   │   └── index.ts             # 导出所有配置
│   │   │
│   │   ├── constants.ts             # 全局常量
│   │   └── routes.ts                # 路由配置
│   │
│   ├── styles/                      # 全局样式
│   │   ├── globals.css
│   │   ├── colors.css
│   │   ├── fonts.css
│   │   └── animations.css
│   │
│   ├── utils/                       # 工具函数
│   │   ├── formatters.ts
│   │   ├── validators.ts
│   │   └── ...
│   │
│   ├── App.tsx                      # 主应用入口
│   └── main.tsx                     # Vite 入口
│
├── docs/                            # 项目文档 (你看的就是这些)
│   ├── 00_PROJECT_OVERVIEW.md
│   ├── 01_FEATURES_DETAILED.md
│   ├── 02_DATA_CONTRACTS.md
│   ├── 03_VISUAL_DESIGN.md
│   └── 04_ARCHITECTURE.md
│
├── vite.config.ts
├── tsconfig.json
├── package.json
└── README.md
```

---

## 2. React 组件树

```
<App>
├── <MainLayout>
│   ├── <Header>
│   │   ├── <GameSelector />
│   │   ├── <ModeToggle />    (Editor / Presentation)
│   │   └── <ExportMenu />
│   │
│   ├── <Sidebar>
│   │   ├── <QuickActions />
│   │   └── <SettingsPanel />
│   │
│   └── <MainContent>
│       │
│       ├── [Tierlist Mode]
│       │   ├── <TierlistContainer>
│       │   │   ├── <TierRow key="T0">
│       │   │   │   ├── <CharacterCard key="char1" />
│       │   │   │   └── <CharacterCard key="char2" />
│       │   │   ├── <TierRow key="T0.5">
│       │   │   └── ...
│       │   │
│       │   └── <CharacterDetailModal>     [条件渲染]
│       │       ├── <DetailCardAnimation />
│       │       └── <DetailCard>
│       │           ├── <RadarChart />
│       │           ├── <SkillPriorityList />
│       │           └── <TeamRecommendation />
│       │
│       ├── [Cover Generator Mode]        [条件渲染]
│       │   ├── <CoverPreview />
│       │   └── <CoverEditor>
│       │       ├── <TitleInput />
│       │       ├── <SubtitleInput />
│       │       ├── <ColorPicker />
│       │       └── <ExportButton />
│       │
│       └── [Other Modes...]
│
└── <Canvas>                     (R3F 场景，全局铺底)
    ├── <Scene>
    ├── <Lighting>
    ├── <Effects>
    └── <InteractiveModel>       (未来版本 3D 角色)
```

---

## 3. 状态管理 (Zustand)

### 3.1 Zustand Store 结构

```typescript
// src/stores/appStore.ts

import { create } from 'zustand';
import type { AppState } from '@/types';

export const useAppStore = create<AppState & AppActions>((set) => ({
  // 状态字段
  currentGameId: 'p5x',
  appMode: 'editor',
  tierlist: { /* ... */ },
  selectedCharacterId: null,
  selectedCharacterDetailAnimation: 'idle',
  canvasConfig: { /* ... */ },
  exportHistory: [],
  
  // 操作方法
  setGameId: (gameId) => set({ currentGameId: gameId }),
  setAppMode: (mode) => set({ appMode: mode }),
  selectCharacter: (id) => set({ 
    selectedCharacterId: id,
    selectedCharacterDetailAnimation: 'opening'
  }),
  closeCharacterDetail: () => set({
    selectedCharacterId: null,
    selectedCharacterDetailAnimation: 'closing'
  }),
  updateTierlist: (tierlist) => set({ tierlist }),
  recordExport: (record) => set((state) => ({
    exportHistory: [...state.exportHistory, record]
  })),
}));
```

### 3.2 分层存储设计

```
AppStore (全局应用状态)
├── currentGameId
├── appMode
└── selectedCharacterId
    │
    ├─ TierlistStore (排行榜子状态)
    │  ├── tiers
    │  ├── filters
    │  └── isDirty
    │
    ├─ GameStore (游戏配置)
    │  ├── config (当前游戏配置)
    │  └── characterDatabase
    │
    └─ ExportStore (导出历史)
       ├── exportHistory
       └── lastExportTime
```

**为什么分层？**
- 每个 Store 专注一个职责
- 组件可选择订阅不同的 Store，避免不必要的重新渲染
- 便于测试和维护

### 3.3 跨 Store 通信

```typescript
// 不直接导入和使用多个 store，而是在需要的地方组合

import { useAppStore } from '@/stores/appStore';
import { useGameStore } from '@/stores/gameStore';

export const SomeComponent = () => {
  const gameId = useAppStore((state) => state.currentGameId);
  const gameConfig = useGameStore((state) => 
    state.getConfigByGameId(gameId)
  );
  
  // 使用 gameConfig...
};
```

---

## 4. 数据流与通信

### 4.1 用户操作数据流

```
用户点击头像
    ↓
TierlistContainer.handleCharacterClick(id)
    ↓
useAppStore.selectCharacter(id)
    ↓
Zustand 更新状态
    ↓
DetailCardModal 订阅到状态变化
    ↓
DetailCardAnimation 触发
    ↓
动画完成后，DetailCard 开始渲染
```

### 4.2 导出流程数据流

```
用户点击"导出"
    ↓
CoverExport.handleExport()
    ↓
获取当前排行榜状态（从 TierlistStore）
    ↓
生成 Canvas + 渲染内容
    ↓
Canvas → Blob (PNG)
    ↓
创建下载链接
    ↓
浏览器触发下载
    ↓
记录导出记录（ExportStore.recordExport）
```

### 4.3 游戏切换流程

```
用户选择新游戏
    ↓
GameSelector.handleGameChange(newGameId)
    ↓
useAppStore.setGameId(newGameId)
    ↓
GameStore 加载新游戏配置 + 角色数据库
    ↓
TierlistStore 更新 (或加载保存的排序)
    ↓
UI 自动重新渲染
```

---

## 5. Antigravity + Cursor 工作流

### 5.1 开发启动步骤

**第 1 步：项目初始化**

```bash
# 由 Human (你) 使用 Cursor 执行
pnpm create vite@latest vts --template react-ts
cd vts
pnpm install

# 安装依赖
pnpm add zustand @react-three/fiber three @react-three/drei framer-motion
pnpm add recharts
pnpm add zod
pnpm add html-to-image
pnpm add -D tailwindcss postcss autoprefixer
```

**第 2 步：项目结构初始化**

由 Cursor (或 Claude) 在 Cursor 中创建上述文件树结构

**第 3 步：Type 定义**

```
你：在 Antigravity 中启动 "Planning" 模式
   ↓
Antigravity 读取 02_DATA_CONTRACTS.md
   ↓
Claude 生成 src/types/ 下的所有 TypeScript 文件
   ↓
你在 Cursor 中审阅并合并代码
```

### 5.2 功能开发流程（以 Tierlist 为例）

**循环流程：**

```
┌─────────────────────────────────────┐
│ Step 1: 需求澄清                    │
│ 你: "实现 Tierlist 拖拽排序"        │
└────────────────┬────────────────────┘
                 ↓
┌─────────────────────────────────────┐
│ Step 2: Antigravity Planning        │
│ 你：上传本 Spec 文档                │
│ Antigravity 理解 01 功能需求        │
└────────────────┬────────────────────┘
                 ↓
┌─────────────────────────────────────┐
│ Step 3: Claude 逻辑部分             │
│ 提示词：                             │
│ "根据 02_DATA_CONTRACTS.md          │
│  实现 tierlistUtils.ts              │
│  包括: reorderCharacters, moveTier" │
└────────────────┬────────────────────┘
                 ↓
┌─────────────────────────────────────┐
│ Step 4: Gemini 视觉部分             │
│ 提示词：                             │
│ "根据 01 和 03 Spec，实现           │
│  TierlistContainer.tsx              │
│  支持拖拽、动画、Editor/Presentation"│
└────────────────┬────────────────────┘
                 ↓
┌─────────────────────────────────────┐
│ Step 5: 你在 Cursor 审阅            │
│ 测试功能、提反馈                     │
└────────────────┬────────────────────┘
                 ↓
┌─────────────────────────────────────┐
│ Step 6: 迭代修复                    │
│ 在 Cursor 中进行微调                │
│ ("把动画速度改到 200ms")            │
└────────────────┬────────────────────┘
                 ↓
┌─────────────────────────────────────┐
│ Step 7: 合并 & 下一功能             │
│ 确认无误，提交 Git                  │
└─────────────────────────────────────┘
```

### 5.3 Prompt 模板库

#### 唤醒 Claude (逻辑实现)

```
Context: 阅读这些文档
@docs/02_DATA_CONTRACTS.md

任务：实现排行榜拖拽重排逻辑

需求：
1. 创建 src/lib/logic/tierlistUtils.ts
2. 实现以下函数：
   - reorderWithinTier(characters, fromIndex, toIndex)
   - moveCharacterToTier(characters, characterId, toTier)
   - validateTierListState(state)

规范：
- 使用 TypeScript，strict mode
- 返回新数组，不修改原数组 (immutable)
- 包含完整的 JSDoc 注释
- 添加单元测试骨架

输出：只输出 TypeScript 代码，不要 UI
```

#### 唤醒 Gemini (视觉实现)

```
Context: 阅读这些文档
@docs/01_FEATURES_DETAILED.md
@docs/03_VISUAL_DESIGN.md

任务：实现 Tierlist 排行榜组件

需求：
1. 创建 src/components/tierlist/TierlistContainer.tsx
2. 支持特性：
   - 左侧 Tier 列 (T0-T4)
   - 右侧网格布局
   - 支持拖拽排序 (使用 @dnd-kit)
   - Editor/Presentation 模式切换
   - 点击头像展开详情卡

设计规范：
- 响应式布局 (1920×1080 基准)
- 头像尺寸 120×120px，间距 16px
- 使用 Framer Motion 实现平滑过渡
- 遵循 03_VISUAL_DESIGN.md 的色彩系统

输出：
- React 函数式组件
- 集成 Zustand store
- 不包含实际数据（mock 数据由测试提供）
```

#### 审阅 & 反馈

```
【Cursor 中测试后的反馈】

修复反馈：

1. 拖拽速度有点快，改为 300ms
2. 头像的鼠标悬停效果需要加 (scale: 1.1)
3. Tier 标签的背景色对 P5X 主题偏暗，改为 primary 色的 80% 亮度

改进建议：
- 考虑添加"撤销/重做"功能（可以后续迭代）
- 拖拽动画可以加个微妙的 rotate（-2° ~ +2°）来增加趣味性
```

---

## 6. 开发检查清单

### 6.1 功能完成检查

#### Tierlist 模块

- [ ] **数据层**
  - [ ] Character 接口定义完整
  - [ ] TierlistState 接口定义完整
  - [ ] Zustand store 初始化成功

- [ ] **逻辑层**
  - [ ] tierlistUtils.reorderWithinTier() ✓ 测试通过
  - [ ] tierlistUtils.moveCharacterToTier() ✓ 测试通过
  - [ ] tierlistUtils.validateState() ✓ 测试通过

- [ ] **UI 层**
  - [ ] TierlistContainer 组件渲染正确
  - [ ] 拖拽排序功能正常
  - [ ] Editor/Presentation 模式切换工作
  - [ ] 响应式布局在 1920×1080 下正常

- [ ] **交互层**
  - [ ] 点击头像打开详情卡
  - [ ] 详情卡 3D 动画流畅
  - [ ] 关闭动画无卡顿

- [ ] **性能**
  - [ ] 首屏加载 < 2 秒
  - [ ] 拖拽帧率 ≥ 60 FPS
  - [ ] 无内存泄漏

#### 封面生成模块

- [ ] **功能**
  - [ ] 预设尺寸 1280×720 正确
  - [ ] 中文字体显示清晰
  - [ ] 颜色选择器工作正常
  - [ ] 导出 PNG 成功

- [ ] **质量**
  - [ ] 导出图片无模糊
  - [ ] 文字抗锯齿处理良好
  - [ ] 适配所有 3 个游戏的色彩系统

### 6.2 跨浏览器测试

- [ ] Chrome 最新版
- [ ] Firefox 最新版
- [ ] Safari 最新版 (macOS)
- [ ] Edge 最新版

### 6.3 OBS 集成测试

- [ ] VTS 窗口可被 OBS 捕捉
- [ ] 色键抠图正常（透明度保留）
- [ ] 录屏 1080p 60FPS 无卡顿
- [ ] 导出文件可在 OBS 中引入

### 6.4 数据测试

- [ ] 导入 JSON 角色数据正常
- [ ] 排序状态保存到 LocalStorage
- [ ] 游戏切换时状态正确恢复
- [ ] 导出历史记录正常

### 6.5 文档与代码质量

- [ ] 所有组件添加 JSDoc 注释
- [ ] TypeScript 零错误
- [ ] 代码格式统一 (Prettier)
- [ ] 无 console.log() 遗留代码

---

## 待确认项

- [ ] 是否需要集成 Git 版本管理？
- [ ] 是否需要 E2E 测试（Cypress / Playwright）？
- [ ] 是否需要 Storybook 组件库文档？
- [ ] 是否需要实时协作功能（WebSocket）？

**请反馈调整意见。** 💬

---

## 快速命令参考

```bash
# 启动开发服务器
pnpm dev

# 构建生产版本
pnpm build

# 预览生产版本
pnpm preview

# 运行测试
pnpm test

# 代码格式化
pnpm format

# 类型检查
pnpm type-check
```
