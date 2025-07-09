# contentInsetAdjustmentBehavior
```
// UIScrollView.h

@property(nonatomic) UIScrollViewContentInsetAdjustmentBehavior contentInsetAdjustmentBehavior API_AVAILABLE(ios(11.0), tvos(11.0)) API_UNAVAILABLE(watchos);

typedef NS_ENUM(NSInteger, UIScrollViewContentInsetAdjustmentBehavior) {
    UIScrollViewContentInsetAdjustmentAutomatic, // Similar to .scrollableAxes, but for backward compatibility will also adjust the top & bottom contentInset when the scroll view is owned by a view controller with automaticallyAdjustsScrollViewInsets = YES inside a navigation controller, regardless of whether the scroll view is scrollable
    UIScrollViewContentInsetAdjustmentScrollableAxes, // Edges for scrollable axes are adjusted (i.e., contentSize.width/height > frame.size.width/height or alwaysBounceHorizontal/Vertical = YES)
    UIScrollViewContentInsetAdjustmentNever, // contentInset is not adjusted
    UIScrollViewContentInsetAdjustmentAlways, // contentInset is always adjusted by the scroll view's safeAreaInsets
} API_AVAILABLE(ios(11.0), tvos(11.0)) API_UNAVAILABLE(watchos);
```

1、为什么要设计这个东西？
当 ScrollView 和系统界面元素（如导航栏、标签栏）重叠时，系统会自动调整 contentInset 以确保内容不被遮挡。contentInsetAdjustmentBehavior 允许你控制这种自动调整的方式，或完全禁用它。
如果你的 scrollView 在没有设置过 contentOffset 的情况下，刚进入的时候变成了 (0, -44), 那大几率就是这个属性导致的。

2、详解每个枚举值
.automatic
- 行为：系统自动调整 contentInset, 使内容避开所有安全区域（如状态栏、导航栏、标签栏）。
- 适用场景：默认值，适用于大多数场景。例如，当导航栏半透明时，系统会自动为滚动视图添加顶部 inset。

.scrollableAxes
- 行为：仅在滚动视图启用了滚动的轴上调整 contentInset:
若垂直可滚动，则调整顶部和底部 inset
若水平可滚动，则调整左侧和右侧 inset
- 适用场景：当你希望仅在实际可滚动的方向上调整 inset 时使用。例如，一个只允许水平滚动的图片浏览器，可能不需要调整垂直方向的 inset

.never
- 行为：不自动调整 contentInset, 保持手动设置的值。
- 适用场景：当你需要完全自定义滚动视图的 inset 时使用。例如，实现沉浸式界面（如全屏视频播放器）时，禁用自动调整以让内容延伸到顶部状态栏下方。

.always
- 行为：根据安全区域（safeAreaInsets）调整 contentInset，但忽略滚动视图是否实际滚动。
- 适用场景：确保内容始终避开安全区域，即使滚动视图不可滚动。例如，一个高度固定的表格视图，仍需要内容避开导航栏。

3、注意事项
当与 `contentInset` 叠加时，系统自动调整的 inset 会跟你手动设置的 contentInset 叠加。例如：
```
scrollView.contentInset = UIEdgeInsets(top: 20, left: 0, bottom: 0, right: 0)
scrollView.contentInsetAdjustmentBehavior = .automatic
// 最终的 inset.top = 20 + 安全区域顶部 inset
```

---

# 怎么监听点击 StatusBar 导致 ScrollView 滑动到顶部？

```
// UIScrollViewDelegate
- (void)scrollViewDidScrollToTop:(UIScrollView *)scrollView;      // called when scrolling animation finished. may be called immediately if already at top
```
