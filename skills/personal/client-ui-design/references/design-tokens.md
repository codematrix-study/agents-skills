# 客户端设计令牌(亮 / 暗双主题)

已有项目先沿用现有变量。客户端 tokens 与网站的区别:**层级靠"表面明度"、密度更紧凑、必须双主题**。

## 目录
1. 颜色(表面 / 文字 / 品牌 / 语义)
2. 字号与密度
3. 间距、圆角、阴影
4. 动效
5. 平台差异
6. 主题切换实现

## 1. 颜色

```css
:root {
  color-scheme: light dark;

  /* 表面:由深到浅 = 由底到顶 */
  --surface-0: #F3F4F7;  /* 窗口底、侧边栏 */
  --surface-1: #FFFFFF;  /* 主内容区 */
  --surface-2: #FFFFFF;  /* 浮层、弹窗、菜单 */
  --surface-hover: rgba(20,24,40,.05);
  --surface-active: rgba(20,24,40,.09);
  --surface-selected: var(--brand-50);

  --separator: rgba(20,24,40,.08);   /* 分隔线用半透明,适配任何底色 */
  --border: rgba(20,24,40,.12);

  --text-1:#1D2129; --text-2:#4E5565; --text-3:#858C9C; --text-disabled:#B3B8C4;

  --brand-50:#EEF2FF; --brand-500:#3D63F2; --brand-600:#2F51D6; --on-brand:#FFFFFF;

  --success:#18A058; --warning:#E08A00; --danger:#D9363E;
  --success-bg:rgba(24,160,88,.12); --warning-bg:rgba(224,138,0,.14); --danger-bg:rgba(217,54,62,.12);

  --focus-ring: 0 0 0 2px var(--surface-1), 0 0 0 4px rgba(61,99,242,.55);
}
:root[data-theme="dark"] {
  --surface-0:#16181D;   /* 侧栏最深 */
  --surface-1:#1D2026;   /* 主区略亮 */
  --surface-2:#262A32;   /* 浮层最亮 */
  --surface-hover: rgba(255,255,255,.06);
  --surface-active: rgba(255,255,255,.10);
  --surface-selected: rgba(110,140,255,.16);
  --separator: rgba(255,255,255,.07); --border: rgba(255,255,255,.12);
  --text-1:#ECEEF3; --text-2:#B5BAC8; --text-3:#7F8696; --text-disabled:#535968;
  --brand-500:#6E8CFF; --brand-600:#88A0FF; --on-brand:#0F1320;
  --success:#3CC27A; --warning:#F0A63A; --danger:#F0626A;
}
```

要点:
- 暗色层级:**越靠上的层越亮**(浮层 > 主区 > 侧栏),阴影在暗色下几乎不可见,改靠明度差 + 1px 半透明描边。
- 分隔线、hover、选中用**半透明**叠色,不写死灰色,这样换底色也协调。
- 品牌色在暗色下提亮一档并降低饱和;按钮文字 `--on-brand` 要重新检查对比度。
- 强调色面积 ≤ 5%:主按钮、选中指示、进度、未读点。

## 2. 字号与密度

| 档位 | 桌面(紧凑) | 移动 | 用途 |
|---|---|---|---|
| caption | 11~12px | 12px | 时间戳、角标 |
| body-s | 12~13px | 14px | 列表次要信息、辅助说明 |
| body | 13~14px | 16px | 正文、列表项 |
| title-s | 15px | 17px | 小标题、对话标题 |
| title | 18px | 20~22px | 页面标题 |
| display | 24~28px | 28~34px | 空状态主标题、欢迎页 |

字体:桌面用系统字体栈(`-apple-system, "SF Pro Text", "Segoe UI Variable", "PingFang SC", "Microsoft YaHei UI", sans-serif`);移动端用系统默认(iOS SF / Android Roboto + 系统中文字体)。字重 400/500/600。

密度档位(桌面):紧凑 列表行 32px / 标准 40px / 宽松 48px。**产品定一档做默认**,可在设置里切换,但页面内不混用。

## 3. 间距、圆角、阴影

```css
:root {
  --space-1:4px; --space-2:8px; --space-3:12px; --space-4:16px; --space-5:20px; --space-6:24px; --space-8:32px;
  --radius-xs:4px;   /* 标签、小控件 */
  --radius-sm:6px;   /* 输入框、列表项选中底 */
  --radius-md:8px;   /* 按钮、卡片 */
  --radius-lg:12px;  /* 弹窗、面板 */
  --radius-pill:999px;
  --shadow-pop: 0 8px 24px rgba(16,24,40,.16), 0 0 0 1px var(--separator);  /* 菜单、弹层 */
}
```

同一产品圆角体系统一:选中底 6px、按钮 8px、弹窗 12px,嵌套时"内圆角 = 外圆角 − 内边距"。

## 4. 动效

```css
:root {
  --ease-standard: cubic-bezier(.2,0,0,1);
  --ease-decel: cubic-bezier(0,0,.2,1);   /* 进入 */
  --ease-accel: cubic-bezier(.4,0,1,1);   /* 离开 */
  --dur-instant:80ms; --dur-fast:150ms; --dur-base:220ms; --dur-slow:320ms;
}
```

- 按压/hover 反馈 80~150ms;面板切换 200~250ms;页面转场 ≤ 320ms。
- 进入用减速、离开用加速且更短(离开时长约为进入的 70%)。
- 尊重系统"减少动态":改为淡入淡出或无动画。

## 5. 平台差异速查

| 维度 | macOS | Windows | iOS | Android |
|---|---|---|---|---|
| 圆角倾向 | 8~10 | 4~8 | 10~14 | 12~16(M3) |
| 主导航 | 左侧栏 | 左侧栏/顶部 | 底部 Tab | 底部导航/抽屉 |
| 返回 | ⌘[ / 标题栏按钮 | Alt+← | 左边缘右滑 | 系统返回手势 |
| 主快捷键 | ⌘ | Ctrl | — | — |
| 材质 | 半透明 vibrancy | Mica/亚克力 | 模糊毛玻璃 | 色调表面 |
| 菜单 | 系统顶部菜单栏 | 窗口内菜单 | 上下文菜单/ActionSheet | 底部菜单/Snackbar |

## 6. 主题切换实现

- 默认跟随系统:`matchMedia('(prefers-color-scheme: dark)')`;用户可手动覆盖并持久化。
- Electron:`nativeTheme.themeSource` 与 `data-theme` 同步;窗口背景色在 `BrowserWindow` 创建时就设为对应主题色,避免白闪。
- 切换时不要对全部属性加 transition(性能与闪烁),仅对背景色/文字色做 150ms 过渡或直接切换。
- 图片/插画提供暗色版本或使用 `currentColor` 的 SVG。