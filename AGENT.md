# 性格测试 — AI 代理开发规范

> 本文件为 AI 代理（含人工协作）在本项目中开发时必须遵守的强制性规范。
> 任何代码提交前均需通过 `pnpm lint`，不通过则不可提交。

---

## 一、项目概览

| 项 | 值 |
|---|---|
| 项目名 | 性格测试小程序 |
| 技术栈 | uni-app + Vue3 (Composition API / `<script setup>`) + Vite + TypeScript + Pinia |
| 首版目标 | MBTI 性格测试，微信小程序 |
| 开发调试 | H5 (`pnpm dev:h5`)，发布时切小程序 |
| 主题色 | 蒂芙尼蓝 — 主色 `#81D8D0`，强调色 `#0ABAB5` |
| 无后端 | 题库 JSON 硬编码，历史记录存 localStorage |
| 默认性别 | 女（结果页胶囊切换只改图片，不改文案） |

---

## 二、ESLint 强制规范

### 2.1 必须通过 lint

所有代码在提交前 **必须** 通过 ESLint 检查，零 error，零 warning（`@typescript-eslint/no-unused-vars` 和 `@typescript-eslint/no-explicit-any` 的 warn 亦需修复）。

```bash
# 检查
pnpm lint

# 自动修复
pnpm lint:fix
```

### 2.2 当前 ESLint 配置要点

配置文件：`eslint.config.mjs`（flat config）

| 规则 | 级别 | 说明 |
|---|---|---|
| `@typescript-eslint/no-unused-vars` | warn → **视同 error** | 未使用变量必须删除或加 `_` 前缀 |
| `@typescript-eslint/no-explicit-any` | warn → **视同 error** | 禁止 `any`，必须给出具体类型 |
| `@typescript-eslint/no-empty-function` | off | 允许空函数 |
| `vue/multi-word-component-names` | off | 允许单词组件名（uni-app 页面惯例） |
| `vue/html-indent` | error | 2 空格缩进 |
| `vue/html-self-closing` | error | 所有标签自闭合 |
| `vue/max-attributes-per-line` | off | 不限单行属性数 |
| `vue/require-default-prop` | off | prop 无需默认值 |
| `vue/no-v-html` | off | 允许 v-html |
| `vue/singleline-html-element-content-newline` | off | 不强制换行 |

### 2.3 补充规则（代理须自觉遵守）

以下规则未在 eslint 中配置但属于强制开发约定：

1. **禁止 `console.log` 留在生产代码中** — 调试用完后删除，或改用条件编译 `#ifdef H5`
2. **禁止硬编码魔法数字** — 提取为命名常量
3. **import 顺序** — Vue → 第三方库 → @/ 别名 → 相对路径，各组间空一行
4. **组件 props 必须定义类型** — 使用 `defineProps<T>()` 泛型写法
5. **emit 必须定义类型** — 使用 `defineEmits<T>()` 泛型写法

---

## 三、注释规范（强制）

> **所有变量和函数都必须添加注释，无一例外。**

### 3.1 注释格式统一标准

#### 变量注释 — 使用 JSDoc `/** */` 行内注释

```typescript
/** 当前选中的题目索引 */
const currentIndex = ref<number>(0)

/** 用户答题记录，键为题目ID，值为选项索引 */
const answers = reactive<Record<number, number>>({})

/** 测试是否已完成 */
const isFinished = computed<boolean>(() => currentIndex.value >= questions.length)

/** 蒂芙尼蓝主色 */
const PRIMARY_COLOR = '#81D8D0'
```

#### 函数/方法注释 — 使用 JSDoc 完整块注释

```typescript
/**
 * 提交单题答案并跳转下一题
 * @param optionIndex - 用户选择的选项索引（从0开始）
 * @returns 无返回值
 */
function selectOption(optionIndex: number): void {
  answers[currentIndex.value] = optionIndex
  currentIndex.value++
}

/**
 * 计算MBTI类型结果
 * @param answerMap - 答题记录映射
 * @returns 四维MBTI类型字符串，如 'INFJ'
 */
function calculateResult(answerMap: Record<number, number>): string {
  // ...
}
```

#### Computed 注释 — 同变量格式

```typescript
/** 已答题数量 */
const answeredCount = computed<number>(() => Object.keys(answers).length)
```

#### 生命周期钩子注释 — 简要说明用途

```typescript
/** 页面加载时初始化题库和恢复历史记录 */
onLoad(() => {
  initQuestions()
  restoreHistory()
})
```

#### Props / Emits 注释 — 在类型定义上方

```typescript
/** 题目选项组件的属性定义 */
interface OptionProps {
  /** 选项文本内容 */
  label: string
  /** 选项索引 */
  index: number
  /** 是否已被选中 */
  selected: boolean
}

const props = defineProps<OptionProps>()

/** 选项点击事件定义 */
interface OptionEmits {
  /** 用户点击选项时触发，传递选项索引 */
  (e: 'select', index: number): void
}

const emit = defineEmits<OptionEmits>()
```

### 3.2 注释规范速查

| 场景 | 格式 | 示例 |
|---|---|---|
| ref / reactive 变量 | `/** 说明 */` | `/** 用户昵称 */` |
| 常量 | `/** 说明 */` | `/** 最大答题时间(秒) */` |
| computed | `/** 说明 */` | `/** 进度百分比 */` |
| 函数 | `/** 说明 + @param + @returns */` | 见上方示例 |
| 生命周期 | `/** 说明 */` | `/** 页面显示时刷新数据 */` |
| Props 字段 | `/** 说明 */` | `/** 选项文本 */` |
| Emits 事件 | `/** 说明 */` | `/** 点击时触发 */` |
| 接口/类型 | `/** 说明 */` | `/** 题目数据结构 */` |
| 复杂业务逻辑 | 行内 `// 说明` | `// E维度：外向者得分需≥5才判定为E` |

### 3.3 禁止事项

- 禁止无意义注释：`/** data */` `/** val */` `/** flag */`
- 禁止注释与代码矛盾：改代码必须同步改注释
- 禁止废弃注释：删除的死代码不要留注释说明

---

## 四、项目目录约定

```
src/
├── pages/              # 页面（每个页面一个目录）
│   ├── index/          # 首页
│   ├── test/           # 答题页
│   └── result/         # 结果页
├── components/         # 公共组件
├── stores/             # Pinia 状态管理
├── utils/              # 工具函数
├── data/               # 题库JSON等静态数据
├── static/             # 图片等静态资源
├── App.vue
├── main.ts
└── pages.json          # 页面路由配置
```

---

## 五、命名约定

| 类型 | 风格 | 示例 |
|---|---|---|
| 页面目录 | kebab-case | `pages/test-question/` |
| 组件文件 | PascalCase | `QuestionCard.vue` |
| 工具函数 | camelCase | `formatTime.ts` |
| Store | camelCase | `useTestStore.ts` |
| 常量 | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT` |
| ref / reactive | camelCase | `currentStep` |
| CSS class | kebab-case | `.result-card` |
| 事件名 | kebab-case | `@option-select` |

---

## 六、Git 提交规范

```
<type>(<scope>): <description>

type: feat | fix | docs | style | refactor | perf | test | chore
scope: 页面或模块名，如 test / result / store
```

示例：
- `feat(test): 添加MBTI答题页进度条`
- `fix(result): 修复类型计算维度边界错误`
- `refactor(store): 拆分测试状态管理`

---

## 七、开发流程清单

每次开发任务必须按以下顺序执行：

1. [ ] 编写/修改代码，遵守注释规范
2. [ ] `pnpm lint:fix` 自动修复
3. [ ] `pnpm lint` 确认零 error、零 warning
4. [ ] `pnpm dev:h5` 验证功能正常
5. [ ] 按规范提交 Git

---

## 八、关键提醒

- 结果页胶囊切换：**只换图片，不改文案**
- 性别默认 **女**
- 首版 **无广告位**、**无管理端**
- 首版 **仅 MBTI**，九型/大五后续迭代
- 图片资源由人工提供，代理只需说明目录结构和规格
- 必须用 `socks5h://` 代理前缀（远程DNS解析）
