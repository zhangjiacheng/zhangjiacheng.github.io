---
layout: post
title: "正则表达式的使用"
date: 2018-09-24 
description: "Describtion of how to use the regular expression."
tag: Regular Expression
---   

## 正则表达式的作用

正则表达式用来在字符串中查找符合某种匹配模式的字符串，正则表达式被大多主流计算机语言支持。
本文使用javascript和Objective-C来说明使用正则表达式的两个例子。

使用正则表达式最关键的步骤是定义匹配模式的规则字符串，匹配模式分两种：
1. 简单匹配模式
2. 使用特殊字符构造复杂的匹配模式

简单匹配模式很容易，比如你要查找一个字符串中匹配到的"abc"，则匹配模式的规则字符串就使用abc。
但我们一般用得多的还是第二种匹配模式，即：使用特殊字符构造复杂的匹配模式。

## 案例一，查找字符串中包含的有效Email地址

*分析：*

Email地址的匹配规则应该是这样的：
这个字符串由一个'@'符号从中分割，
'@'前半段由有效字符组成，
'@'后半段由有效字符组成（但其中间要有一个'.'分割）。

    /* 
    结合使用特殊字符构造复杂的匹配模式。

    '@'前半段的有效字符我们规定为：大小写字母，下划线，点号，加号的随机组合。
    匹配模式的规则字符串为：
    [a-zA-Z0-9_\-\.\+]+

    其中[]包括起来的是允许的字符，因为 - . + 在正则表达式里都是特殊字符，必须带个\ ,说明不把这三者作为特色字符看待（即：作为普通字符）。
    []后面带的+号，是特殊字符，意思是前面的任意字符出现1次或以上。
    
    根据以上分析，依次类推，得到整个匹配模式的规则字符串为：
    [a-zA-Z0-9_\-\.\+]+@[a-zA-Z0-9_\-\.]+\.[A-Za-z]+
    */

    //--- javascript ---
    
    // 1. use the RegExp construtor of RegExp to create a instance. 
    let  myReg = new RegExp("[a-zA-Z0-9_\\-\\.\\+]+@[a-zA-Z0-9_\\-\\.]+\\.[A-Za-z]+","g");
    // 2. or you can use RegExp literal to create a instance.
    myReg = /[a-zA-Z0-9_\-\.\+]+@[a-zA-Z0-9_\-\.]+\.[A-Za-z]+/g;
    // find the match string array 
    let matchArray = "dajk zhangjiachengwang@163.com afa 547357561@qq.com".match(myReg);
    matchArray.forEach( item => {console.log(item)} );

    //--- Objective-C ---
    NSError *error = nil;
    // String to search
    NSString *str = @"dajk zhangjiachengwang@163.com afa 547357561@qq.com";
    // Regular expression to parse email
    NSString *regexAsString = @"[a-zA-Z0-9_\\-\\.\\+]+@[a-zA-Z0-9_\\-\\.]+\\.[A-Za-z]+";
    // Create instance of NSRegularExpression
    // which is a compiled regular expression pattern
    NSRegularExpression *regex = [NSRegularExpression regularExpressionWithPattern:regexAsString options:0 error:&error];
    // If no errors
    if (!error) {
        // Array of matches of regex in string
        NSArray *results = [regex matchesInString:str options:0 range:NSMakeRange(0, str.length)]; 
        // View results to see range and length of matches
        NSLog(@"results: %@", results);
        int count = 1;
        // For each item found...
        for (NSTextCheckingResult *entry in results) {
            // Get email from the str using range
            NSString *text = [str substringWithRange:entry.range];
            NSLog(@"Result %d: - %@", count++, text);
        }
    }
    else
        NSLog(@"Invalid epxression pattern: %@", error);



## 案例二，查找字符串中外面包裹着{}的字符串

*分析：*

外面包裹着{}的字符串的匹配规则应该是这样的：
这个字符串由一个'{'开始，由一个'}'结束，中间包裹着的是非'{'和'}'任意字符。 

    /* 
    结合使用特殊字符构造复杂的匹配模式。

    经分析得到匹配模式的规则字符串为：
    \{[^{}]+\}

    其中'{' '}'为特殊字符，需要带前缀'\'转义，
    [^{}]+ 代表除'{'和'}'以外的任意字符的组合
    */

    //--- javascript ---
    let myReg1 = new RegExp("\\{[^{}]+\\}","g");
    // or use follw literal create instance of RegExp
    myReg1 = /\{[^{}]+\}/g;
    let toMatchStr = "{localSearch:zhangjiacheng}xx{remoteSearch:xhu}"
    let matchArray = toMatchStr.match(myReg);
    matchArray.forEach( item => {console.log(item)} );

    //--- Objective-C ---
    //跟上例类似。

## 总结

如上所述，使用正则表达式最关键的步骤为使用特殊字符构造匹配模式的规则字符串。
[详细教程见此](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions#special-word-boundary)
*感叹：MDN真是一个伟大的网站*
