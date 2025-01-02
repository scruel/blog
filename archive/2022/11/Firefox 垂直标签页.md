---
tags:
  - firefox
  - sidebery
  - edge
  - vertical-toolbar
  - tabs
  - css
---

文本通过一些配置，在 Firefox 上增加了类似 MS Edge 的垂直标签页。树状标签页扩展用的是 Sidebery，外加一些自定义浏览器样式后，可实现如下效果：

![](images/tabs.gif)

## 扩展安装

Firefox 扩展商店里的树状标签页扩展不止一个，本人在简单比较各个插件的优劣后，选择了个人体感最好的 [Sidebery](https://addons.mozilla.org/en-US/firefox/addon/sidebery)。

安装插件后，Firefox 左侧应该就会显示树状标签页了，但交互体验等方面，相比较 Edge 是有差距的。

**下面会介绍我怎么把他打造成演示的样子。**

## 浏览器配置

为了实现目的，我们先来作一些准备工作。

1. 为了后续能美观又好用，我们先让标题栏显示出来。

右键 Firefox 的地址栏的空闲处，然后点击 **定制工具栏（Customize Toolbar）**：

![](images/customize_toolbar.jpg)

找到并勾选左下角的 **标题栏（Title Bar）** 后，点击保存即可。


2. 下一步我们需要自定义 Firefox 的样式，在此之前需要打开一个配置项。
在地址栏中输入 `about:config` 并回车访问（当作是个网址），遇到警告页面的话就点“接受风险并继续”，然后在顶部输入 `toolkit.legacyUserProfileCustomizations.stylesheets` 以搜索，最后双击列出来的选项，确保其值 `true` 即可。
![image](https://github.com/user-attachments/assets/54e5aa5a-ef98-4c50-8fab-ec945b83cf13)


3. 允许自定义样式后，我们先创建一个空的自定义样式文件。
Window + R 打开运行框，输入 `%appdata%\Mozilla\Firefox\Profiles` 以打开 Firefox 存放 Profiles 的目录， 找到并进入到你的浏览器 profile 文件夹（文件夹名称形如 `xxx.default-release`）。

> 如果只装了一个 Firefox 版本，且没有自己新建 Profile，那 Profiles 目录下应该只有一个文件夹，如果不止一个，你可能需要先确定一下哪个文件夹是当前 Firefox 所用的。

在这个 `xxx.default-release` 文件夹下，新建 `chrome` 文件夹，然后新建一个名为 `userChrome.css` 的文件，马上会编辑到。



## 自动隐藏原生标签栏

在完成先前的步骤后，树状标签页和横向标签页栏会同时显示，整个窗口功能冗余且略显臃肿，这个问题可通过下面的操作来解决~

首先来处理一下 Sidebery 的扩展图标，如果还没有显示出来的话，可以点击地址栏右侧的扩展图标，然后右击 Sidebery 扩展，再点击 **Pin to Toolbar** 即可：

![image](https://github.com/user-attachments/assets/28243190-08ac-4dcb-afcb-601ba650297a)

单击这个扩展图标可以显示/隐藏垂直标签页，个人没有隐藏树状标签页的需求，所以扩展按钮在其显示的时候是冗余的，为了让其在树状标签页显示的时候能够“临时隐藏”，我们可以通过 **定制工具栏（Customize Toolbar）** ，将它拖入原生的横向标签页栏中（马上就会实现当树状标签页显示时隐藏横向标签页栏），放到左边还是右边，则主要看自己的个人习惯：

![img](images/toolbar_icon.png)

> 不将扩展图标显示出来的话以后会比较麻烦，因为树状标签页面板存在于窗口的侧边栏中，且原生的历史记录面板等也是，所以当你打开历史记录之类的其他面板时，树状标签页面板就会被替换掉，这时候如果有扩展图标，那么点一下它即可恢复树状标签页的显示，而如果没有的话则会需要先点击地址栏的扩展 -> 找到 Sidebery -> 点击，才能再次调出它，可见操作步骤会多出不少。


接下来便开始隐藏原生的横向标签页栏，打开 Sidebery 扩展的设置 -> 通用，找到“前言值设置（Preface Value）”，并把对应的“向窗口标题添加前言值”功能启用，然后在输入框里面输入 `---`，你会发现左上角的标题栏中的标题左边出现了 `---`。

然后编辑 `userChrome.css` 文件，添加如下的 CSS 样式代码并保存：

```css
#main-window[titlepreface="---"] #TabsToolbar {
  display: none;
}
```

> 在启用“添加前言值”功能后，扩展就会增加 `titlepreface` 属性，内容就是刚才输入框里输入的 `---`，所以就可以通过使用 CSS 选择器，来进行当前是否打开了树状标签页。

重启浏览器，这时候你会发现，只要树状标签页开着，原生的横向标签栏就不会显示了。



如果你觉得标题栏中的标题前会多字符这事儿不美观，也可以把它替换成一串不可见的字符（直接复制粘贴即可）：

```
‌‌‌​‌‌​‍‌‌​‌​‍‌‌‌​‌​‌‍‌​‍‌‍‍
```

再次编辑 `userChrome.css` 文件，替换其中的内容为：

```css
#main-window[titlepreface="‌‌‌​‌‌​‍‌‌​‌​‍‌‌‌​‌​‌‍‌​‍‌‍‍"] #TabsToolbar {
  display: none;
}
```

再次“保存-重启”，检查一下效果吧~


## 自动收缩标签页

Edge 的垂直标签页有一个收缩的效果，下面我们也来整一下。

仍然是编辑 `userChrome.css` 文件，向其末尾添加 CSS 代码：

```css
#main-window[titlepreface="‌‌‌​‌‌​‍‌‌​‌​‍‌‌‌​‌​‌‍‌​‍‌‍‍"] #sidebar-box {
    --thin-tab-width: 180px;
    position: relative !important;
    transition: all 100ms !important;
    width: var(--thin-tab-width) !important;
}

#main-window[titlepreface="‌‌‌​‌‌​‍‌‌​‌​‍‌‌‌​‌​‌‍‌​‍‌‍‍"] #sidebar-box:hover {
    --wide-tab-width: 360px;
    transition: all 150ms !important;
    width: var(--wide-tab-width) !important;
    margin-right: calc((var(--wide-tab-width) - var(--thin-tab-width)) * -1) !important;
    z-index: 1;
}
```

这上面主要设置了默认的宽度，以及当鼠标移上去时增加宽度的效果，你可以根据需要修改数值。

重启浏览器后就能实现效果的，但你很快会发现扩展的关闭按钮有点突兀，这里可以再次编辑 `userChrome.css` 文件，向其末尾添加 CSS 代码：

```css
#main-window[titlepreface="‌‌‌​‌‌​‍‌‌​‌​‍‌‌‌​‌​‌‍‌​‍‌‍‍"] #sidebar-box #sidebar-close{
    display:none;
}

#main-window[titlepreface="‌‌‌​‌‌​‍‌‌​‌​‍‌‌‌​‌​‌‍‌​‍‌‍‍"] #sidebar-box:hover #sidebar-close{
    display:unset;
}
```

最后处理一下特殊的情况，比如进入全屏后就不要显示了：

```css
#main-window[inFullscreen] #sidebar-box,
#main-window[inFullscreen] #sidebar-splitter {
  display: none !important;
  width: 0px !important;
}

#main-window[titlepreface="[1] "] #sidebar-box {
  visibility: collapse !important;
}
```


（文末总结会放出完整的 `userChrome.css` 文件内容）

## 配置扩展的树状面板

（可选）如果你觉得上面的配置好后，用起来已经很满意了，那就忽略这节即可，当然我还是推荐做一下，可以使得你的体验更佳。

打开 Sidebery 的设置：

1. 菜单（Menu） -> 导航栏（Navigation bar） -> 布局（Layout），选择垂直排列（vertical）。

2. 样式编辑器（Style editor），左上角选择 SideBar（默认的），右侧面板中输入：

   ```css
   #root .NavigationBar {
     display: none;
   }
   
   #root:hover .NavigationBar {
     display: flex;
   }
   
   #root {
     --name-font-size: 12px;
     --count-font-size: 10px;
   }
   
   .NavigationBar .nav-item {
     display: flex;
     flex-direction: column-reverse;
     padding: 6px 0;
     height: auto;
   }
   
   .NavigationBar .main-items .nav-item[data-type="add_tp"] {
     height: var(--nav-btn-width);
   }
   
   .NavigationBar .main-items .nav-item[data-type="settings"] {
     height: var(--nav-btn-width);
   }
   
   .NavigationBar .nav-item:not([data-type="add_tp"]):not([data-type="settings"]) .name {
     position: relative;
     display: block;
     padding: 0;
     margin: 0 2px 0 0;
     font-size: var(--name-font-size);
     color: var(--color, var(--nav-btn-fg));
     writing-mode: sideways-lr;
     text-orientation: mixed;
   }
   
   .NavigationBar .nav-item .update-badge {
     top: 1px;
     left: 1px;
   }
   
   .NavigationBar .nav-item .badge,
   .NavigationBar .nav-item .progress-spinner {
     display: none;
   }
   
   
   .NavigationBar .hidden-panels-popup-layer .nav-item div.name-box {
     display: none;
   }
   ```

至此，就能有一个比较舒服的垂直面板标签的效果了。

![](images/vertical.jpg)



## 扩展推荐配置

启用先进的双击标签页关闭功能：

鼠标 -> 标签页操作 -> 双击标签页 -> 选择关闭标签页。



## 总结

 `userChrome.css` 文件：

```css
/* Hide the native toolbar */
#main-window[titlepreface="‌‌‌​‌‌​‍‌‌​‌​‍‌‌‌​‌​‌‍‌​‍‌‍‍"] #TabsToolbar {
  display: none;
}

/* Shrink sidebar until hovered. */
#main-window[titlepreface="‌‌‌​‌‌​‍‌‌​‌​‍‌‌‌​‌​‌‍‌​‍‌‍‍"] #sidebar-box {
    --thin-tab-width: 180px;
    position: relative !important;
    transition: all 100ms !important;
    width: var(--thin-tab-width) !important;
}

#main-window[titlepreface="‌‌‌​‌‌​‍‌‌​‌​‍‌‌‌​‌​‌‍‌​‍‌‍‍"] #sidebar-box:hover {
    --wide-tab-width: 360px;
    transition: all 150ms !important;
    width: var(--wide-tab-width) !important;
    margin-right: calc((var(--wide-tab-width) - var(--thin-tab-width)) * -1) !important;
    z-index: 1;
}

#main-window[titlepreface="‌‌‌​‌‌​‍‌‌​‌​‍‌‌‌​‌​‌‍‌​‍‌‍‍"] #sidebar-box #sidebar-close{
    display:none;
}

#main-window[titlepreface="‌‌‌​‌‌​‍‌‌​‌​‍‌‌‌​‌​‌‍‌​‍‌‍‍"] #sidebar-box:hover #sidebar-close{
    display:unset;
}

#main-window[inFullscreen] #sidebar-box,
#main-window[inFullscreen] #sidebar-splitter {
  display: none !important;
  width: 0px !important;
}

#main-window[titlepreface="[1] "] #sidebar-box {
  visibility: collapse !important;
}
```



Sidebery 导入配置：

```json
{"settings":{"nativeScrollbars":false,"nativeScrollbarsThin":true,"nativeScrollbarsLeft":false,"selWinScreenshots":false,"updateSidebarTitle":true,"markWindow":true,"markWindowPreface":"‌‌‌​‌‌​‍‌‌​‌​‍‌‌‌​‌​‌‍‌​‍‌‍‍","ctxMenuNative":false,"ctxMenuRenderInact":true,"ctxMenuRenderIcons":true,"ctxMenuIgnoreContainers":"","navBarLayout":"vertical","navBarInline":false,"navBarSide":"left","hideAddBtn":false,"hideSettingsBtn":false,"navBtnCount":true,"hideEmptyPanels":true,"navActTabsPanelLeftClickAction":"none","navActBookmarksPanelLeftClickAction":"none","navTabsPanelMidClickAction":"none","navBookmarksPanelMidClickAction":"none","navSwitchPanelsWheel":true,"groupLayout":"grid","skipEmptyPanels":false,"dndTabAct":true,"dndTabActDelay":750,"dndTabActMod":"none","dndExp":"pointer","dndExpDelay":0,"dndExpMod":"none","dndOutside":"win","searchBarMode":"dynamic","warnOnMultiTabClose":"collapsed","activateLastTabOnPanelSwitching":true,"activateLastTabOnPanelSwitchingLoadedOnly":true,"tabRmBtn":"hover","hideInact":false,"activateAfterClosing":"next","activateAfterClosingStayInPanel":false,"activateAfterClosingGlobal":false,"activateAfterClosingNoFolded":true,"activateAfterClosingNoDiscarded":true,"askNewBookmarkPlace":false,"tabsRmUndoNote":true,"nativeHighlight":false,"tabsUnreadMark":false,"tabsUpdateMark":"all","tabsUpdateMarkFirst":true,"tabsReloadLimit":5,"tabsReloadLimitNotif":true,"showNewTabBtns":true,"newTabBarPosition":"after_tabs","tabsPanelSwitchActMove":false,"tabsUrlInTooltip":"full","openSubPanelOnMouseHover":false,"selectActiveTabFirst":true,"moveNewTabPin":"start","moveNewTabParent":"last_child","moveNewTabParentActPanel":false,"moveNewTab":"end","moveNewTabActivePin":"start","pinnedTabsPosition":"panel","pinnedTabsList":false,"pinnedAutoGroup":false,"tabsTree":true,"groupOnOpen":true,"tabsTreeLimit":"none","hideFoldedTabs":false,"hideFoldedParent":"none","autoFoldTabs":false,"autoFoldTabsExcept":"none","autoExpandTabs":false,"rmChildTabs":"none","tabsChildCount":true,"tabsLvlDots":true,"discardFolded":false,"discardFoldedDelay":0,"discardFoldedDelayUnit":"sec","tabsTreeBookmarks":true,"treeRmOutdent":"branch","colorizeTabs":false,"colorizeTabsSrc":"domain","colorizeTabsBranches":false,"colorizeTabsBranchesSrc":"url","inheritCustomColor":true,"warnOnMultiBookmarkDelete":"collapsed","autoCloseBookmarks":false,"autoRemoveOther":false,"highlightOpenBookmarks":false,"activateOpenBookmarkTab":false,"showBookmarkLen":false,"bookmarksRmUndoNote":true,"loadBookmarksOnDemand":true,"pinOpenedBookmarksFolder":true,"loadHistoryOnDemand":true,"fontSize":"m","animations":true,"animationSpeed":"fast","theme":"proton","density":"default","colorScheme":"ff","sidebarCSS":true,"groupCSS":false,"snapNotify":true,"snapExcludePrivate":false,"snapInterval":0,"snapIntervalUnit":"min","snapLimit":0,"snapLimitUnit":"snap","hScrollAction":"none","navSwitchPanelsDelay":128,"scrollThroughTabs":"none","scrollThroughVisibleTabs":false,"scrollThroughTabsSkipDiscarded":false,"scrollThroughTabsExceptOverflow":true,"scrollThroughTabsCyclic":false,"scrollThroughTabsScrollArea":0,"autoMenuMultiSel":true,"multipleMiddleClose":false,"longClickDelay":500,"wheelThreshold":false,"wheelThresholdX":10,"wheelThresholdY":60,"tabDoubleClick":"close","tabsSecondClickActPrev":false,"tabsSecondClickActPrevPanelOnly":false,"shiftSelAct":true,"activateOnMouseUp":true,"tabLongLeftClick":"none","tabLongRightClick":"none","tabCloseMiddleClick":"discard","tabsPanelLeftClickAction":"none","tabsPanelDoubleClickAction":"tab","tabsPanelRightClickAction":"menu","tabsPanelMiddleClickAction":"tab","newTabMiddleClickAction":"new_child","bookmarksLeftClickAction":"open_in_act","bookmarksLeftClickActivate":false,"bookmarksLeftClickPos":"default","bookmarksMidClickAction":"open_in_new","bookmarksMidClickActivate":false,"bookmarksMidClickPos":"default","syncName":"","syncSaveSettings":true,"syncSaveCtxMenu":true,"syncSaveStyles":true,"syncSaveKeybindings":false},"sidebarCSS":"#root .NavigationBar {\n  display: none;\n}\n\n#root:hover .NavigationBar {\n  display: flex;\n}\n\n#root {\n  --name-font-size: 12px;\n  --count-font-size: 10px;\n}\n\n.NavigationBar .nav-item {\n  display: flex;\n  flex-direction: column-reverse;\n  padding: 6px 0;\n  height: auto;\n}\n\n.NavigationBar .main-items .nav-item[data-type=\"add_tp\"] {\n  height: var(--nav-btn-width);\n}\n\n.NavigationBar .main-items .nav-item[data-type=\"settings\"] {\n  height: var(--nav-btn-width);\n}\n\n/* \n.NavigationBar .main-items .nav-item:not([data-type=\"add_tp\"]):not([data-type=\"settings\"]) > svg,\n.NavigationBar .hidden-bar .nav-item > svg,\n.NavigationBar .main-items .nav-item > img {\n  display: none;\n} */\n\n.NavigationBar .nav-item:not([data-type=\"add_tp\"]):not([data-type=\"settings\"]) .name {\n  position: relative;\n  display: block;\n  padding: 0;\n  margin: 0 2px 0 0;\n  font-size: var(--name-font-size);\n  color: var(--color, var(--nav-btn-fg));\n  writing-mode: sideways-lr;\n  text-orientation: mixed;\n}\n\n.NavigationBar .nav-item .update-badge {\n  top: 1px;\n  left: 1px;\n}\n\n.NavigationBar .nav-item .badge,\n.NavigationBar .nav-item .progress-spinner {\n  display: none;\n}\n\n\n.NavigationBar .hidden-panels-popup-layer .nav-item div.name-box {\n  display: none;\n}","groupCSS":"","ver":"5.0.0b31"}
```



**Have fun！**
