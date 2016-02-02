<properties
    pageTitle="在 Azure Active Directory 中撰寫屬性對應的運算式 | Microsoft Azure"
    description="了解在 Azure Active Directory 中自動化佈建 SaaS 應用程式物件的期間，如何使用運算式對應將屬性值轉換成可接受的格式。"
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2015"
    ms.author="markusvi"/>



# 在 Azure Active Directory 中撰寫屬性對應的運算式

當您設定佈建到 SaaS 應用程式時，您可以指定的其中一種屬性對應類型是運算式對應。 
 您必須撰寫類似指令碼的運算式，以便讓您將使用者的資料轉換成 SaaS 應用程式更能接受的格式。





## 語法概觀

屬性對應的運算式語法是 Visual Basic for Applications (VBA) 函式。

- 
*

- 您可以在函式內互相巢狀函式。

- 您可以將三種不同類型的引數傳入函式：

   1. 屬性，必須以方括弧括住。 例如：[attributeName]

   2. 字串常數，必須以雙引號括住。 例如："United States"

   3. 其他函式。

- 對於字串常數，如果您在字串中需要反斜線 ( \ ) 或引號 ( " ) ，則必須使用反斜線 ( \ ) 符號逸出。



## 函式的清單







----------

### Append

 


 




| 名稱| 必要 / 重複| 型別| 注意事項|
|--- | ---                 | ---  | ---   |
| **source**| 必要| String| 通常為 source 物件的屬性名稱|
| **suffix**| 必要| String| 您想要附加至 source 值結尾的字串。|


----------

### FormatDateTime

 


 




| 名稱| 必要 / 重複| 型別| 注意事項|
|--- | ---                 | ---  | ---   |
| **source**| 必要| String| 通常為 source 物件的屬性名稱。|
| **inputFormat**| 必要| String| source 值的預期格式。|
| **outputFormat**| 必要| String| 輸出日期的格式。|



----------

### Join

 


 


如果其中一個 source 值是多重值屬性，則該屬性中的每個值會結合在一起，並以分隔符號值分隔。




| 名稱| 必要 / 重複| 型別| 注意事項|
|--- | ---                 | ---  | ---   |
| **separator**| 必要| String| 用來分隔串連成一個字串的 source 值的字串。如果不需要分隔符號，可以是 ""。|
| | | String| |



----------

### Mid

 


 
子字串是只包含 source 字串某些字元的字串。




| 名稱| 必要 / 重複| 型別| 注意事項|
|--- | ---                 | ---  | ---   |
| **source**| 必要| String| 通常為屬性的名稱。|
| **start**| 必要| integer| 子字串在 **source** 字串中起始位置的索引。字串第一個字元的索引為 1，第二個字元的索引為 2，依此類推。|
| **length**| 必要| integer| 子字串的長度。如果長度超出 **source** 字串結尾，函式會傳回從 **start** 索引一直到 **source** 字串結尾的子字串。|




----------

### Not

 


 
如果 **source** 值為 "*True*"，則傳回 "*False*"。 反之則傳回 "*True*"。




| 名稱| 必要 / 重複| 型別| 注意事項|
|--- | ---                 | ---  | ---   |
| **source**| 必要| Boolean String| 預期的 **source** 值為 "True" 或 "False"。|



----------

### 將

 



取代字串內的值。 根據提供的參數而以不同的方式運作：

- 提供 **oldValue** 和 **replacementValue** 時：

   - 

- 提供 **oldValue** 和 **template** 時：

   - 以 **source** 值取代 **template** 中所有的 **oldValue** 項目

- 提供 **oldValueRegexPattern**、**oldValueRegexGroupName**、**replacementValue** 時：

   - 以 replacementValue 取代 source 字串中符合 oldValueRegexPattern 的所有值

- 提供 **oldValueRegexPattern**、**oldValueRegexGroupName**、**replacementPropertyName** 時：

   - 如果 **source** 有值，則傳回 **source**

   - 如果 **source** 沒有值，則使用 **oldValueRegexPattern** 和 **oldValueRegexGroupName** 從有 **replacementPropertyName** 的屬性擷取取代值。 結果會傳回取代值




| 名稱| 必要 / 重複| 型別| 注意事項|
|--- | ---                 | ---  | ---   |
| **source**| 必要| String| 通常為 source 物件的屬性名稱。|
| **oldValue**| 選用| String| 在 **source** 或 **templet** 中要被取代的值。|
| **regexPattern**| 選用| String| 在 **source** 中要被取代的值的規則運算式模式。或者，如果使用了 replacementPropertyName，要從取代屬性擷取值的模式。|
| **regexGroupName**| 選用| String| **regexPattern** 內的群組名稱。|
| **replacementValue**| 選用| String| 要取代舊值的新值。|
| **replacementAttributeName**| 選用| String| 當 source 沒有任何值時，要用於取代值的屬性名稱。|
| **template**| 選用| String| 提供 **template** 值時，我們會尋找 template 內的 **oldValue** 並以 source 值取代。|



----------

### StripSpaces

 


 




| 名稱| 必要 / 重複| 型別| 注意事項|
|--- | ---                 | ---  | ---   |
| **source**| 必要| String| 要更新的 **source** 值。|



----------

### Switch

 


 
如果 **source** 值不符合任何 key，則傳回 **defaultValue**。 **Key** 和 **value** 參數必須永遠成對出現。 函式必須要有偶數數目的參數。



| 名稱| 必要 / 重複| 型別| 注意事項|
|--- | ---                 | ---  | ---   |
| **source**| 必要| String| 要更新的 **source** 值。|
| **defaultValue**| 選用| String| 當 source 不符合任何 key 時要使用的預設值。可以是空字串 ("")。|
| **key**| 必要| String| 要與 **source** 值比較的 **key**。|
| **value**| 必要| String| 符合 key 的 **source** 的取代值。|



## 範例

### 刪去已知的網域名稱










- **輸入** (mail)："john.doe@contoso.com"

- 


### 附加常數尾碼到使用者名稱

如果您使用 Salesforce 沙箱，您可能需要附加額外的尾碼到您所有的使用者名稱，才能進行同步處理。









- **輸入**：(userPrincipalName)："John.Doe@contoso.com"

- 





### 串連部分名字和姓氏產生使用者別名

您必須取出使用者名字的前 3 個字母和使用者姓氏的前 5 個字母來產生使用者別名。







- **輸入** (givenName)："John"

- **輸入** (surname)："Doe"

- 




### 以特定格式將日期輸出為字串










**範例輸入/輸出：**

- **輸入** (extensionAttribute1)："20150123105347.1Z"

- 





### 根據預先定義的一組選項取代值









**範例輸入/輸出：**

- **輸入** (state)："QLD"

- **輸出**："Australia/Brisbane"


[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]





