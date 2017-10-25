---
title: UITabBarItem添加动画的一种方法
date: 2017-10-24 21:43:30
tags:
 - iOS
---

最近项目考虑到可能对APP的TabBarItem添加动画，使得界面更多的展现出高大上的感觉/(ㄒoㄒ)/~~ 虽然我感觉然并卵。不过我还是想办法尝试一下，如何给TabBarItem添加动画。
首先来看一下效果：
![](/assets/blog/tabbar_animate1.gif)
接下来说一下实现方法。

### 1.自定义UITabBarController
APP的根控制器是自定义的TabBarController，继承自UITabBarController。接着在自定义的TabBarController中重写选中tabbarItem的方法。
```OC
- (void)tabBar:(UITabBar *)tabBar didSelectItem:(UITabBarItem *)item {

}
```
### 2.选中item时，找到添加动画的控件
如果直接对 tabbarItem 添加动画，并不方便，实际上要做的是，找到方便添加动画的控件 **UITabBarButton**。

//重写 didSelectItem 方法
- (void)tabBar:(UITabBar *)tabBar didSelectItem:(UITabBarItem *)item {

NSInteger index = [self.tabBar.items indexOfObject:item];

if (self.indexFlag != index) {
[self animationWithIndex:index];
}
}


// 动画
- (void)animationWithIndex:(NSInteger) index {

NSMutableArray *tabbarbuttonArray = [NSMutableArray array];
//找到所有的 UITabBarButton
for (UIView *tabBarButton in self.tabBar.subviews) {

if ([tabBarButton isKindOfClass:NSClassFromString(@"UITabBarButton")]) {

[tabbarbuttonArray addObject:tabBarButton];
}
}

//动画组
CAAnimationGroup *group = [CAAnimationGroup animation];
group.animations = @[[self scaleAnimation], [self rotationAnimation]];
group.duration = 0.15;

UIView *tabbarBtn = tabbarbuttonArray[index];
//找到UITabBarButton中的imageView，加动画
for (UIView *sub in tabbarBtn.subviews) {

if ([sub isKindOfClass:NSClassFromString(@"UITabBarSwappableImageView")]) {

[sub.layer addAnimation:group forKey:nil];
//音效
AudioServicesPlaySystemSound(1109);
}
}

self.indexFlag = index;
}

///大小的动画
- (CABasicAnimation *)scaleAnimation {

CABasicAnimation *scaleAni = [CABasicAnimation animationWithKeyPath:@"transform.scale"];
scaleAni.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseOut];
scaleAni.repeatCount = 1;
scaleAni.autoreverses = YES;
scaleAni.fromValue = [NSNumber numberWithFloat:1];
scaleAni.toValue = [NSNumber numberWithFloat:0.5];

return scaleAni;
}

///旋转的动画
- (CABasicAnimation *)rotationAnimation {

CABasicAnimation *rotateAni = [CABasicAnimation animationWithKeyPath:@"transform.rotation.y"];
rotateAni.fromValue = @(0);
rotateAni.toValue = @(M_PI);
rotateAni.repeatCount = 1;

return rotateAni;
}
