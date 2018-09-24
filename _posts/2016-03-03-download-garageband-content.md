---
title: GarageBand下载基本音乐、声效库缓慢的解决办法
layout: post
tags:
- garageband
categories:
- Music
date: '2016-03-04 01:54:44 +0800'
---

lsof查下载的文件：

```zsh
ps aux | grep GarageBand  
lsof -g 291 > ~/log.txt
```

在我的电脑上下载后所保存的临时目录为：

`/private/var/folders/数字/.../C/com.apple.garageband10/com.apple.MusicApps/audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_GarageBandCoreContent.pkg`

去下面这里下载（除了第一个，下面都是可选）：
<!-- more -->
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_GarageBandCoreContent.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_GarageBandPremiumContent.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_GB_StereoDrumKitsSongWriter.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_GB_StereoDrumKitsAlternative.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_GB_StereoDrumKitsRock.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_GB_StereoDrumKitsRnB.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MGBContentCompatibility.pkg

直接双击安装就成，之前把~/Library和/Library中跟GarageBand相关的东西都删除（别顺手把主文件删除）。

特别注意~/Library/Containers中有GarageBand的文件夹缓存一定要删除，删除完最后重启，然后安装下载的这个音效包，然后再启动GarageBand就不会再要求下载了。

2015:

http://audiocontentdownload.apple.com/lp10_ms3_content_2015/MAContent10_GarageBandCoreContent2.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2015/MAContent10_GarageBandCoreContent_v3.pkg

如果安装之后还不生效的话，那就用cleanmymac重置一下GarageBand，然后再安装就可以了。



附：Logic Pro的内容

2G Essential包：

http://audiocontentdownload.apple.com/lp10_ms3_content_2013/ProAudioCoreContent10.pkg

新贝司

http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_InstrumentsBass.pkg

新Drum Kits

http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_StereoDrumKitsAlternative.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_StereoDrumKitsSongwriter.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_StereoDrumKitsRock.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_StereoDrumKitsRnB.pkg  

新的Producer Drum Kits

http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_ProducerClassicSixtiesKit.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_ProducerPawnShopKit.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_ProducerBirchKit.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_ProducerSeventiesPlexiKit.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_ProducerTightMapleKit.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_ProducerModernMapleKit.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_ProducerStudioKit.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_ProducerPatches.pkg

新鼓机

http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_ElectronicDrumKits.pkg

新吉他

http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_InstrumentsGuitar.pkg

新击槌
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_InstrumentsMallet.pkg

新交响乐器

http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_InstrumentsOrchestralBrass.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_InstrumentsOrchestralChoir.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_InstrumentsOrchestralHarp.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_InstrumentsOrchestralKeyboard.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_InstrumentsOrchestralPercussion.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_InstrumentsOrchestralPipeOrgan.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_InstrumentsOrchestralStrings.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_InstrumentsOrchestralWoodwinds.pkg 

新的钢琴

http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_InstrumentsPiano.pkg

新的世界音乐

http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_InstrumentsWorldKeyboards.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_InstrumentsWorldPercussion.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_InstrumentsWorldStringed.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_InstrumentsWorldVoice.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_InstrumentsWorldWoodwind.pkg 

新的Apple Loop

http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_AppleLoopsElectroHouse.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_AppleLoopsHipHop.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_AppleLoopsModernRnB.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_AppleLoopsTechHouse.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_AppleLoopsDubstep.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_AppleLoopsChillwave.pkg  
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_AppleLoopsDeepHouse.pkg 

新混响－立体声
http://audiocontentdownload.apple.com/lp10_ms3_content_2013/MAContent10_IRsStereo.pkg