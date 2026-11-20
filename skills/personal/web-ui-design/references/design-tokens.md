# 设计令牌(Design Tokens)模板

已有项目先读现有变量并沿用。仅在没有体系时使用本模板。品牌色换成项目的主色即可,其余保持不变。

## 目录
1. 颜色
2. 字体与字号
3. 间距、圆角、阴影
4. 动效
5. 暗色模式
6. Tailwind 映射
7. 数字格式化工具

## 1. 颜色

灰阶带轻微冷色相,比纯灰更高级;不要用 #000。

```css
:root {
  /* 中性色 */
  --bg-page:      #F5F6FA;   /* 页面底色 */
  --bg-surface:   #FFFFFF;   /* 卡片/面板 */
  --bg-subtle:    #F0F2F7;   /* 表头、悬停、次级区块 */
  --border:       #E4E7EE;   /* 默认边框 */
  --border-strong:#CDD2DD;   /* 输入框、强调分隔 */
  --text-1:       #1F2430;   /* 标题、关键数字 */
  --text-2:       #4A5163;   /* 正文 */
  --text-3:       #7B8294;   /* 辅助、单位、占位 */
  --text-disabled:#A9AEBB;

  /* 品牌(主色仅用于主操作、选中态、链接) */
  --brand-50:  #EEF3FF;
  --brand-100: #DCE6FF;
  --brand-500: #2F5BEA;
  --brand-600: #2449C8;      /* hover */
  --brand-700: #1C3A9F;      /* active */

  /* 语义色(各配 bg / fg / border 三件套) */
  --success-bg:#E8F7EE; --success-fg:#13804A; --success-bd:#BFE6CF;
  --warning-bg:#FFF4E0; --warning-fg:#A85F00; --warning-bd:#F5D9A8;
  --danger-bg: #FDECEC; --danger-fg: #C62828; --danger-bd: #F5C2C2;
  --info-bg:   var(--brand-50); --info-fg: var(--brand-600); --info-bd: var(--brand-100);

  --focus-ring: 0 0 0 3px rgba(47, 91, 234, .28);
}
```

规则:语义色只用于状态;品牌色占页面面积 ≤ 10%;图表色板最多 6 色,第一色用品牌色。

## 2. 字体与字号

```css
:root {
  --font-sans: -apple-system, BlinkMacSystemFont, "Inter", "PingFang SC",
               "Microsoft YaHei", "Noto Sans SC", sans-serif;
  --font-mono: "JetBrains Mono", "SF Mono", Menlo, Consolas, monospace;

  --fs-12: 12px;  --lh-12: 18px;   /* 标签、脚注 */
  --fs-13: 13px;  --lh-13: 20px;   /* 辅助文字、表头 */
  --fs-14: 14px;  --lh-14: 22px;   /* 正文(管理端默认) */
  --fs-16: 16px;  --lh-16: 24px;   /* 正文(C 端默认)、小标题 */
  --fs-20: 20px;  --lh-20: 28px;   /* 区块标题 */
  --fs-28: 28px;  --lh-28: 36px;   /* 页面标题、KPI 数字 */
  /* C 端营销页追加:40 / 56 / 72 */
}
body { font-family: var(--font-sans); font-size: var(--fs-14); color: var(--text-2); }
.num { font-variant-numeric: tabular-nums; font-feature-settings: "tnum"; }
```

字重只用 400 / 500 / 600。中文标题不要用 700 以上(笔画过粗)。中文行高正文 1.6~1.75。

## 3. 间距、圆角、阴影

```css
:root {
  --space-1:4px; --space-2:8px; --space-3:12px; --space-4:16px;
  --space-6:24px; --space-8:32px; --space-12:48px; --space-16:64px;

  --radius-sm:6px;   /* 标签、输入框 */
  --radius-md:8px;   /* 按钮 */
  --radius-lg:12px;  /* 卡片 */
  --radius-xl:16px;  /* 弹窗、大面板 */

  --shadow-1: 0 1px 2px rgba(16,24,40,.04);                         /* 卡片 */
  --shadow-2: 0 4px 12px rgba(16,24,40,.08);                        /* 悬浮、下拉 */
  --shadow-3: 0 12px 32px rgba(16,24,40,.14);                       /* 弹窗 */
}
```

## 4. 动效

```css
:root {
  --ease-out: cubic-bezier(.2,.8,.2,1);
  --dur-fast:120ms; --dur-base:200ms; --dur-slow:320ms;
}
@media (prefers-reduced-motion: reduce) {
  * { animation-duration:.01ms !important; transition-duration:.01ms !important; }
}
```

只对 `opacity` / `transform` / `background-color` 做过渡;hover 反馈 ≤ 150ms;进场 200~320ms。

## 5. 暗色模式

```css
:root[data-theme="dark"], @media (prefers-color-scheme: dark) {
  --bg-page:#0F1115; --bg-surface:#171A21; --bg-subtle:#1E222B;
  --border:#262B36; --border-strong:#353B49;
  --text-1:#ECEEF3; --text-2:#B8BDCB; --text-3:#818899;
  --brand-500:#5B82FF; --brand-600:#7595FF;
  --shadow-1:none; --shadow-2:0 4px 12px rgba(0,0,0,.4);
}
```

暗色要点:不用纯黑底;层级靠"更亮的表面色"而非阴影;语义色降低饱和、提高明度;品牌色整体提亮一档。

## 6. Tailwind 映射

```js
// tailwind.config.js
theme: { extend: {
  colors: {
    page:'var(--bg-page)', surface:'var(--bg-surface)', subtle:'var(--bg-subtle)',
    border:'var(--border)', brand:{500:'var(--brand-500)',600:'var(--brand-600)',50:'var(--brand-50)'},
    ink:{1:'var(--text-1)',2:'var(--text-2)',3:'var(--text-3)'},
  },
  borderRadius:{ sm:'6px', md:'8px', lg:'12px', xl:'16px' },
  boxShadow:{ 1:'var(--shadow-1)', 2:'var(--shadow-2)', 3:'var(--shadow-3)' },
}}
```

## 7. 数字格式化工具(TypeScript)

```ts
const nf = (min: number, max: number) =>
  new Intl.NumberFormat('zh-CN', { minimumFractionDigits: min, maximumFractionDigits: max });

export const formatInt     = (n: number) => nf(0, 0).format(n);
export const formatPoints  = (n: number) => nf(0, 2).format(n);          // 积分:最多 2 位
export const formatMoney   = (n: number) => '¥' + nf(2, 4).format(n);    // 金额:2~4 位
export const formatPercent = (n: number) => nf(0, 1).format(n * 100) + '%';
export const formatCompact = (n: number) =>
  new Intl.NumberFormat('zh-CN', { notation: 'compact', maximumFractionDigits: 1 }).format(n);
```

KPI 渲染:`<span class="num">{value}</span><span class="unit">{unit}</span>`,整体 `display:inline-flex; align-items:baseline; gap:4px`。数字与小数**不要**拆成两个字号。