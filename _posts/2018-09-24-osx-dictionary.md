---
title: OS X 自带词典功能强化
layout: post
date: '2018-09-24 09:00:00'
tags:
- dictionary
- osx
categories:
- Technique
---

# 如何扩充词库？
## 1. 使用DictUnifier + Stardict等词库数据

下载安装[DictUnifier](https://github.com/jjgod/mac-dictionary-kit)，然后将词典数据拖入即可完成自动安装。词典数据请参考[](http://download.huzheng.org/zh_CN)

比较推荐小学馆的中日日中，以及朗道的英汉汉英。

<!-- more -->
## 2. 使用Mdict转换

参考[Kaihao's Blog](https://kaihao.io/2018/mdict-to-macos-dictionary/)，此处不赘述。具体需要的工具是[pyglossary](https://github.com/ilius/pyglossary)，[python-lzo](https://github.com/jd-boyd/python-lzo)和其他一些包：[lxml beautifulsoup4 html5lib]

他在知乎上的[回答](https://www.zhihu.com/question/21964466)已经帮我们转换好了Macmillan English Dictionary，Oxford Advanced Learner's Dictionary和Longman Dictionary of Contemporary English，这三个.dictionary文件亲测足够用了，拖到`~/Library/Dictionaries`里面试试看吧！

# 生词本功能
可以参考[BetterDictionary](https://github.com/pooriaazimi/BetterDictionary)，但是已经很久没有维护了。

另一种需要自己动动手的方法是利用OS X自己的Automator（自动操作），请参考[郭海旭的博客](https://hectorguo.com/zh/save-words-in-dictionary/)，其中比较亮点的是自动保存到Evernote的功能，操作方法是：

1. 先设置一个变量叫wordName（用来保存选中的单词）
2. 获取释义
3. 获取变量wordName
4. 执行AppleScript（把如下代码拷贝过去，就可以了）：

```zsh
on run {input}
	tell application "Evernote" to activate
	set selectedText to first item of input
	set wordName to second item of input
	tell application "Evernote"
		if (not (notebook named "wordlist" exists)) then
			make notebook with properties {name:"wordlist"}
		end if
		try
			create note title wordName with text selectedText notebook "wordlist"
			synchronize
		end try
	end tell
end run
```

今天要说的是，如何利用Automator将生词格式化保存到一个Markdown文件里面，作为一个单独的page（或者post）呢？这就要把Shell脚本稍微改改了。原先保存为一个html文件的脚本为：

```zsh
# 将单词释义保存到桌面的wordlist.html文件下，若文件不存在，则初始化文件（填充html的标签）
FILE=$HOME/Desktop/wordlist.html
INIT_STRING='<!DOCTYPE html><html lang="en"><head><meta charset="UTF-8"><title>MAC-Wordlist</title></head><body><table></table></body></html>'
# 格式化单词释义（把单词，音标，解释分离开）
RESULT=$(echo -e $1 | sed -E 's#([a-zA-Z]+) (\|[^\|]+\|)(.*)#<tr><td>\1</td><td>\2</td><td>\3</td></tr>#g')
if [ ! -f $FILE ]; then
	echo $INIT_STRING > $FILE
fi
sed -Ei '' "s#(</table>)#$RESULT\1#g" $FILE
```

改成：

```zsh
FILE=$HOME/wittyfilter.github.io/wordlists.md
INIT_STRING='---
layout: page
title: Word Lists
---
'
# 格式化单词释义（把单词，音标，解释分离开）
RESULT=$(echo -e $1 | sed -E 's/([a-zA-Z]+) (\|[^\|]+\|) (.*)/\
### \1\
>\3\
/g')
if [ ! -f $FILE ]; then
	echo $INIT_STRING > $FILE
fi
echo $RESULT >> $FILE
```

完成后的效果如下：

![]({{ site.baseurl }}/public/images/wordlists2.png)

Automator的设置如下：

![]({{ site.baseurl }}/public/images/wordlists1.png)