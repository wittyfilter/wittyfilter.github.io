---
layout: post
title: 获取当前Windows序列号
categories: Coding
tags:
  - windows
  - serial number
date: 2014-12-23 00:00:00
---

Code from [http://pastebin.ubuntu.com/8803153/](http://pastebin.ubuntu.com/8803153/)


{% highlight powershell %}
Dim s
s = InputBox("当前Windows系统序列号为：", "Windows序列号", GetWindowsSN)
WScript.Quit


'取得当前Windows序列号函数

Function GetWindowsSN()
    Const HKEY_LOCAL_MACHINE = &H80000002
    strKeyPath = "SOFTWARE\Microsoft\Windows NT\CurrentVersion"
    strValueName = "DigitalProductId"
    strComputer = "."
    Dim iValues()
    Set oReg = GetObject("winmgmts:{impersonationLevel=impersonate}!\\" & strComputer & "\root\default:StdRegProv")
    oReg.GetBinaryValue HKEY_LOCAL_MACHINE, strKeyPath, strValueName, iValues
    Dim arrDPID
    arrDPID = Array()
    For i = 52 To 66
        ReDim Preserve arrDPID( UBound(arrDPID) + 1 )
        arrDPID( UBound(arrDPID) ) = iValues(i)
    Next
    ' <--------------- Create an array to hold the valid characters for a microsoft Product Key -------------------------->
    Dim arrChars
    arrChars = Array("B", "C", "D", "F", "G", "H", "J", "K", "M", "P", "Q", "R", "T", "V", "W", "X", "Y", "2", "3", "4", "6", "7", "8", "9")

    ' <--------------- The clever bit !!! (Decrypt the base24 encoded binary data)-------------------------->
    For i = 24 To 0 Step -1
        k = 0
        For j = 14 To 0 Step -1
            k = k * 256 Xor arrDPID(j)
            arrDPID(j) = Int(k / 24)
            k = k Mod 24
        Next
        strProductKey = arrChars(k) & strProductKey
        ' <------- add the "-" between the groups of 5 Char -------->
        If i Mod 5 = 0 And i <> 0 Then strProductKey = "-" & strProductKey
    Next
    GetWindowsSN = strProductKey
End Function
{% endhighlight %}