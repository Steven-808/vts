# 📊 VTS 数据契约 - TypeScript Schema

**Document ID:** 02_DATA_CONTRACTS.md  
**Status:** Draft for Review  
**Format:** TypeScript Interfaces + Zod Schemas  
**Last Updated:** 2025-12-04

---

## 目录

1. [核心数据结构](#核心数据结构)
2. [游戏配置 (GameConfig / Cartridge)](#游戏配置-gameconfigcartridge)
3. [角色数据 (Character)](#角色数据-character)
4. [排行榜状态 (TierlistState)](#排行榜状态-tierliststate)
5. [UI 模式与画布 (Canvas & Layout)](#ui-模式与画布-canvas--layout)
6. [导出配置 (Export)](#导出配置-export)
7. [验证规则 (Zod Schema)](#验证规则-zod-schema)

---

## 核心数据结构

### 全局应用状态

```typescript
// src/types/app.ts

/**
 * 全局应用状态
 * 由 Zustand store 管理
 */
export interface AppState {
  // 当前选中的游戏
  currentGameId: 'p5x' | 're1999' | 'trails-polemica';
  
  // UI 模式
  appMode: 'editor' | 'presentation';
  
  // 排行榜数据
  tierlist: TierlistState;
  
  // 当前展开的角色详情（null 表示关闭）
  selectedCharacterId: string | null;
  selectedCharacterDetailAnimation: AnimationState;
  
  // 画布配置（用于封面生成、HUD 等）
  canvasConfig: CanvasLayoutConfig;
  
  // 导出历史
  exportHistory: ExportRecord[];
}

/**
 * 动画状态机
 */
export type AnimationState = 'idle' | 'opening' | 'open' | 'closing';

/**
 * 导出记录
 */
export interface ExportRecord {
  id: string;
  type: 'cover' | 'oneshot' | 'hud' | 'banner';
  gameId: string;
  timestamp: number;
  fileSize?: number;
  filename: string;
}
```

---

## 游戏配置 (GameConfig/Cartridge)

### 游戏配置接口

```typescript
// src/types/gameConfig.ts

/**
 * 游戏配置
 * 每个游戏对应一个 cartridge 文件
 * src/config/games/{gameId}.ts
 */
export interface GameConfig {
  // === 基本信息 ===
  id: 'p5x' | 're1999' | 'trails-polemica';
  name: string;
  displayName: string;
  
  // === 主题色系 ===
  theme: {
    primary: string;      // 主色 (hex)
    accent: string;       // 辅色
    background: string;   // 背景色
    textPrimary: string;  // 主文字色
    textSecondary: string;// 辅文字色
    tierColors: Record<TierLevel, string>; // 各 Tier 背景色
  };
  
  // === 字体配置 ===
  fonts: {
    title: string;        // e.g., "FZZhengHeiS, serif"
    body: string;         // e.g., "Microsoft YaHei, sans-serif"
    mono: string;         // e.g., "Courier New, monospace"
  };
  
  // === 属性字段映射 ===
  characterSchema: CharacterFieldDefinition[];
  
  // === Tier 定义 ===
  tiers: TierLevel[];
  
  // === 资源路径 ===
  assetPaths: {
    backgrounds: string;  // /public/assets/{gameId}/bg/
    characters: string;   // /public/assets/{gameId}/characters/
    icons: string;        // /public/assets/{gameId}/icons/
  };
  
  // === 特殊配置 ===
  features: {
    hasRadarChart: boolean;
    radarDimensions?: Array<{
      label: string;
      key: string;
    }>; // 6个维度
    
    hasCommandSeat?: boolean; // 命座/意识
    weaponSystem: 'single' | 'multiple'; // 武器配置
    
    specialUIComponents?: string[]; // 特殊组件列表
  };
}

/**
 * 角色字段定义
 * 定义每个游戏可能有的数据字段
 */
export interface CharacterFieldDefinition {
  key: string;           // e.g., "position", "element"
  label: string;         // 中文标签
  type: FieldType;
  
  // 对于 'select' 类型
  options?: Array<{
    value: string;
    label: string;
    color?: string;      // 用于可视化
    icon?: string;       // icon URL
  }>;
  
  // 对于 'radar' 类型
  radarDimensions?: Array<{
    name: string;
    min: number;
    max: number;
  }>;
  
  // 显示优先级（在详情卡中的顺序）
  displayPriority: number;
  
  // 是否在 Tierlist 中显示（如标签）
  showInTierlist: boolean;
}

export type FieldType = 
  | 'text' 
  | 'number' 
  | 'select' 
  | 'boolean' 
  | 'array' 
  | 'radar' 
  | 'rating';

export type TierLevel = 'T0' | 'T0.5' | 'T1' | 'T1.5' | 'T2' | 'T3' | 'T4';
```

---

## 角色数据 (Character)

### 角色数据接口

```typescript
// src/types/character.ts

/**
 * 角色基础数据
 * 存储在游戏特定的数据文件中
 */
export interface Character {
  // === 基本信息 ===
  id: string;           // e.g., "p5x_joker", "re1999_sorollet"
  gameId: string;       // 所属游戏
  name: string;         // 角色名
  rarity: number;       // 稀有度 (1-5)
  
  // === 视觉资源 ===
  artwork: string;      // 立绘 URL
  icon: string;         // 头像 URL (120x120)
  thumbnail: string;    // 缩略图 URL (用于列表)
  
  // === 排行榜信息 ===
  currentTier: TierLevel;
  tierRank: number;     // 同 Tier 内的排序（1, 2, 3...）
  
  // === 动态字段 ===
  // 根据 GameConfig.characterSchema 动态填充
  metadata: Record<string, any>;
  
  // 示例结构（仅供参考）：
  // metadata: {
  //   position: "输出",
  //   element: "火",
  //   strengthRating: "S+",
  //   strengthScore: 4.8,
  //   recommendedTeammates: ["char1_id", "char2_id"],
  //   recommendedWeapon: { name: "XXX", refinement: 5 },
  //   ascensions: [...],
  //   skillPriority: [...],
  //   radarStats: { ... }
  // }
  
  // === 时间戳 ===
  createdAt: number;
  updatedAt: number;
}

/**
 * 角色详情卡数据
 * Character 的扩展，包含完整的展示信息
 */
export interface CharacterDetail extends Character {
  // 扩展字段
  description?: string;
  tips?: string[];
  buildGuides?: Array<{
    title: string;
    content: string;
  }>;
}

/**
 * 角色数据集合
 */
export interface CharacterDatabase {
  gameId: string;
  version: string;
  characters: Character[];
  lastUpdated: number;
}
```

---

## 排行榜状态 (TierlistState)

### 排行榜状态接口

```typescript
// src/types/tierlist.ts

/**
 * 排行榜完整状态
 */
export interface TierlistState {
  gameId: string;
  
  // 所有角色的排序
  // 按 Tier 和 rank 组织
  // structure: Map<TierLevel, Character[]>
  tiers: Record<TierLevel, string[]>; // 存储 character IDs
  
  // 是否有未保存的变更
  isDirty: boolean;
  
  // 最后一次保存的时间戳
  lastSavedAt: number;
  
  // 版本（用于导入/导出冲突解决）
  version: number;
  
  // 用户自定义 Tier 名称（可选）
  tierLabels?: Record<string, string>;
  
  // 筛选条件（未来功能）
  filters?: TierlistFilter;
}

/**
 * 排行榜筛选条件
 */
export interface TierlistFilter {
  searchQuery?: string;
  elements?: string[];        // 属性过滤
  positions?: string[];       // 定位过滤
  rarities?: number[];        // 稀有度过滤
  hideLowTiers?: boolean;     // 隐藏低 Tier
}

/**
 * 排行榜变更事件
 */
export interface TierlistChangeEvent {
  type: 'move' | 'add' | 'remove' | 'reorder';
  characterId: string;
  fromTier: TierLevel;
  toTier: TierLevel;
  fromRank: number;
  toRank: number;
  timestamp: number;
  userId?: string; // 未来多人编辑
}

/**
 * 排行榜历史（可选，用于撤销/重做）
 */
export interface TierlistHistory {
  present: TierlistState;
  past: TierlistState[];
  future: TierlistState[];
}
```

---

## UI 模式与画布 (Canvas & Layout)

### 画布配置接口

```typescript
// src/types/canvas.ts

/**
 * 画布布局配置
 * 用于封面生成、HUD、一图流等
 */
export interface CanvasLayoutConfig {
  type: 'cover' | 'oneshot' | 'hud' | 'banner';
  
  // === 尺寸 ===
  width: number;
  height: number;
  dpi: number; // 72, 96, 300 等
  
  // === 背景 ===
  background: CanvasBackground;
  
  // === 内容区域 ===
  sections: CanvasSection[];
  
  // === 文字样式 ===
  typography: {
    title: TypographyStyle;
    subtitle: TypographyStyle;
    body: TypographyStyle;
  };
  
  // === 配置版本 ===
  template?: string; // e.g., "p5x_cover_v1"
  metadata?: Record<string, any>;
}

/**
 * 画布背景配置
 */
export interface CanvasBackground {
  type: 'solid' | 'gradient' | 'image';
  
  // solid 类型
  color?: string;
  
  // gradient 类型
  gradientStart?: string;
  gradientEnd?: string;
  gradientAngle?: number; // 0-360
  
  // image 类型
  imageUrl?: string;
  imagePosition?: 'cover' | 'contain' | 'stretch';
}

/**
 * 画布内容区域
 */
export interface CanvasSection {
  id: string;
  type: 'image' | 'text' | 'shape' | 'grid';
  
  // === 位置与尺寸 ===
  x: number;
  y: number;
  width: number;
  height: number;
  
  // === 内容 ===
  content?: {
    // image
    imageUrl?: string;
    
    // text
    text?: string;
    fontSize?: number;
    fontColor?: string;
    fontFamily?: string;
    fontWeight?: 'normal' | 'bold' | 'lighter';
    alignment?: 'left' | 'center' | 'right';
    
    // shape
    shapeType?: 'rect' | 'circle' | 'rounded-rect';
    fillColor?: string;
    strokeColor?: string;
    strokeWidth?: number;
  };
  
  // === 效果 ===
  opacity?: number;
  rotation?: number;
  shadow?: {
    offsetX: number;
    offsetY: number;
    blur: number;
    color: string;
  };
  
  // === 动画（可选） ===
  animation?: {
    type: 'fade' | 'slide' | 'scale';
    duration: number;
    delay: number;
  };
}

/**
 * 文字样式
 */
export interface TypographyStyle {
  fontSize: number;
  fontFamily: string;
  fontWeight: number; // 400, 500, 600, 700
  lineHeight: number;
  letterSpacing: number;
  color: string;
  textShadow?: {
    offsetX: number;
    offsetY: number;
    blur: number;
    color: string;
  };
}

/**
 * 画布预设模板
 */
export interface CanvasTemplate {
  id: string;
  name: string;
  gameId: string;
  type: 'cover' | 'oneshot' | 'hud' | 'banner';
  config: CanvasLayoutConfig;
  thumbnail?: string;
  createdAt: number;
}
```

---

## 导出配置 (Export)

### 导出接口

```typescript
// src/types/export.ts

/**
 * 导出任务
 */
export interface ExportJob {
  id: string;
  type: ExportType;
  gameId: string;
  
  // === 源数据 ===
  sourceCharacterId?: string;    // 用于封面
  sourceCharacterIds?: string[]; // 用于一图流
  sourceTierlist?: TierlistState; // 用于社交一图流
  
  // === 导出配置 ===
  config: ExportConfig;
  
  // === 状态 ===
  status: 'pending' | 'processing' | 'success' | 'error';
  progress: number; // 0-100
  error?: string;
  
  // === 输出 ===
  output?: ExportOutput;
  
  // === 时间 ===
  createdAt: number;
  completedAt?: number;
}

export type ExportType = 'cover' | 'oneshot' | 'hud' | 'banner' | 'video';

/**
 * 导出配置
 */
export interface ExportConfig {
  format: 'png' | 'jpg' | 'webp' | 'mp4' | 'webm';
  width: number;
  height: number;
  dpi: number;
  quality: 'low' | 'medium' | 'high';
  
  // 特殊选项
  options?: {
    // 是否包含水印
    watermark?: boolean;
    watermarkText?: string;
    
    // 是否压缩
    compress?: boolean;
    
    // 自定义命名
    filename?: string;
  };
}

/**
 * 导出输出
 */
export interface ExportOutput {
  format: string;
  blob: Blob;
  url: string; // Object URL
  size: number; // bytes
  createdAt: number;
}

/**
 * 导出历史
 */
export interface ExportHistory {
  records: ExportJob[];
  lastExportTime?: number;
}
```

---

## 验证规则 (Zod Schema)

### Zod 验证 Schema

```typescript
// src/types/validation.ts

import { z } from 'zod';

// === 基础 Schema ===

export const TierLevelSchema = z.enum([
  'T0', 'T0.5', 'T1', 'T1.5', 'T2', 'T3', 'T4'
]);

export const GameIdSchema = z.enum([
  'p5x', 're1999', 'trails-polemica'
]);

export const AppModeSchema = z.enum(['editor', 'presentation']);

// === 颜色 Schema ===

export const HexColorSchema = z.string().regex(/^#([A-Fa-f0-9]{6}|[A-Fa-f0-9]{3})$/);

// === 角色 Schema ===

export const CharacterSchema = z.object({
  id: z.string(),
  gameId: GameIdSchema,
  name: z.string(),
  rarity: z.number().min(1).max(5),
  artwork: z.string().url().optional(),
  icon: z.string().url(),
  thumbnail: z.string().url().optional(),
  currentTier: TierLevelSchema,
  tierRank: z.number().min(1),
  metadata: z.record(z.any()),
  createdAt: z.number(),
  updatedAt: z.number(),
});

export type Character = z.infer<typeof CharacterSchema>;

// === 排行榜 Schema ===

export const TierlistStateSchema = z.object({
  gameId: GameIdSchema,
  tiers: z.record(z.array(z.string())),
  isDirty: z.boolean(),
  lastSavedAt: z.number(),
  version: z.number(),
  tierLabels: z.record(z.string()).optional(),
  filters: z.any().optional(),
});

export type TierlistState = z.infer<typeof TierlistStateSchema>;

// === 画布 Schema ===

export const CanvasLayoutConfigSchema = z.object({
  type: z.enum(['cover', 'oneshot', 'hud', 'banner']),
  width: z.number().positive(),
  height: z.number().positive(),
  dpi: z.number().positive(),
  background: z.any(),
  sections: z.array(z.any()),
  typography: z.any(),
  template: z.string().optional(),
  metadata: z.record(z.any()).optional(),
});

export type CanvasLayoutConfig = z.infer<typeof CanvasLayoutConfigSchema>;

// === 导出 Schema ===

export const ExportConfigSchema = z.object({
  format: z.enum(['png', 'jpg', 'webp', 'mp4', 'webm']),
  width: z.number().positive(),
  height: z.number().positive(),
  dpi: z.number().positive(),
  quality: z.enum(['low', 'medium', 'high']),
  options: z.object({
    watermark: z.boolean().optional(),
    watermarkText: z.string().optional(),
    compress: z.boolean().optional(),
    filename: z.string().optional(),
  }).optional(),
});

export type ExportConfig = z.infer<typeof ExportConfigSchema>;

// === 应用状态 Schema ===

export const AppStateSchema = z.object({
  currentGameId: GameIdSchema,
  appMode: AppModeSchema,
  tierlist: TierlistStateSchema,
  selectedCharacterId: z.string().nullable(),
  selectedCharacterDetailAnimation: z.enum(['idle', 'opening', 'open', 'closing']),
  canvasConfig: CanvasLayoutConfigSchema,
  exportHistory: z.array(z.any()),
});

export type AppState = z.infer<typeof AppStateSchema>;
```

---

## 待确认项

- [ ] 是否需要支持"自定义 Tier"？（当前固定 T0-T4）
- [ ] 角色元数据中是否需要更多字段？（如 "获取难度"、"练度成本" 等）
- [ ] 导出文件是否需要嵌入 metadata（如创建日期、游戏版本）？
- [ ] 是否需要"配置快照"功能（保存多个排行榜版本）？

**请反馈调整意见。** 💬
