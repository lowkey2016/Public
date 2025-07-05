# 怎么实现瀑布流效果
通过重写 UICollectionViewLayout 来实现，在 prepare 方法里准备好 UICollectionViewLayoutAttributes 数组，假设有 columns 列，一共有 itemCount 个 cell 要展示，在排列每一个 cell 时，首先获取这个 cell 的高度，然后找出当前高度最小的列，在那一列放一个 cell（计算好对应的 frame），同时更新每一列的高度，循环这个过程，直到所有 cells 排列好。
接下来在下面 2 个方法里返回对应的 layout attributes 就行：
```
func layoutAttributesForElements(in rect: CGRect) -> [UICollectionViewLayoutAttributes]?
func layoutAttributesForItem(at indexPath: IndexPath) -> UICollectionViewLayoutAttributes?
```
UICollectionViewLayoutAttributes 会包括 frame 属性，可以告诉 UIKit 怎么放置这个 cell。
