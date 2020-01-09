---
title: AsaTung：信息标记的三种形式与信息提取的一般方法概述
date: 2019-07-17 10:00:00
tags: python
---
# Ⅰ.信息的标记

+ 标记后的信息可形成信息组织结构，增加了信息维度
+ 标记的结构与信息一样具有重要价值
+ 标记后的信息可用于通信、存储或展示
+ 标记后的信息更有利于程序理解和应用

&nbsp;&nbsp;&nbsp;HTML通过预定义的<>...</>标签形式组织不同类型的信息
# Ⅱ.信息标记的三种方式
## 1.XML
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`<img src="china.jpg" size="10">...</img>`

&nbsp;&nbsp;以`<img>`开始，以`</img>`结束，`<img>`为标签tag
&nbsp;&nbsp;`img`为名称name，标签中的`src`与`size`为属性attibute

&nbsp;&nbsp;如果是空元素，有以下缩写形式：`<img src="china.jpg" size="10" />`

&nbsp;&nbsp;注释书写形式：`<!--This is a comment, very useful -->`

### XML实例：

```

    <person>
       <firstName>Asa</firstName>
       <lastName>Tung</lastName>
       <college>山西大学</college>
       <address>
          <province>山西省</province>
          <city>太原市</city>
          <streetAddr>小店区坞城路92号</streetAddr>
       </address>
       <prof>计算机科学与技术</prof>
    </person>


```

## 2.JSON
&nbsp;&nbsp;&nbsp;有类型的键值对 `key:value`

如 `"name" : "山西大学"`

`"name" : ["山西大学", "山西大学堂"]` 多值用[,]来组织

```

    "name":{
          "newName" : "山西大学",
          "oldName" : "山西大学堂"
           }

```

键值嵌套用{,}组织

### JSON实例：

```

    {
       "firstName" : "Asa",
       "lastNamw"  : "Tung",
       "college"   : "山西大学"，
       "address"   : {
                        "province"   : "山西省",
                        "city"       : "太原市"，
                        "streetAddr" : "小店区坞城路92号"
                     }，
       "prof"      : "计算机科学与技术"
    }

```

## 3.YAML

无类型键值对 key : value

`name : 山西大学`&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;name为key，仅可为字符串，山西大学为value

缩进表达所属关系：

```

    name :
       oldName : 山西大学堂
       newName : 山西大学

```

用符号'-'来表示并列关系：

```
    name : 
    -山西大学
    -山西大学堂

```

'|'&nbsp;&nbsp;表达整块数据，'#'&nbsp;&nbsp;表示注释：

```
    text : |                   #学校介绍
    
    山西大学文脉可追溯到明代三立书院及清代晋阳书院和令德堂书院，学校前身是清朝光绪二十八年（1902年）

    5月8日由英国人李提摩太和时任山西巡抚岑春煊共同创办的山西大学堂。民国元年（1912年）改名为山西大学
    
    校，1918年确定为国立山西大学，是中国创办最早的三所国立大学之一。1931年改名为山西大学。1953

    年经全国高校院系调整，更名为山西师范学院。1959年恢复山西大学校名，是中国最早在联合国注册的高校之

    一。

```

### YAML实例：

```

    firstName : Asa
    lastName  : Tung
    college   : 山西大学
    address   :
       province   : 山西省
       city       : 太原市
       streetAddr : 小店区坞城路92号
    prof      : 计算机科学与技术

```

# Ⅲ.三种信息标记形式的比较

+ XML
    + 早的通用信息标记语言，可拓展性好，但繁琐
    + Internet上的信息交互与传递
+ JSON
    + 信息有类型，适合程序处理(js)，较XML简洁
    + 移动应用云端的节点的信息通信，无注释
+ YAML
    + 信息无类型，文本信息比例最好，可读性好
    + 各类系统的配置文件，有注释易读

# Ⅳ.信息提取的一般方法

## 1.完整解析信息的标记形式(XML JSON YAML)，再提取关键信息
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;此方法需要标记解析器，例如，python中bs4库的标签树遍历

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;优点：信息解析准确

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;缺点：提取过程繁琐，速度慢

## 2.无视标记形式，直接搜索关键信息(搜索)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;此方法需要对信息的文本查找函数

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;优点：提取过程简洁，速度较快

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;缺点：提取结果准确性与信息内容有关

## 3.融合方法：结合形式解析与搜索方法，提取关键信息(XML JSON YAML 搜索)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;融合方法需要标记解析器及文本查找函数







    


 
   





 