# 📚 VTS 规范文档快速导航

**Generated:** 2025-12-04  
**Status:** Ready for Antigravity Planning  
**Language:** 中文 (中文字体完整支持)

---

## 📋 文档全览

本规范包包含 **5 个核心文档**，总计 20,000+ 字，涵盖需求、设计、架构、工作流的全闭环。

| 文档 | 用途 | 阅读时间 | 关键决策 |
|------|------|--------|---------|
| **00_PROJECT_OVERVIEW.md** | 项目总体愿景、范围、时间规划 | 10 分钟 | ✓ 范围确认、优先级确认 |
| **01_FEATURES_DETAILED.md** | 6 大功能模块的需求细解、交互设计 | 20 分钟 | ✓ 各功能的具体行为定义 |
| **02_DATA_CONTRACTS.md** | TypeScript 类型定义、Zod 验证 Schema | 15 分钟 | ✓ 数据结构最终定稿 |
| **03_VISUAL_DESIGN.md** | 3D 动画、雷达图、字体、色彩规范 | 15 分钟 | ✓ 视觉风格确认 |
| **04_ARCHITECTURE.md** | 项目结构、组件树、状态管理、工作流 | 15 分钟 | ✓ 开发工作流确认 |

**总计阅读时间：** 75 分钟  
**推荐阅读顺序：** 00 → 01 → 02 → 03 → 04

---

## 🎯 快速检查清单

### 对于 YOU (Architect)

在启动 Antigravity Planning 之前，请确认：

- [ ] **范围理解**
  - [ ] 明确 v1.0 必做的 3 个功能（Tierlist + 详情卡 + 封面）
  - [ ] 确认"延后功能"清单（一图流、HUD、卡池、排轴）
  - [ ] 同意"不在范围内"的项（无 TTS、无自动录制、无账户系统）

- [ ] **功能需求**
  - [ ] Tierlist 交互流程（拖拽、点击、Editor/Presentation）
  - [ ] 3D 卡片飞出动画的具体效果（旋转、位置、时间）
  - [ ] 角色详情卡的 8 个必要字段（定位、强度、队友、武器、命座、技能、雷达、等等）

- [ ] **设计确认**
  - [ ] 赞同"平民瀑布 + 少量 3D"的排行榜风格（而非全 3D 艺术图）
  - [ ] 确认中文字体方案（思源黑体 / 微软雅黑）
  - [ ] 确认 3 个游戏的色彩系统

- [ ] **技术可行性**
  - [ ] R3F + Framer Motion 的 3D 动画方案可接受
  - [ ] Zustand 状态管理方案合适
  - [ ] 导出管线（Canvas → PNG）满足需求
  - [ ] OBS 集成无特殊要求（直接截窗口）

- [ ] **交付模式**
  - [ ] 同意 Antigravity Planning → Cursor Coding 的工作流
  - [ ] Claude 负责逻辑层，Gemini 负责视觉层的分工可接受
  - [ ] 时间规划（20-30 天 MVP）合理

---

### 对于 Antigravity (Planning)

使用这份规范的最佳方式：

1. **读取** 00_PROJECT_OVERVIEW.md → 理解项目全景
2. **展开** 01_FEATURES_DETAILED.md 中的"Tierlist 排行榜"部分
3. **引用** 02_DATA_CONTRACTS.md 中的数据结构
4. **遵循** 03_VISUAL_DESIGN.md 的动画规范
5. **应用** 04_ARCHITECTURE.md 的组件树结构

**生成开发计划时的建议：**
- 按"第 1 周：数据层" → "第 2 周：Tierlist UI" → "第 3 周：3D 动画" → "第 4 周：封面工具"的顺序排期
- 明确每个 Sprint 的 Deliverable（例如第 1 周交付 `src/types/` 所有文件 + `src/stores/appStore.ts`）
- 为 Claude 和 Gemini 分别准备 Prompt 模板（见 04_ARCHITECTURE.md § 5.3）

---

### 对于 Claude & Gemini (Coding)

每次收到任务时的检查清单：

**Claude (逻辑)：**
- [ ] 明确输出位置（`src/lib/logic/` / `src/stores/` / `src/types/`）
- [ ] 了解相关的 TypeScript 接口（从 02_DATA_CONTRACTS.md）
- [ ] 检查是否有单元测试要求
- [ ] 确认 Immutability 原则（不修改原数据）

**Gemini (视觉)：**
- [ ] 明确组件位置（`src/components/` 哪个子目录）
- [ ] 了解相关的设计规范（从 03_VISUAL_DESIGN.md）
- [ ] 检查是否有 Tailwind / CSS Module 偏好
- [ ] 确认是否需要 Framer Motion / R3F / 其他动画库

---

## 🔍 关键章节速查

### 如果你想快速了解...

| 想知道什么 | 查看文件 | 章节 |
|----------|--------|------|
| 项目是什么、做什么 | 00 | § 项目使命 |
| 功能有哪些 | 00 | § 项目范围 |
| 支持哪些游戏 | 00 | § 支持的游戏 (Cartridge System) |
| Tierlist 要怎么做 | 01 | § 1. Tierlist 排行榜 + 角色详情卡 |
| 3D 卡片动画细节 | 03 | § 1. 3D 卡片动画 |
| 数据结构是什么 | 02 | § 核心数据结构 |
| 字体怎么配置 | 03 | § 3. 中文字体与多语言 |
| 项目文件怎么组织 | 04 | § 1. 项目文件结构 |
| 状态管理怎么做 | 04 | § 3. 状态管理 (Zustand) |
| 怎么开发和测试 | 04 | § 5 & 6. 工作流与检查清单 |
| 色彩怎么选 | 03 | § 4. 色彩系统 |
| 3 个游戏的配置 | 03 | § 5. 游戏特定 Cartridge 设计 |

---

## 🛠️ 常见问题解答 (FAQ)

### Q1: 为什么选择 Zustand 而不是 Redux?
**A:** Zustand 轻量（~2KB），学习曲线低，对小型项目足够。Redux 过度设计会增加项目复杂度。  
参考：04_ARCHITECTURE.md § 3. 状态管理

### Q2: 3D 卡片动画会不会很卡？
**A:** 我们限制了 3D 旋转角度（只有 15° rotateY），使用 easeOut 曲线加速收尾。大多数浏览器都能 60FPS 渲染。  
参考：03_VISUAL_DESIGN.md § 1.2 动画序列详解

### Q3: 中文字体会不会让项目很大？
**A:** 我们推荐子集化策略，仅加载项目中用到的字符。最终字体包 < 500KB。  
参考：03_VISUAL_DESIGN.md § 3.3 字体加载优化

### Q4: 如何支持多个游戏而不会代码重复？
**A:** 每个游戏一个 Cartridge 配置文件 (`src/config/games/{gameId}.ts`)，定义色彩、字体、字段映射。核心组件通过 GameConfig 动态适配。  
参考：02_DATA_CONTRACTS.md § 游戏配置、04_ARCHITECTURE.md § 1. 项目文件结构

### Q5: 能否后期支持"撤销/重做"功能？
**A:** 可以，架构中预留了 TierlistHistory 接口。目前 v1.0 不做，v1.1+ 可加。  
参考：02_DATA_CONTRACTS.md § 排行榜状态

### Q6: OBS 直播能用这个工具吗？
**A:** 可以，VTS 生成的 HUD 和覆盖层可被 OBS 以"浏览器源"的方式引入。见 04_ARCHITECTURE.md § 5.2 用户工作流。

---

## 📞 反馈与修改流程

### 如果你要修改这份规范

1. **直接在文档中批注** (使用 Google Docs / Notion 的批注功能)
   - 标记位置：例如 "01_FEATURES_DETAILED.md § 1.2 第 3 段"
   - 内容：说明建议改动或问题
   - 优先级：标注 P0 (阻塞) / P1 (重要) / P2 (可选)

2. **列出变更清单** (整合反馈)
   - 集合所有批注，形成"变更清单"
   - 提交给 Human，由 Human 确认是否采纳

3. **更新规范** (文档保持最新)
   - Human 确认后，重新生成对应文档
   - 所有变更都 commit 到 Git

**示例反馈邮件：**
```
主题: VTS Spec 修改反馈 - 优先级 P1

变更位置：
  01_FEATURES_DETAILED.md § 1.3 角色详情卡 § 打开动画规范

现有描述：
  卡片飞出时间：250ms

建议改动：
  改为 350ms，理由是 250ms 太快，用户可能看不清动画

相关文件需要更新：
  - 03_VISUAL_DESIGN.md § 1.2 动画序列（改时间参数）
  - 04_ARCHITECTURE.md 工作流中的 Prompt 模板（加入动画时间说明）

优先级：P1 (影响最终用户体验)
```

---

## 🚀 下一步行动

### 对 YOU

```
□ 逐个阅读 5 份文档 (建议 2-3 小时完整阅读)
□ 反馈调整意见 (在文档中批注或发列表)
□ 确认数据源 (3 个游戏的角色数据是否已准备？JSON 还是手工录入？)
□ 启动 Antigravity Planning 模式 → 生成详细开发计划
□ 在 Cursor 中初始化项目结构
```

### 对 Antigravity + Claude & Gemini

```
□ 阅读完整规范 → 理解项目全景
□ 根据 Human 确认的反馈更新规范
□ 生成第一个 Sprint 的任务（第 1 周：数据层 + 基础组件）
□ 为 Claude 和 Gemini 准备清晰的任务卡和 Prompt 模板
□ 跟踪交付进度，每日同步（或每 Sprint 同步）
```

---

## 📎 附件与资源

### 需要补充的外部资源

- [ ] **游戏角色数据** (JSON 格式样本)
- [ ] **立绘素材** (或立绘获取来源)
- [ ] **LOGO / 品牌资源** (如需要)
- [ ] **字体文件** (SourceHanSans-Bold.woff2 等)

### 可选的增强资源

- [ ] **Figma 设计稿** (UI 组件库参考)
- [ ] **用户流程视频** (展示期望的交互)
- [ ] **竞品参考** (类似的工具、排行榜设计)
- [ ] **测试数据** (模拟数据集，供开发测试)

---

## 🎓 学习资源

### 推荐阅读（用于深入理解）

**关于 Zustand 状态管理：**
- https://github.com/pmndrs/zustand

**关于 React Three Fiber：**
- https://docs.pmnd.rs/react-three-fiber/

**关于拖拽交互：**
- https://docs.dndkit.com/ (@dnd-kit 官方文档)

**关于 Framer Motion：**
- https://www.framer.com/motion/

---

**文档版本：** v1.0.0  
**最后更新：** 2025-12-04  
**下一次计划更新：** 第一个 Sprint 完成后

---

**有问题或需要澄清？直接联系 Human。** 💬
