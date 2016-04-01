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

- 整個運算式必須以函式定義，名稱後面接著括號括住的引數所組成 ︰ <br>
*FunctionName (<< 引數 1 >>，<<argument N>>)*


- 您可以在函式內互相巢狀函式。 例如： <br> *FunctionOne (FunctionTwo(<<argument1>>))*


- 您可以將三種不同類型的引數傳入函式：

   1. 屬性，必須以方括弧括住。 例如：[attributeName]

   2. 字串常數，必須以雙引號括住。 例如："United States"

   3. 其他函式。 例如 ︰ FunctionOne (<<argument1>>，FunctionTwo (<<argument2>>))


- 對於字串常數，如果您在字串中需要反斜線 ( \ ) 或引號 ( " ) ，則必須使用反斜線 ( \ ) 符號逸出。 例如:"公司名稱 ︰ \"Contoso\"」



## 函式的清單

[附加](#append) （& s) nbsp; （& s) nbsp; & nbsp; & nbsp; [FormatDateTime](#formatdatetime) （& s) nbsp; （& s) nbsp; & nbsp; & nbsp; [加入](#join) （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [Mid](#mid) （& s) nbsp; （& s) nbsp; & nbsp; & nbsp; [不](#not) （& s) nbsp; （& s) nbsp; & nbsp; & nbsp; [取代](#replace) （& s) nbsp; （& s) nbsp; & nbsp; & nbsp; [StripSpaces](#stripspaces) （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [參數](#switch)





----------
### Append

**函式 ︰**<br> 
Append(source, suffix)

**Description:**<br> 
來源字串值，並在結尾附加尾碼。
 
**參數：**<br> 

|名稱| 必要 / 重複 | 型別 | 注意事項 |
|--- | ---                 | ---  | ---   |
| **來源** | 必要 | String | 通常為 source 物件的屬性名稱 |
| **suffix** | 必要 | String | 您想要附加至 source 值結尾的字串。 |


----------
### FormatDateTime

**函式 ︰**<br> 
FormatDateTime （source，inputFormat，outputFormat）

**Description:**<br> 
將日期字串的格式，並將它轉換成不同的格式。
 
**參數：**<br> 

|名稱| 必要 / 重複 | 型別 | 注意事項 |
|--- | ---                 | ---  | ---   |
| **來源** | 必要 | String | 通常為 source 物件的屬性名稱。 |
| **inputFormat** | 必要 | String | source 值的預期格式。 如需支援的格式，請參閱 [http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)。 |
| **outputFormat** | 必要 | String | 輸出日期的格式。 |



----------
### Join

**函式 ︰**<br> 
聯結 （分隔符號，source1，source2，…）

**Description:**<br> 
執行 join （） 類似 append （），之處在於它可以合併多個 **來源** 成單一字串，值的字串，並將隔開每個值 **分隔符號** 字串。

如果其中一個 source 值是多重值屬性，則該屬性中的每個值會結合在一起，並以分隔符號值分隔。

 
**參數：**<br> 

|名稱| 必要 / 重複 | 型別 | 注意事項 |
|--- | ---                 | ---  | ---   |
| **separator** | 必要 | String | 用來分隔串連成一個字串的 source 值的字串。 如果不需要分隔符號，可以是 ""。 |
| * * source1... sourceN * * | 必要、 次數的變數數目 | String | 字串聯結在一起的值。 |



----------
### Mid

**函式 ︰**<br> 
Mid （來源，開始，長度）

**Description:**<br> 
傳回來源值的子字串。 子字串是只包含 source 字串某些字元的字串。


**參數：**<br> 

|名稱| 必要 / 重複 | 型別 | 注意事項 |
|--- | ---                 | ---  | ---   |
| **來源** | 必要 | String | 通常為屬性的名稱。 |
| **start** | 必要 | integer | 索引中 **來源** 字串的子字串中起始位置。 字串第一個字元的索引為 1，第二個字元的索引為 2，依此類推。 |
| **length** | 必要 | integer | 子字串的長度。 如果長度超出 **來源** 字串，函式會傳回子字串從 **開始** 索引至結尾 **來源** 字串。 |




----------
### 否

**函式 ︰**<br> 
Not （source)

**Description:**<br> 
翻轉的布林值 **來源**。 如果 **來源** 值是"*True*」，會傳回"*False*」。 否則，會傳回 「*True*」。


**參數：**<br> 

|名稱| 必要 / 重複 | 型別 | 注意事項 |
|--- | ---                 | ---  | ---   |
| **來源** | 必要 | Boolean String | 預期 **來源** 值為"True"或"False"... |



----------
### 將

**函式 ︰**<br> 
ObsoleteReplace （來源、 oldValue，regexPattern，regexGroupName，replacementValue，replacementAttributeName，範本）

**Description:**<br>
取代字串內的值。 根據提供的參數而以不同的方式運作：

- 當 **oldValue** 和 **replacementValue** 提供 ︰

   - 以 replacementValue 取代 oldValue 來源中的所有項目

- 當 **oldValue** 和 **範本** 提供 ︰

   - 所有項目 **oldValue** 中 **範本** 與 **來源** 值

- 當 **oldValueRegexPattern**, ，**oldValueRegexGroupName**, ，**replacementValue** 提供 ︰

   - 以 replacementValue 取代 source 字串中符合 oldValueRegexPattern 的所有值

- 當 **oldValueRegexPattern**, ，**oldValueRegexGroupName**, ，**replacementPropertyName** 提供 ︰

   - 如果 **來源** 值時， **來源** 傳回

   - 如果 **來源** 沒有值，會使用 **oldValueRegexPattern** 和 **oldValueRegexGroupName** 從屬性擷取取代值 **replacementPropertyName**。 結果會傳回取代值


**參數：**<br> 

|名稱| 必要 / 重複 | 型別 | 注意事項 |
|--- | ---                 | ---  | ---   |
| **來源** | 必要 | String | 通常為 source 物件的屬性名稱。 |
| **oldValue** | 選用 | String | 要取代的值 **來源** 或 **範本**。 |
| **regexPattern** | 選用 | String | 要取代的值的 Regex 模式 **來源**。 或者，如果使用了 replacementPropertyName，要從取代屬性擷取值的模式。 |
| **regexGroupName** | 選用 | String | 內的群組名稱 **regexPattern**。 只要使用 replacementPropertyName 時，我們會做為 replacementValue 從取代屬性擷取此群組的值。 |
| **replacementValue** | 選用 | String | 要取代舊值的新值。 |
| **replacementAttributeName** | 選用 | String | 當 source 沒有任何值時，要用於取代值的屬性名稱。 |
| **template** | 選用 | String | 當 **範本** 提供值，我們會找出 **oldValue** 在範本內，並以 source 值取代。 |



----------
### StripSpaces

**函式 ︰**<br> 
Stripspaces （source)

**Description:**<br> 
移除開頭空格 ("") 從來源字串的字元。

**參數：**<br> 

|名稱| 必要 / 重複 | 型別 | 注意事項 |
|--- | ---                 | ---  | ---   |
| **來源** | 必要 | String | **來源** 值，以更新。 |



----------
### Switch

**函式 ︰**<br> 
參數 （來源、 defaultValue，key1，value1，key2、 value2，...）

**Description:**<br> 
當 **來源** 值符合 **金鑰**, ，傳回 **值** 的 **金鑰**。 如果 **來源** 值不符合任何 key，則傳回 **defaultValue**。  **索引鍵** 和 **值** 參數必須永遠成對出現。 函式必須要有偶數數目的參數。

**參數：**<br> 

|名稱| 必要 / 重複 | 型別 | 注意事項 |
|--- | ---                 | ---  | ---   |
| **來源** | 必要 | String | **來源** 值，以更新。 |
| **defaultValue** | 選用 | String | 當 source 不符合任何 key 時要使用的預設值。 可以是空字串 ("")。 |
| **索引鍵** | 必要 | String | **索引鍵** 比較 **來源** 具有值。 |
| **value** | 必要 | String | 取代值 **來源** 比對索引鍵。 |



## 範例

### 刪去已知的網域名稱

您必須從使用者的電子郵件刪去已知的網域名稱，得到使用者名稱。 <br>
例如，如果網域是"contoso.com"，您可以使用下列運算式 ︰


**運算式 ︰** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**範例輸入 / 輸出 ︰** <br>

- **輸入** (mail):"john.doe@contoso.com"

- **輸出**:"john.doe"


### 附加常數尾碼到使用者名稱

如果您使用 Salesforce 沙箱，您可能需要附加額外的尾碼到您所有的使用者名稱，才能進行同步處理。




**運算式 ︰** <br>
`Append([userPrincipalName], ".test"))`

**範例輸入/輸出：** <br>

- **輸入**: (userPrincipalName):"John.Doe@contoso.com"


- **輸出**:"John.Doe@contoso.com.test"





### 串連部分名字和姓氏產生使用者別名

您必須取出使用者名字的前 3 個字母和使用者姓氏的前 5 個字母來產生使用者別名。


**運算式 ︰** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**範例輸入/輸出：** <br>

- **輸入** (givenName):"John"

- **輸入** (surname):"Doe"

- **輸出**:"JohDoe"




### 以特定格式將日期輸出為字串

您想要以特定格式傳送日期到 SaaS 應用程式。 <br>
例如，您要格式化 ServiceNow 的日期。



**運算式 ︰** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**範例輸入/輸出：**

- **輸入** (extensionAttribute1):"20150123105347.1 z"

- **輸出**:"2015年-01-23"





### 根據預先定義的一組選項取代值

您必須根據儲存在 Azure AD 中的狀態碼定義使用者的時區。 <br>
如果狀態碼不符合任何預先定義的選項，請使用"Australia/Sydney"的預設值。


**運算式 ︰** <br>

`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**範例輸入/輸出：**

- **輸入** （狀態）:"QLD"

- **輸出**:"Australia/Brisbane"


[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]


