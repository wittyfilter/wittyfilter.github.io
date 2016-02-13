---
layout: post
title: 情人节快乐～
tags:
  - valentine
categories:
  - Trivial
date: 2016-02-13 07:59:59
---

<script>
function startTime() {
    var today = new Date();
    var startdt = new Date("2015/09/01 14:30:00");
    var diffdt = today.getTime() - startdt.getTime();

	var days = Math.floor(diffdt/(24*3600*1000));

	var res1 = diffdt%(24*3600*1000);
	var hours = Math.floor(res1/(3600*1000));
	
	var res2 = res1%(3600*1000);
	var minutes = Math.floor(res2/(60*1000));

	var res3 = res2%(60*1000);
	var seconds = Math.round(res3/1000)

    document.getElementById('love').innerHTML =
    days + "天" + hours + "小时" + minutes + "分钟" + seconds + "秒";

    var t = setTimeout(startTime, 500);
}
</script>

![]({{ site.baseurl }}/public/images/ilovezy.jpg)

## 亲爱的赵琰：

### 我们在一起已经
<div id="love" style="color:#ffc0cb;font-weight:bold;font-family: 'PT Sans', fantasy;font-size: 40px"></div>

<script type="text/javascript">window.onload=startTime();</script>

	这是我们的第一个情人节，也是未来无数个情人节的开端。还是那句老话，余生请多指教！

>未来的日子有你才美，梦才会真一点  
我愿意这条情路相守相随  
你最珍贵




