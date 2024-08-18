+++
title = '将 CapsLock 映射成 Ctrl 和 Escape'
date = 2024-08-18T11:51:20+08:00
draft = false
toc = true
math = false
tags = ["tips", "vim/neovim", "keybindings", "windows"]
+++

常见的 [QWERTY 键盘](https://en.wikipedia.org/wiki/QWERTY)会将 <kbd>CapsLock</kbd> 键放在 [Home row](https://en.wikipedia.org/wiki/Touch_typing#Home_row)
的最左侧，也是最靠近左手小指的地方，如下图所示：

![QWERTY 键盘的按键排列](https://github.com/user-attachments/assets/03b498d2-6700-4d6a-9268-1502faedb131)

但在日常使用中，<kbd>CapsLock</kbd> 的使用频率很低。让这么一个使用频率低的键占据一个很方便按到的位置（**最靠近左手小指**)，实际上是一种浪费。
你可以想想，如果是 <kbd>Ctrl</kbd> 或者 <kbd>Esc</kbd> 放在 <kbd>CapsLock</kbd> 的位置，是不是日常使用就不需要伸手指去“够”它们？只要挪一下小指就行了。
特别是对于 [Vim](https://www.vim.org/)/[Neovim](https://neovim.io/)/[Emacs](https://www.gnu.org/software/emacs/)
等用户来说，更是如虎添翼。

而且我们还可以做到更灵活的映射，即点按（tap）<kbd>CapsLock</kbd> 时是 <kbd>Esc</kbd>，长按（hold）则是 <kbd>Ctrl</kbd>。
这样在使用 **Vim/Neovim** 的时候会更方便，无论是切换 mode 还是按键序列。

<!--more-->

在 Linux 上可以用 [xcape](https://github.com/alols/xcape)[^1]，而在 macOS 上则可以使用 [karabiner-elements](https://karabiner-elements.pqrs.org/)[^2] 来实现。
在 Windows 上并没有简单易用的方案，只有基于 [AutoHotkey](https://www.autohotkey.com/) 的方案[^3]和这个软件 [dual-key-remap](https://github.com/ililim/dual-key-remap)。
但是我测试下来都不起作用，所以在 Windows 上一直没用上这种灵活的 <kbd>CapsLock</kbd> 映射。

直到最近我发现了一个用 [Rust](https://www.rust-lang.org/) 写的跨平台软件 [kanata](https://github.com/jtroo/kanata)。
接下来我就简单介绍一下如何使用 kanata 来实现 <kbd>CapsLock</kbd> 的灵活映射。

[^1]: https://www.jianshu.com/p/6fdc0e0fb266
[^2]: https://ke-complex-modifications.pqrs.org/#caps_lock_tapped_escape_held_left_control
[^3]: https://gist.github.com/sedm0784/4443120

## 安装

首先需要安装 [kanata](https://github.com/jtroo/kanata)，其实也没有特别复杂的操作，参考 [README](https://github.com/jtroo/kanata/blob/main/README.md) 就行。
可以选择下载预编译好的[包](https://github.com/jtroo/kanata/releases)，也可以通过包管理器进行下载。
我在 Windows 上使用 [Scoop](https://scoop.sh/) 来进行安装，命令如下：

```powershell {linenos=false}
scoop install kanata
```

kanata 默认不会在后台跑，而且它也没有什么托盘图标，需要在命令行里启动：

```powershell {linenos=false}
kanata --cfg kanata.kbd
```

{{< tip >}}
如果需要托盘图标的话，可以使用这个库 [kanata-tray](https://github.com/rszyma/kanata-tray)。
{{< /tip >}}

{{< tip >}}
也可以使用 [v1.7.0-prerelease-1](https://github.com/jtroo/kanata/releases/tag/v1.7.0-prerelease-1) 版本。
内置了 Windows 下的托盘图标，详见这个 [PR](https://github.com/jtroo/kanata/pull/990)。
{{< /tip >}}

如果想让它开机自启的话，那么可以通过在 `%APPDATA%\Microsoft\Windows\Start Menu\Programs\Startup`
目录下创建一个 Shortcut 来实现。在 Target 这一栏填入下面这一行命令：

```powershell {linenos=false}
C:\Windows\System32\conhost.exe --headless <path-to-your-kanata.exe> --cfg <path-to-your-kanata.kbd>
```

## 配置

kanata 的配置使用的是 LISP 的语法来编写的，可以参考这篇 [Guide](https://github.com/jtroo/kanata/blob/main/docs/config.adoc)
和 [cfg_samples](https://github.com/jtroo/kanata/blob/main/cfg_samples)。
另外 kanata 的作者还提供了一个在线的[模拟器](https://jtroo.github.io/)来让你测试配置。

和 LISP 不同的地方是，`;` 不是单行注释符，而是按键 <kbd>;</kbd>，单行注释符是 `;;`。另外块注释则是 `#|` 和 `|#`。

{{< note >}}
kanata 配置语法来自于和它相似的项目 [kmonad](https://github.com/kmonad/kmonad)，这是一个用 [Haskell](https://www.haskell.org/) 写的软件。
{{< /note >}}

简单来说，kanata 的配置需要有且仅有一个
[`defsrc`](https://github.com/jtroo/kanata/blob/main/docs/config.adoc#defsrc)
以及至少一个
[`deflayer`](https://github.com/jtroo/kanata/blob/main/docs/config.adoc#deflayer)。
前者定义了键盘物理按键的位置，后者则定义了物理按键的映射规则，叫做层（layer）。
层可以有多个，默认会激活第一个 `deflayer` 所定义的层。

如果想将一个按键映射到另一个按键，则需要使用 `defalias`。

### defsrc

例如一个 QWERTY 键盘 60 配列的键盘配置如下：

```lisp
;; QWERTY 键盘 60 配列
(defsrc
  grv  1    2    3    4    5    6    7    8    9    0    -    =    bspc
  tab  q    w    e    r    t    y    u    i    o    p    [    ]    \
  caps a    s    d    f    g    h    j    k    l    ;    '    ret
  lsft z    x    c    v    b    n    m    ,    .    /    rsft
  lctl lmet lalt           spc            ralt rmet rctl
)
```

其中 `lmet` 和 `rmet` 分别是左 Meta 键和右 Meta 键，也就是 Windows 键。

如果是键盘上没有的按键，在 `defsrc` 里可以省略。另外如果是键盘自带的特殊键，也可以省略。
比如我使用的的键盘就没有 `rmet`，同时多了两个 <kbd>Fn</kbd> 和 <kbd>Pn</kbd>，我的配置如下：

```lisp
(defsrc
  esc  1    2    3    4    5    6    7    8    9    0    -    =    bspc
  tab  q    w    e    r    t    y    u    i    o    p    [    ]    \
  caps a    s    d    f    g    h    j    k    l    ;    '    ret
  lsft z    x    c    v    b    n    m    ,    .    /    rsft
  lctl lmet lalt           spc            ralt rctl
)
```

### deflayer

例如在 QWERTY 键盘上定义一个 [Dvorak](https://en.wikipedia.org/wiki/Dvorak_keyboard_layout) 布局层：

![Dvorak layout](https://github.com/user-attachments/assets/ff8f84be-5e92-4884-95a2-e3b780736a9e)

```lisp
;; QWERTY 键盘 60 配列
(defsrc
  grv  1    2    3    4    5    6    7    8    9    0    -    =    bspc
  tab  q    w    e    r    t    y    u    i    o    p    [    ]    \
  caps a    s    d    f    g    h    j    k    l    ;    '    ret
  lsft z    x    c    v    b    n    m    ,    .    /    rsft
  lctl lmet lalt           spc            ralt rmet rctl
)

;; Dvorak 布局
(deflayer dvorak
  grv  1    2    3    4    5    6    7    8    9    0    [    ]    bspc
  tab  '    ,    .    p    y    f    g    c    r    l    /    =    \
  caps a    o    e    u    i    d    h    t    n    s    -    ret
  lsft ;    q    j    k    x    b    m    w    v    z    rsft
  lctl lmet lalt           spc            ralt rmet rctl
)
```

### defalias & tap-hold

想要实现按下 <kbd>CapsLock</kbd> 是 <kbd>Esc</kbd>，长按是 <kbd>Ctrl</kbd>
则需要使用 `defalias` 和 `tap-hold` 这个动作来实现。

`defalias` 需要一个
[alias](https://github.com/jtroo/kanata/blob/main/docs/config.adoc#aliases) 和一个
[action](https://github.com/jtroo/kanata/blob/main/docs/config.adoc#actions)，
然后需要在 `deflayer` 里使用 `@alias` 来将这个动作映射到某个按键上面。

`tap-hold` 这个动作接受以下 4 个参数：

1. tap timeout (unit: ms)
2. hold timeout (unit: ms)
3. tap action
4. hold action

`tap-hold` 还有两个变体动作：

1. `tap-hold-press` 或 `tap⬓↓`
2. `tap-hold-release` 或 `tap⬓↑`

这两个变体动作用于让按键响应更加灵敏而不受 hold timeout 的限制。

我们可以使用 `tap-hold-press` 来实现，代码如下：

```lisp
;; tap for escape, hold for left ctrl
(defalias cap (tap-hold-press 200 200 esc lctl))
```

### tap-dance

如果真的需要用上 <kbd>CapsLock</kbd> 的话，可以使用
[`tap-dance`](https://github.com/jtroo/kanata/blob/main/docs/config.adoc#tap-dance)
来将之映射到另一个按键下，短按两下触发。

比如映射到 <kbd>Esc</kbd> 上，代码如下：

```lisp
(defalias
  ;; 1 tap : "Escape"
  ;; 2 tap : "Caps Lock"
  eca (tap-dance 200 (esc caps))
)
```

## kanata.kbd

将以下代码保存为 `.kbd` 文件，就可以让 kanata 加载这份配置文件，实现更灵活的 <kbd>CapsLock</kbd>：

```lisp
;; QWERTY 键盘 60 配列
(defsrc
  grv  1    2    3    4    5    6    7    8    9    0    -    =    bspc
  tab  q    w    e    r    t    y    u    i    o    p    [    ]    \
  caps a    s    d    f    g    h    j    k    l    ;    '    ret
  lsft z    x    c    v    b    n    m    ,    .    /    rsft
  lctl lmet lalt           spc            ralt rmet rctl
)

;; QWERTY 布局 + 灵活的 CapsLock
(deflayer qwerty
  grv  1    2    3    4    5    6    7    8    9    0    -    =    bspc
  tab  q    w    e    r    t    y    u    i    o    p    [    ]    \
  @cap a    s    d    f    g    h    j    k    l    ;    '    ret
  lsft z    x    c    v    b    n    m    ,    .    /    rsft
  lctl lmet lalt           spc            ralt rmet rctl
)

;; 按一下 CapsLock 是 Esc，按住则是左 Ctrl 键
(defalias cap (tap-hold-press 200 200 esc lctl))
```
