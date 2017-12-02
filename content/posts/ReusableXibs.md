---
title: "Reusable XIB's with dynamic size"
date: 2016-02-11
categories: 
 - "Code"
draft: true
---

Quick reference

## 1: Create your view ##
 - Create XIB file. Add your view to XIB.
 - Create corresponding `UIView` class, and inherit from `UTXibView`
 - Set 'Files owner' in XIB to your custom class name. *Do not* set the class of the view itself.
 - Set outlets etc.

```
@IBDesignable
class UTXibView: UIView {
	
	required init?(coder aDecoder: NSCoder) {
		super.init(coder: aDecoder)
		initializeSubviews()
	}
	
	override init(frame: CGRect) {
		super.init(frame: frame)
		initializeSubviews()
	}
	
	func initializeSubviews() {
		let viewName = String(self.dynamicType)
		let view: UIView = NSBundle.mainBundle().loadNibNamed(
			viewName,
			owner: self,
			options: nil)[0] as! UIView
		self.ut_pinViewToEdges(view)
	}
}
```

## 2: Add your view to storyboard ##
 - Add placeholder view to controller on storyboard, set the class type to the corresponding class
 - Setup constraints. For dynamic width/height, see next step

## 3: Dynamic size ##
 - On storyboard, select your view placeholder. Remove height / width constraint as desired. 
 - In size inspector, change **Intrinsic Size** from `Default (System defined)` to `Placeholder`
 - In your view class, implement `intrinsicContentSize()`
 - Whenever your view changes its own size, have it call `invalidateIntrinsicContentSize()`. This will propagate changes up the view hierarchy and cause the controller's view in your storyboard to relayout automatically.
