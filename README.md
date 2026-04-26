# 灵析 (Character)

一款多平台性格测试小程序，首版上线微信小程序，后续扩展抖音、QQ 等平台。

## 技术栈

- **框架**: uni-app (Vue 3 + Composition API + Vite + TypeScript)
- **状态管理**: Pinia
- **UI 组件**: uni-ui
- **图表**: uCharts / Canvas
- **首版无后端**，题库数据 JSON 硬编码，历史记录存 localStorage

## 主题色

| 用途       | 色值     | 说明                   |
|------------|----------|------------------------|
| 主色       | #81D8D0  | 蒂芙尼蓝（背景/渐变）   |
| 强调色     | #0ABAB5  | 深蒂芙尼蓝（按钮/点缀） |
| 辅色       | #FFFFFF  | 白色                   |
| 标题文字   | #111111  | 深黑                   |
| 正文文字   | #333333  | 深灰                   |
| 辅助文字   | #888888  | 中灰                   |
| 背景色     | #F5F5F5  | 浅灰                   |
| 分割线     | #EEEEEE  | 极细线                 |
| 标签背景   | #81D8D0  | 20% 透明度             |

## 页面

| 页面   | 路径             | 说明                          |
|--------|------------------|-------------------------------|
| P1 首页 | /pages/index     | 沉浸式单测试入口              |
| P2 答题 | /pages/quiz      | 10 题快速测试（选择题+滑动题） |
| P3 结果 | /pages/result    | 首屏情绪命中 + 第二屏深度认同  |
| P4 海报 | /pages/poster    | Canvas 生成分享海报            |
| P5 分享 | /pages/share     | 裂变落地页                    |
| P6 历史 | /pages/history   | 测试记录回看                  |

## 开发

```bash
# H5 开发调试
npm run dev:h5

# 微信小程序
npm run dev:mp-weixin

# 构建
npm run build:h5
npm run build:mp-weixin
```

## 首版范围

- 仅 MBTI 十六型人格测试
- 功能闭环：答题 → 结果 → 分享海报
- 广告位预留但暂不上线
- 管理端不做，题库数据硬编码
- 九型人格 / 大五人格后续版本接入

## 项目结构

```
src/
├── pages/              # 页面
├── components/         # 公共组件
├── data/               # 题库和性格数据
├── store/              # Pinia 状态管理
├── utils/              # 工具函数
└── static/             # 图片资源
```

## 文档

需求与设计文档位于 `docs/` 目录：

- `requirements.md` — 需求概述
- `technical-design.md` — 技术方案
- `pages-design.md` — 页面设计
- `assets-guide.md` — 图片资源指南
- `mbti-data.md` — MBTI 题库与算分逻辑
- `task-list.md` — 开发任务列表
