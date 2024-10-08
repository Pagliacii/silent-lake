+++
title = 'Home Row Mods'
date = 2024-10-08T23:23:04+08:00
draft = false
toc = true
math = false
tags = ["keyboard", "kanata", "tips"]
+++

## 什么是 home row？

[Home row](https://en.wikipedia.org/wiki/Touch_typing#Home_row) 是键盘上中间的那一排按键。以常见的 [QWERTY](https://en.wikipedia.org/wiki/QWERTY) 键盘为例，home row 就是 <kbd>A</kbd><kbd>S</kbd><kbd>D</kbd><kbd>F</kbd><kbd>G</kbd><kbd>H</kbd><kbd>J</kbd><kbd>K</kbd><kbd>L</kbd><kbd>;</kbd> 这一排按键。在 <kbd>F</kbd> 和 <kbd>J</kbd> 键上还有定位用的横杠，方便盲打时手指快速移动到相应的位置。

## 什么是 mods？

Mods 全称是 [Modifiers](https://en.wikipedia.org/wiki/Modifier_key)，指的是键盘上的修饰键，如 <kbd>⌃ Ctrl</kbd>、<kbd>⎇ Alt</kbd>、<kbd>⇧ Shift</kbd>、<kbd>⊞ Win</kbd>、<kbd>⌘ Command</kbd> 等。

## 什么是 home row mods？

通常来说，Mods 按键都位于键盘的左下角和右下角，处在 home row 上的手不得不移动到边上才能够到这些按键。而 home row mods 则是借助于 [QMK](https://qmk.fm/) 的 [Mod-tap](https://docs.qmk.fm/mod_tap) 功能，来让 home row 按键作为[两个按键](https://en.wikipedia.org/wiki/Modifier_key#Dual-role_keys)来使用，点按（**tap**）时则是键帽上印着的按键，按住（**hold**）时则是映射过去的 Mods 按键。

## 常见的 home row mods 映射

这里以 QWERTY 键盘为例，介绍一些常见的 home row mods 映射。

首先约定一些符号：

| Full Modifier Name                                                                                                                                                       | Abbreviation | Symbol |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------ | ------ |
| [Shift](https://en.wikipedia.org/wiki/Shift_key)                                                                                                                         | S            | ⇧      |
| [Control](https://en.wikipedia.org/wiki/Control_key)                                                                                                                     | C            | ⎈      |
| [Alt](https://en.wikipedia.org/wiki/Alt_key)/[Option](https://en.wikipedia.org/wiki/Option_key)                                                                          | A            | ⎇      |
| [GUI](<https://en.wikipedia.org/wiki/Super_key_(keyboard_button)>)/[Win](https://en.wikipedia.org/wiki/Windows_key)/[Command](https://en.wikipedia.org/wiki/Command_key) | G            | ◆      |
| No modifier/mod-tap                                                                                                                                                      | \_           | \_     |

接下来就是常见的映射：

- **SCGA**: <kbd>⇧</kbd><kbd>⎈</kbd><kbd>◆</kbd><kbd>⎇</kbd><kbd>\_</kbd><kbd>\_</kbd><kbd>⎇</kbd><kbd>◆</kbd><kbd>⎈</kbd><kbd>⇧</kbd>
- **GASC**: <kbd>◆</kbd><kbd>⎇</kbd><kbd>⇧</kbd><kbd>⎈</kbd><kbd>\_</kbd><kbd>\_</kbd><kbd>⎈</kbd><kbd>⇧</kbd><kbd>⎇</kbd><kbd>◆</kbd>
- **GACS**: <kbd>◆</kbd><kbd>⎇</kbd><kbd>⎈</kbd><kbd>⇧</kbd><kbd>\_</kbd><kbd>\_</kbd><kbd>⇧</kbd><kbd>⎈</kbd><kbd>⎇</kbd><kbd>◆</kbd>
- **CAGS**: <kbd>⎈</kbd><kbd>⎇</kbd><kbd>◆</kbd><kbd>⇧</kbd><kbd>\_</kbd><kbd>\_</kbd><kbd>⇧</kbd><kbd>⎇</kbd><kbd>⎈</kbd><kbd>◆</kbd>

以 **GASC** 为例，这是在 QWETRY home row 上的映射关系：

| Home row | <kbd>A</kbd> | <kbd>S</kbd> | <kbd>D</kbd> | <kbd>F</kbd> | <kbd>G</kbd>  | <kbd>H</kbd>  | <kbd>J</kbd> | <kbd>K</kbd> | <kbd>L</kbd> | <kbd>;</kbd> |
| -------- | ------------ | ------------ | ------------ | ------------ | ------------- | ------------- | ------------ | ------------ | ------------ | ------------ |
| Tap      | <kbd>A</kbd> | <kbd>S</kbd> | <kbd>D</kbd> | <kbd>F</kbd> | <kbd>G</kbd>  | <kbd>H</kbd>  | <kbd>J</kbd> | <kbd>K</kbd> | <kbd>L</kbd> | <kbd>;</kbd> |
| Hold     | <kbd>◆</kbd> | <kbd>⎇</kbd> | <kbd>⇧</kbd> | <kbd>⎈</kbd> | <kbd>\_</kbd> | <kbd>\_</kbd> | <kbd>⎈</kbd> | <kbd>⇧</kbd> | <kbd>⎇</kbd> | <kbd>◆</kbd> |

### 上述映射的优缺点

- **SCGA**: 这是直接将 Mods 按键在键盘上的顺序，映射到 home row 的结果。忽略了 Mods 按键的使用频率，比如将 <kbd>⎇</kbd> 和 <kbd>◆</kbd> 放在了最方便按到的食指和中指的位置，但是很明显这比 <kbd>⇧</kbd> 和 <kbd>⎈</kbd> 使用频率要低得多。
- **GASC**: 这种布局将 <kbd>⎈</kbd> 和 <kbd>⇧</kbd> 放在了食指和中指的位置，将使用频率更高的按键放在更灵活的手指上，更加合理和舒适。而且将 <kbd>⇧</kbd> 放在中指上，食指就可以很方便地去按其他按键从而输入大写字母。缺点就是 <kbd>◆</kbd> 在尾指位置上，在 macOS 上就没那么方便按。
- **GACS**: 这种布局则是将使用频率最高的修饰键放在最灵活的手指下，使用频率最低的放在最弱的手指处。同时它避免了 **GASC** 布局无法单手 <kbd>Ctrl</kbd>+<kbd>Letter</kbd> 的尴尬局面，比如 <kbd>Ctrl</kbd>+<kbd>C</kbd>。
- **CAGS**: 这种布局则是在 macOS 上更加合理和方便。

另外这些 home row key 就不能长按了，因为长按时会被 Mods 按键覆盖掉。这在玩游戏时会造成不便，所以建议定义一个 game mode layer 来禁用 home row mods。

## 组合修饰键

还有一种用法就是在 lower row 上，即 home row 下一排的按键，将一些常用组合修饰键映射上去。比如 <kbd>Ctrl</kbd>+<kbd>Shift</kbd> 映射到 <kbd>C</kbd> 上。

这里举一个例子：

| Lower row |       <kbd>Z</kbd>        |       <kbd>X</kbd>        |       <kbd>C</kbd>        | <kbd>V</kbd> | <kbd>B</kbd> | <kbd>N</kbd> |       <kbd>M</kbd>        |       <kbd>,</kbd>        |       <kbd>.</kbd>        |
| --------- | :-----------------------: | :-----------------------: | :-----------------------: | :----------: | :----------: | :----------: | :-----------------------: | :-----------------------: | :-----------------------: |
| Tap       |       <kbd>Z</kbd>        |       <kbd>X</kbd>        |       <kbd>C</kbd>        | <kbd>V</kbd> | <kbd>B</kbd> | <kbd>N</kbd> |       <kbd>M</kbd>        |       <kbd>,</kbd>        |       <kbd>.</kbd>        |
| Hold      | <kbd>◆</kbd>+<kbd>⎇</kbd> | <kbd>⎇</kbd>+<kbd>⎈</kbd> | <kbd>⎈</kbd>+<kbd>⇧</kbd> | <kbd>V</kbd> | <kbd>B</kbd> | <kbd>N</kbd> | <kbd>⇧</kbd>+<kbd>⎈</kbd> | <kbd>⎈</kbd>+<kbd>⎇</kbd> | <kbd>⎇</kbd>+<kbd>◆</kbd> |

{{< note >}}
如果想了解更多，可以阅读 [A guide to home row mods](https://precondition.github.io/home-row-mods)，里面有更多详细说明和 [tips](https://precondition.github.io/home-row-mods#tips-and-tricks)。不过涉及了很多 QMK 相关的内容，如果不打算使用 QMK 的话，这部分可以略过。
{{< /note >}}

## Kanata 实现

我选择了 [kanata](https://github.com/jtroo/kanata) 而不是 [A guide to home row mods](https://precondition.github.io/home-row-mods) 里[提及](https://precondition.github.io/home-row-mods#using-home-row-mods-with-kmonad)的 [KMonad](https://github.com/kmonad/kmonad) 作为实现 home row mods 的工具。不过 kanata 是受 KMonad 启发而用 [Rust](https://www.rust-lang.org/) 写的，功能上差不多。

Kanata 仓库里就有两份 home row mods 的示例配置，分别是 [home-row-mod-basic.kbd](https://github.com/jtroo/kanata/blob/main/cfg_samples/home-row-mod-basic.kbd) 和 [home-row-mod-advanced.kbd](https://github.com/jtroo/kanata/blob/main/cfg_samples/home-row-mod-advanced.kbd)。

我之前也写过一篇使用 kanata 将 <kbd>CapsLock</kbd> 映射成 <kbd>Ctrl</kbd> 和 <kbd>Esc</kbd> 的[文章](https://pagliacii.github.io/silent-lake/blog/using-capslock-as-ctrl-and-escape/)，可以作为 kanata 的简单介绍。

也可以使用 [kanata online simulator](https://jtroo.github.io/) 来测试配置是否正确。

下面是我目前使用的 kanata 配置：

```lisp
(defsrc
  esc  1    2    3    4    5    6    7    8    9    0    -    =    bspc
  tab  q    w    e    r    t    y    u    i    o    p    [    ]    \
  caps a    s    d    f    g    h    j    k    l    ;    '    ret
  lsft z    x    c    v    b    n    m    ,    .    /    rsft
  lctl lmet lalt           spc            ralt rctl
)

(deflayer qwerty
  @eca 1    2    3    4    5    6    7    8    9    0    -    =    bspc
  tab  @q`  w    e    r    t    y    u    i    o    p    [    ]    \
  @cap a    s    d    f    g    h    j    k    l    ;    '    ret
  lsft z    x    c    v    b    n    m    ,    .    /    rsft
  @cap lmet lalt           spc            ralt rctl
)

(defalias
  cap (tap-hold-press 200 200 esc lctl)
  ;; 1 tap : "Escape"
  ;; 2 tap : "Caps Lock"
  eca (tap-dance 200 (esc caps))
  ;; 1 tap : "q"
  ;; 2 tap : "`"
  q` (tap-dance 200 (q grave))
)
```

我打算采用 **GASC** 布局。至于无法单手 <kbd>Ctrl</kbd>+<kbd>Letter</kbd> 的问题，我可以通过 <kbd>CapsLock</kbd>+<kdb>Letter</kdb> 来代替，因为我的 <kbd>CapsLock</kbd> 已经是 dual-role key 了。

### defalias

首先需要定义一些 `tap-hold` 别名来描述 Dual role keys，这里可以参考 kanata 示例配置里的 [home-row-mod-advanced.kbd](https://github.com/jtroo/kanata/blob/main/cfg_samples/home-row-mod-advanced.kbd)：

```lisp
(defalias
  ;; home row mods -- GASC
  met_a (tap-hold-release-keys $tt $ht (multi a @tap) lmet $left-hand-keys)
  alt_s (tap-hold-release-keys $tt $ht (multi s @tap) lalt $left-hand-keys)
  sft_d (tap-hold-release-keys $tt $ht (multi d @tap) lsft $left-hand-keys)
  ctl_f (tap-hold-release-keys $tt $ht (multi f @tap) lctl $left-hand-keys)

  ctl_j (tap-hold-release-keys $tt $ht (multi j @tap) rctl $right-hand-keys)
  sft_k (tap-hold-release-keys $tt $ht (multi k @tap) rsft $right-hand-keys)
  alt_l (tap-hold-release-keys $tt $ht (multi l @tap) lalt $right-hand-keys)
  met_; (tap-hold-release-keys $tt $ht (multi ; @tap) rmet $right-hand-keys)
)
```

在上面的 alias 里用到的 action 是 [`tap-hold-release-keys`](https://github.com/jtroo/kanata/blob/main/docs/config.adoc#tap-hold)，它的作用是当第 5 个参数中的按键在当前动作发生时被按下，提前触发一次点按，减少同手滚动输入的误差。

比如你想连按 <kbd>k</kbd> 和 <kbd>l</kbd> 来输入 `kl`，使用 `tap-hold-release-keys` 就可以减少输入成 <kbd>Shift</kbd><kbd>l</kbd> 的可能性。

然后再定义一些变量：

```lisp
(defvar
  tap-timeout   50
  hold-timeout  200
  tt $tap-timeout
  ht $hold-timeout

  left-hand-keys (
    q w e r t
    a s d f g
    z x c v b
  )
  right-hand-keys (
    y u i o p
    h j k l ;
    n m , . /
  )
)
```

其中 `left-hand-keys` 和 `right-hand-keys` 变量分别是左手和右手的按键，用作 `tap-hold-release-keys` 的第 5 个参数。

至于 `tap-timeout` 和 `hold-timeout` 则可以根据自己的按键习惯来进行设置。

```lisp
(defalias
  tap (multi
    @nomods
    (on-idle-fakekey to-base tap 20)
  )
)
```

`tap` 则是一个虚拟按键，用于在 home row 按键点按触发时临时切换至 base layer，以防止误触。具体定义如下：

```lisp
(deffakekeys
  to-base (layer-switch qwerty)
)
```

### deflayer

接着定义 `qwerty` 层：

```lisp
(deflayer qwerty
  esc   1      2      3      4      5      6      7      8      9      0      -      =      bspc
  tab   q      w      e      r      t      y      u      i      o      p      [      ]      @bsl
  @cap  @met_a @alt_s @sft_d @ctl_f g      h      @ctl_j @sft_k @alt_l @met_; '      ret
  lsft  z      x      c      v      b      n      m      ,      .      /      rsft
  lctl  lmet   lalt                 spc                  ralt   rctl
)
```

以及 `nomods-layer`：

```lisp
(deflayer nomods-layer
  esc  1    2    3    4    5    6    7    8    9    0    -    =    bspc
  tab  q    w    e    r    t    y    u    i    o    p    [    ]    @bsl
  @cap a    s    d    f    g    h    j    k    l    ;    '    ret
  lsft z    x    c    v    b    n    m    ,    .    /    rsft
  lctl lmet lalt           spc            ralt rctl
)
```

我还定义了一个 `layers` 层，用于切换到其他层，并把它映射到 <kbd>\\</kbd> 上：

```lisp
(deflayermap (layers)
  1    @base
  2    @nomods
  3    lrld
)

(defalias
  ;; toggle layer aliases
  bsl (tap-hold-release $tt $ht \ @lay)
  lay (layer-toggle layers)
  ;; change the base layer between base and nomods-layer
  base (layer-switch qwerty)
  nomods (layer-switch nomods-layer)
)
```

通过按住 <kbd>\\</kbd> 键，可以切换到 `layers` 层，然后用 <kbd>1</kbd>、<kbd>2</kbd>、<kbd>3</kbd> 键切换到不同的层。

### 完整配置

至此，我的配置就完成了。完整的配置可以看[这里](https://github.com/Pagliacii/dotfiles/blob/main/kanata/.config/kanata/kanata.kbd)。
