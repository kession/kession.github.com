---
layout: post
title: xselect
categories:
- ued
tags:
- iOS
- Objc
---

## xselect
***
### 1. 基本用法  [详细点击](http://setao.alibaba-inc.com/lib/lib/120.htm)
其中*base* 为xselect父节点div ID，__注意 `#`__ ， *id*设置xselect的id，__无需 `#`__.

	var selectDate =  [{label:'全部',value:'-1'},	{label:'新签',value:'1'},{label:'续签',value:'2'};
	var sel = new Lite.ui.Xselect({
		base : '#bp-tradetype',  
		data : selectDate,
		key: {
			   label: 'label',
			   value: 'value'
		},
		id : 'bss-select-salesorg-ID',
		name : '_tradeType',
		css : { width : 70 },
		onChange:function(value){
		 	// onChange Event do something
		}
	});
###2. 取值
	// 取得选择项的 value 值
	var v = $("#bss-select-salesorg-ID").val();
	// 取得选择项的 label 值
	var l = $("li.lt-menuitem[rel="+ salesOrgID +"]").html();
###3. json传值初始化
	var sel = new Lite.ui.Xselect({
		base : '#selectSalesName',  
		key: {
			   label: 'salesOrgName',
			   value: 'salesOrgID'
		},
		id : 'bss-select-salesorg-ID',
	});
	s1.init(selectDate);
###4. 默认值
	// 设置默认值
	var selectDate = [{salesOrgName:'全部',salesOrgID:'-1'}];
	selectDate = selectDate.concat(res.d.salesOrgList);
	s1.init(selectDate);
	// 默认选择
	s1.value('-1');




