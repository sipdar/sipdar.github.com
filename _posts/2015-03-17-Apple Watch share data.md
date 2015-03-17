---
layout: post
title: Apple Watch share data
---

## 使用 `NSUserDefaults` 分享数据
首先我们要在创建的工程中打开应用和 WatchKit Extension 的 `APP Groups` 并且应用和 WatchKit Extension 要添加在同一个 **group** 下面。
![](/image/2015/03/17/1.png)
然后我们创建一个数据对象，功能很简单 从 `NSUserDefaults` 中读取 String 数组 ，然后在对数组的添加删除操作后 更新 `NSUserDefaults` 对象

{% highlight objc linenos %}

-(void)loadItems 
	_defaults =[[NSUserDefaults alloc] initWithSuiteName:@"group.com.test.DataSharing"];
	_items = []_defaults objectForKey:@"Items"];
	if (!_items) 
		_items =[NSMutableArray array];
	}
}

-(void)appendWith:(NSString *)string
	[_items addObject:string];
	[self save];
}

-(void)removeItemAt:(NSInteger)index
	[_items removeObjectAtIndex:index];
	[self save];
}

-(void)save 
	[_defaults setObject:_items forKey:@"Items"];
	[self.delege dataModelDidChanged];
}

{% endhighlight %}

在 WatchKit Extension 中 我们就可以使用同一个数据对象来更新 Watch app 的UI
{% highlight objc linenos %}
-(void)loadTable 
	[self.tableView setNumberOfRows:[_dataModel.items count] withRowType:@"ItemCell"];
	[_dataModel.items enumerateObjectsUsingBlock:^(NSString *obj, NSUInteger idx, BOOL *stop)
		WKRowItem *rowItem = [self.tableView rowControllerAtIndex:idx];
		[rowItem.label setText:obj];
	}];
}
{% endhighlight %}

 
## Watch App 唤醒应用
因为我们在 Watch App 中也可以输入文本。所以在手表上更新数据后 我们还以通知 应用来使用新数据刷新UI。在 `WKInterfaceController` 有提供API 给我们
{% highlight objc linenos %}
 
+(BOOL)openParentApplication:(NSDictionary *)userInfo 
					   reply:(void(^)(NSDictionary *replyInfo, NSError *error)) reply;

{% endhighlight %}


{% highlight objc linenos %}

[self presentTextInputControllerWithSuggestions:@[@"suggestion",@"Test",@"Input"]
							    allowedInputMode:WKTextInputModeAllowAnimatedEmoji
									  completion:^(NSArray *results) 
								 __strong typeof(weakSelf) strongSelf = weakSelf;
								 if (strongSelf) 
									 [_dataModel appendWith:results.firstObject];
									 [strongSelf loadTable];
									 [WKInterfaceController openParentApplication:@{@"update":@"update"} 
									 			                            reply:^(NSDictionary *replyInfo, NSError *error)}];
 								}
}];

{% endhighlight %}

![](/image/2015/03/17/2.gif)

[Demo](https://github.com/sipdar/Watch-App-Share-data)
   