---
title: "Handy UIViewController debug display"
date: 2012-12-20
tags: [ "Objective-C" ]
---

When working with complex iOS projects with many screens, sometimes it can be annoying and time consuming to figure out exactly which class to start looking at when you want to make changes to a view controller. You can use this handy UIViewController category extension to display the name of the class on each screen. They are great reference points especially if you are jumping into a new codebase.

	#ifdef DEBUG
	 
	#import "UIViewController+Debug.h"
	#import <objc/runtime.h>
	 
	static char key;
	 
	@implementation UIViewController (Debug)
	 
	+ (void)load
	{
		method_exchangeImplementations(class_getInstanceMethod(self, @selector(viewDidLoad)),
									   class_getInstanceMethod(self, @selector(swizzled_viewDidLoad)));
		 
		method_exchangeImplementations(class_getInstanceMethod(self, @selector(viewWillLayoutSubviews)),
									   class_getInstanceMethod(self, @selector(swizzled_viewWillLayoutSubviews)));
	}
	 
	- (void)swizzled_viewDidLoad {
		[self swizzled_viewDidLoad];
		 
		UILabel* nameLabel = [[self class] createDebugLabel];
		objc_setAssociatedObject(self, &key, nameLabel, OBJC_ASSOCIATION_RETAIN);
		[self.view addSubview:nameLabel];
	}
	 
	- (void)swizzled_viewWillLayoutSubviews {
		[self swizzled_viewWillLayoutSubviews];
		 
		UILabel* nameLabel = objc_getAssociatedObject(self, &key);
		 
		if (nameLabel) {
			[nameLabel.superview bringSubviewToFront:nameLabel];
		}
	}
	 
	+ (UILabel*)createDebugLabel {
		UILabel* nameLabel = [[UILabel alloc] initWithFrame:CGRectZero];
		nameLabel.backgroundColor = [UIColor colorWithRed:0 green:0 blue:0 alpha:0.4f];
		nameLabel.textColor = [UIColor colorWithRed:.1f green:1 blue:.1f alpha:0.7f];
		nameLabel.layer.borderColor = [UIColor colorWithRed:0 green:0 blue:0 alpha:.8f].CGColor;
		nameLabel.layer.borderWidth = 1;
		nameLabel.layer.cornerRadius = 2;
		nameLabel.font = [UIFont systemFontOfSize:10.0f];
		nameLabel.text = [[self class] description];
		[nameLabel sizeToFit];
		nameLabel.top = nameLabel.left = 2;
		nameLabel.width += 4;
		nameLabel.textAlignment = UITextAlignmentCenter;
		 
		return nameLabel;
	}
	 
	@end
	 
	#endif
