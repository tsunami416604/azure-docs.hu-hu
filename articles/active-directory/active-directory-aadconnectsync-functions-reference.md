<properties
    pageTitle="Azure AD Connect 同步：函式參考 | Microsoft Azure"
    description="Azure AD Connect 同步處理中宣告式佈建運算式的參考。"
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="StevenPo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2015"
    ms.author="markusvi"/>


# Azure AD Connect 同步處理：函式參考


在 Azure Active Directory 同步處理中，函式可在同步處理期間用來操作屬性值。 <br>
使用下列格式來表示函式的語法 ︰ <br>
`<output type> FunctionName(<input type> <position name>, ..)`

如果函式是多載的且接受多種語法，即會列出所有的有效語法。<br>
函式是強型別的，且會確認傳入的類型與記載的類型相符。<br>
如果類型不符，即會擲回錯誤。

類型可以使用下列語法來表示：

- **bin** -二進位
- **bool** – 布林值
- **dt** – UTC 日期/時間
- **列舉** – 已知常數的列舉
- **exp** – 運算式必須評估為布林值
- **mvbin** – 多重值二進位
- **mvstr** – 多重值的參考
- **num** – 數值
- **ref** – 單一值的參考
- **str** – 單一值的字串
- **var** – （幾乎） 任何其他類型的 variant
- **void** – 不會傳回值



## 函式參考

----------
**轉換：**

[CBool](#cbool) （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [CDate](#cdate) （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [CGuid](#cguid) （& s) nbsp; （& s) nbsp; （& s) nbsp; & nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; （& s) nbsp; [ConvertFromBase64](#convertfrombase64) （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [ConvertToBase64](#converttobase64) （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [ConvertFromUTF8Hex](#convertfromutf8hex) （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [ConvertToUTF8Hex](#converttoutf8hex) （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [CNum](#cnum)  （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [CRef](#cref) （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [CStr](#cstr)  （& s) nbsp; （& s) nbsp; & nbsp; & nbsp; [StringFromGuid](#StringFromGuid) （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [StringFromSid](#stringfromsid)

**日期 / 時間：**

[DateAdd](#dateadd) （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [DateFromNum](#datefromnum)  （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [FormatDateTime](#formatdatetime)  （& s) nbsp; （& s) nbsp; & nbsp; & nbsp; [現在](#now)  （& s) nbsp; （& s) nbsp; & nbsp; & nbsp; [NumFromDate](#numfromdate)

**目錄**

[DNComponent](#dncomponent)  （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [DNComponentRev](#dncomponentrev) （& s) nbsp; （& s) nbsp; & nbsp; & nbsp; [EscapeDNComponent](#escapedncomponent)

**檢查：**

[IsBitSet](#isbitset)  （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [IsDate](#isdate) （& s) nbsp; （& s) nbsp; & nbsp; & nbsp; [IsEmpty](#isempty)
（& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [IsGuid](#isguid) （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [IsNull](#isnull) （& s) nbsp; （& s) nbsp; & nbsp; & nbsp; [IsNullOrEmpty](#isnullorempty) （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [IsNumeric](#isnumeric)  （& s) nbsp; （& s) nbsp; & nbsp; & nbsp; [IsPresent](#ispresent) （& s) nbsp; （& s) nbsp; & nbsp; & nbsp; [IsString](#isstring)

**數學運算：**

[BitAnd](#bitand) （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [BitOr](#bitor) （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [RandomNum](#randomnum)

**多重值**

[包含](#contains) （& s) nbsp; （& s) nbsp; & nbsp; & nbsp; [計數](#count) （& s) nbsp; （& s) nbsp; & nbsp; & nbsp; [項目](#item)   （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [ItemOrNull](#itemornull) （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [加入](#join) （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [RemoveDuplicates](#removeduplicates) （& s) nbsp; （& s) nbsp; & nbsp; & nbsp; [分割](#split)

**程式流程：**

[錯誤](#error) （& s) nbsp; （& s) nbsp; & nbsp; & nbsp; [IIF](#iif)  （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [參數](#switch)


**文字**

[GUID](#guid) （& s) nbsp; （& s) nbsp; & nbsp; & nbsp; [InStr](#instr) （& s) nbsp; （& s) nbsp; & nbsp; & nbsp; [InStrRev](#instrrev) （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [LCase](#lcase) （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [左](#left) （& s) nbsp; & nbsp; （& s) nbsp; （& s) nbsp; [Len](#len) （& s) nbsp; （& s) nbsp; & nbsp; & nbsp; [LTrim](#ltrim)  （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [Mid](#mid)  （& s) nbsp; （& s) nbsp; & nbsp; & nbsp; [PadLeft](#padleft) （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [PadRight](#padright) （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [PCase](#pcase)   （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [取代](#replace) （& s) nbsp; （& s) nbsp; & nbsp; & nbsp; [ReplaceChars](#replacechars) （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [右](#right) （& s) nbsp; （& s) nbsp; & nbsp; （& s) nbsp; [RTrim](rtrim) （& s) nbsp; & nbsp; & nbsp; （& s) nbsp; [修剪](#trim) （& s) nbsp; （& s) nbsp; & nbsp; & nbsp; [UCase](#ucase) （& s) nbsp; （& s) nbsp; & nbsp; & nbsp; [Word](#word)

----------
### BitAnd

**Description:**<br>
BitAnd 函式會設定指定的位元的值。

**語法：**<br>
`num BitAnd(num value1, num value2)`

- value1，value2：應該使用 AND 連結在一起的數值

**備註：**<br>
此函式會將這兩個參數轉換成二進位表示法，並設定一個位元 ︰

- 0-如果有一個或多個對應位元 *遮罩* 和 *旗標* 0
- 1 - 如果這兩個對應位元都是 1。

換句話說，除非這兩個參數的對應位元都是 1，否則在所有情況下都會傳回 0。

**範例：**<br>
`BitAnd(&HF, &HF7)`<br>
傳回 7，因為十六進位"F"AND"F7"評估為此值。

----------
### BitOr

**Description:** <br>
BitOr 函式會設定指定的位元的值。

**語法：** <br>
`num BitOr(num value1, num value2)`

- value1，value2：應該使用 OR 連結在一起的數值

**備註：** <br>
此函數會將這兩個參數轉換成二進位表示法，並設定一個位元為 1，如果一或兩個對應位元遮罩和旗標都是 1，並為 0 如果兩個對應位元為 0。 <br>
換句話說，除非這兩個參數的對應位元都是 0，否則在所有情況下都會傳回 1。

----------
### CBool

**Description:**<br>
CBool 函式會傳回布林值，根據評估的運算式

**語法：** <br>
`bool CBool(exp Expression)`

**備註：**<br>
如果運算式評估為非零值，cbool 就會傳回 True 否則它會傳回 False。


**範例：**<br>
`CBool([attrib1] = [attrib2])` <br>

如果這兩個屬性的值相同，即會傳回 True。




----------
### CDate

**Description:**<br>
CDate 函數從字串傳回 UTC 日期時間。 日期時間不是同步處理中的原生屬性類型，但有部分函式會用到。

**語法：**<br>
`dt CDate(str value)`

- Value：包含日期、時間和選擇性時區的字串

**備註：**<br>
傳回的字串一律是 utc 格式。

**範例：**<br>
`CDate([employeeStartTime])` <br>
傳回日期時間根據員工的開始時間

`CDate("2013-01-10 4:00 PM -8")` <br>
傳回日期時間，代表"2013年-01-11 12:00 AM"




----------
### CGuid

**Description:**<br>
CGuid 函式會將 GUID 的字串表示轉換成其二進位表示法。

**語法：**<br>
`bin CGuid(str GUID)GUID`

- 使用此模式格式化的字串：xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 或 {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}




----------
### Contains

**Description:**<br>
Contains 函式內尋找字串多重值屬性

**語法：**<br>
`num Contains (mvstring attribute, str search)` -區分大小寫<br>
`num Contains (mvstring attribute, str search, enum Casetype)`<br>
`num Contains (mvref attribute, str search)` -區分大小寫

- 屬性 ︰ 要搜尋的多重值的屬性。<br>
- 搜尋 ︰ 在屬性中尋找的字串。<br>
- Casetype: CaseInsensitive 或 CaseSensitive。<br>

傳回在多重值屬性中找到字串的索引。 如果找不到該字串，即會傳回 0。


**備註：**<br>
多重值的字串屬性的搜尋會尋找子字串的值。<br>
針對參考屬性，搜尋的字串必須完全符合要被視為相符的值。

**範例：**<br>
`IIF(Contains([proxyAddresses],”SMTP:”)>0,[proxyAddresses],Error(“No primary SMTP address found.”))`<br>
如果 proxyAddresses 屬性具有主要電子郵件地址 (以大寫"SMTP:")，則傳回 proxyAddress 屬性，否則會傳回錯誤。




----------
### ConvertFromBase64

**Description:**<br>
ConvertFromBase64 函數將指定的 base64 編碼值轉換為一般字串。

**語法：**<br>
`str ConvertFromBase64(str source)` -假設以 Unicode 編碼方式 <br>
`str ConvertFromBase64(str source, enum Encoding)`

- source：Base64 編碼的字串<br>
- Encoding：Unicode、ASCII、UTF8

**範例**<br>
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`<br>
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

這兩個範例會傳回 「*Hello world ！*」




----------
### ConvertFromUTF8Hex

**Description:**<br>
ConvertFromUTF8Hex 函式會將指定的 UTF8 十六進位編碼值轉換為字串。

**語法：**<br>
`str ConvertFromUTF8Hex(str source)`

- source：UTF8 2 個位元組的編碼字串

**備註：**<br>
此函式與在 ConvertFromBase64([],UTF8)，在於結果支援 DN 屬性之間的差異。<br>
Azure Active Directory 會使用此格式做為 DN。

**範例：**<br>
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`<br>
傳回"*Hello world ！*」




----------
### ConvertToBase64

**Description:** <br>
ConvertToBase64 函式會將字串轉換為 Unicode base64 字串。<br>
將整數陣列的值轉換為其對等的字串表示法，此表示法是以 Base-64 數字編碼的。

**語法：** <br>
`str ConvertToBase64(str source)`

**範例：** <br>
`ConvertToBase64("Hello world!")` <br>
傳回"SABlAGwAbABvACAAdwBvAHIAbABkACEA"




----------
### ConvertToUTF8Hex

**Description:**<br>
ConvertToUTF8Hex 函式會將字串轉換為 UTF8 十六進位編碼值。

**語法：**<br>
`str ConvertToUTF8Hex(str source)`

**備註：**<br>
此函式的輸出格式可供 Azure Active Directory 做為 DN 屬性格式。

**範例：** <br>
`ConvertToUTF8Hex("Hello world!")` <br>
/ / 傳回 48656C6C6F20776F726C6421




----------
### Count

**Description:**<br>
Count 函數傳回多重值屬性中的項目數

**語法：** <br>
`num Count(mvstr attribute)`




----------
### CNum

**Description:** <br>
CNum 函數接受字串，並傳回數值資料類型。

**語法：** <br>
`num CNum(str value)`




----------
### CRef

**Description:** <br>
將字串轉換為參考屬性

**語法：** <br>
`ref CRef(str value)`

**範例：** <br>
`CRef(“CN=LC Services,CN=Microsoft,CN=lcspool01, CN=Pools,CN=RTC Service,” & %Forest.LDAP%)`




----------
### CStr

**Description:** <br>
CStr 函式會將字串資料類型。

**語法：** <br>
`str CStr(num value)` <br>
`str CStr(ref value)` <br>
`str CStr(bool value)` <br>

- value：可以是數值、參考屬性或布林值。

**範例：** <br>
`CStr([dn]) <br>`
可能會傳回"cn = Joe，dc = contoso，dc = com"




----------
### DateAdd

**Description:** <br>
傳回日期，包含要加入指定的時間間隔的日期。

**語法：** <br>
`dt DateAdd(str interval, num value, dt date)`

- interval：字串運算式，此為您想要加入的時間間隔。 字串必須具有下列其中一個值：
 - yyyy 年
 - q 季
 - m 月
 - y 一年中第幾天
 - d 日
 - w 工作日
 - ww 周
 - h 小時
 - n 分鐘
 - s 秒
- value：您想要加入的單位數。 它可以是正數 (用以取得未來的日期) 或負數 (用以取得過去的日期)。
- date：日期時間，代表要加入間隔的日期。

**範例：** <br>
`DateAdd("m", 3, CDate("2001-01-01"))` <br>
加入 3 個月，並傳回代表"2001年-04-01"的日期時間




----------
### DateFromNum

**Description:** <br>
DateFromNum 函數將 AD 日期值格式化為日期時間類型。

**語法：** <br>
`dt DateFromNum(num value)`

**範例：** <br>
`DateFromNum([lastLogonTimestamp])` <br>
`DateFromNum(129699324000000000)` <br>
會傳回代表 2012年-01-01 23:00:00




----------
### DNComponent

**Description:** <br>
DNComponent 函數會傳回指定 DN 元件從左邊的值。

**語法：** <br>
`str DNComponent(ref dn, num ComponentNumber)`

- dn：要解譯的參考屬性
- ComponentNumber：DN 中要傳回的元件

**範例：** <br>
`DNComponent([dn],1)`  <br>
如果 dn 是"cn = Joe，ou = …，就會傳回 Joe




----------
### DNComponentRev

**Description:** <br>
DNComponentRev 函數會傳回從右邊 （結尾處） 指定 DN 元件的值。

**語法：** <br>
`str DNComponentRev(ref dn, num ComponentNumber)` <br>
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

- dn：要解譯的參考屬性
- ComponentNumber - DN 中要傳回的元件
- Options：DC - 忽略所有含 "dc =" 的元件

**範例：** <br>
`If dn is “cn=Joe,ou=Atlanta,ou=GA,ou=US, dc=contoso,dc=com” then DNComponentRev([dn],3)` <br>  `DNComponentRev([dn],1,”DC”)` <br>
兩者都傳回 US。




----------
### 錯誤

**Description:** <br>
錯誤函式用來傳回自訂錯誤。

**語法：** <br>
`void Error(str ErrorMessage)`

**範例：** <br>
`IIF(IsPresent([accountName]),[accountName],Error(“AccountName is required”))` <br>
如果 accountName 屬性不存在，會在物件上擲回錯誤。




----------
### EscapeDNComponent

**Description:** <br>
EscapeDNComponent 函式會採用 DN 的一個元件，並以 LDAP 逸出。

**語法：** <br>
`str EscapeDNComponent(str value)`

**範例：** <br>
`EscapeDNComponent(“cn=” & [displayName]) & “,” & %ForestLDAP%` <br>
可確保即使 displayName 屬性具有必須在 LDAP 中逸出字元，可以在 LDAP 目錄中建立的物件。




----------
### FormatDateTime

**Description:** <br>
FormatDateTime 函式用來將日期時間格式化字串，使用指定的格式

**語法：** <br>
`str FormatDateTime(dt value, str format)`

- value：具備日期時間格式的值 <br>
- format：字串，表示要轉換的目標格式。

**備註：** <br>
可以在這裡找到格式的可能值 ︰ [使用者定義日期/時間格式 （Format 函式）](http://msdn2.microsoft.com/library/73ctwf33\(VS.90\).aspx)

**範例：** <br>

`FormatDateTime(CDate(“12/25/2007”),”yyyy-mm-dd”)` <br>
結果是"2007年-12-25"。

`FormatDateTime(DateFromNum([pwdLastSet]),”yyyyMMddHHmmss.0Z”)` <br>
20140905081453.0"」




----------
### GUID

**Description:** <br>
函式會產生新的隨機 GUID

**語法：** <br>
`str GUID()`




----------
### IIF

**Description:** <br>  
IIF 函數會傳回一組可能的值根據指定的條件。

**語法：** <br>
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

- condition：可評估為 True 或 False 的任何值或運算式。
- valueIfTrue：條件評估為 True 時所傳回的值。
- valueIfFalse：條件評估為 False 時所傳回的值。

**範例：** <br>
`IIF([employeeType]=“Intern”,”t-“&[alias],[alias])` <br>
傳回具有 「 t-」 加入至它的開頭，如果使用者是實習生，其他的使用者別名傳回因為是使用者的別名。




----------
### InStr

**Description:** <br>
InStr 函數在字串中尋找第一個子字串

**語法：** <br>

`num InStr(str stringcheck, str stringmatch)` <br>
`num InStr(str stringcheck, str stringmatch, num start)` <br>
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

- stringcheck：要搜尋的字串 <br>
- stringmatch：要尋找的字串 <br>
- start：開始尋找子字串的位置 <br>
- compare：vbTextCompare 或 vbBinaryCompare

**備註：** <br>
傳回的位置，其中找到子字串，或找不到 0。

**範例：** <br>
`InStr("The quick brown fox","quick")` <br>
評估為 5

`InStr("repEated","e",3,vbBinaryCompare)` <br>
評估為 7




----------
### InStrRev

**Description:** <br>
InStrRev 函數在字串中尋找子字串的最後一個項目

**語法：** <br>
`num InstrRev(str stringcheck, str stringmatch)` <br>
`num InstrRev(str stringcheck, str stringmatch, num start)` <br>
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

- stringcheck：要搜尋的字串 <br>
- stringmatch：要尋找的字串 <br>
- start：開始尋找子字串的位置 <br>
- compare：vbTextCompare 或 vbBinaryCompare

**備註：** <br>
傳回的位置，其中找到子字串，或找不到 0。

**範例：** <br>
`InStrRev("abbcdbbbef","bb")` <br>
傳回 7




----------
### IsBitSet

**Description:** <br>
已設定 IsBitSet 函數測試位元

**語法：** <br>
`bool IsBitSet(num value, num flag)`

- value：評估的數值。flag：具有要評估之位元的數值

**範例：** <br>
`IsBitSet(&HF,4)` <br>
傳回 True，因為位元"4"設定的十六進位值"F"




----------
### IsDate

**Description:** <br>
IsDate 函式會評估為 True，如果運算式可以是會評估為 DateTime 型別。

**語法：** <br>
`bool IsDate(var Expression)`

**備註：** <br>
用來判斷 cdate （） 是否會成功。




----------
###IsEmpty

**Description:** <br>  
如果屬性存在於 CS 或 MV 中，但評估為空字串，IsEmpty 函式會評估為 True。

**語法：** <br>
`bool IsEmpty(var Expression)`




----------
###IsGuid

**Description:** <br>
評估為 true，如果字串無法轉換成 GUID，IsGuid 函式。

**語法：** <br>
`bool IsGuid(str GUID)`

**備註：** <br>
GUID 定義為下列其中一種模式的字串 ︰ xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx 或 {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

用來判斷 CGuid() 是否將會成功。

**範例：** <br>
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)` <br>
如果 StrAttribute 具有 GUID 格式，傳回的二進位表示法，否則會傳回 Null。




----------
###IsNull

**Description:** <br>
IsNull 函數會傳回 true，如果運算式評估為 Null。

**語法：** <br>
`bool IsNull(var Expression)`

**備註：** <br>
針對屬性，Null 表示該屬性不存在。

**範例：** <br>
`IsNull([displayName])` <br>
如果屬性不存在 CS 或 MV 中，則傳回 True。




----------
###IsNullOrEmpty

**Description:** <br>
IsNullOrEmpty 函數會傳回 true，如果運算式為 null 或空字串。

**語法：** <br>
`bool IsNullOrEmpty(var Expression)`

**備註：** <br>
針對屬性，這會評估為 True，如果屬性不存在，或存在但為空字串。<br>
此函式的相反函式名稱為 IsPresent。

**範例：** <br>
`IsNull([displayName])` <br>
如果屬性不存在，或為空字串在 CS 或 MV 中的，則傳回 True。




----------
### IsNumeric

**Description:** <br>
IsNumeric 函數會傳回布林值，指出運算式是否可評估為數字類型。

**語法：** <br>
`bool IsNumeric(var Expression)`

**備註：** <br>
用來判斷 cnum （） 是否會成功剖析運算式。




----------
### IsString

**Description:** <br>
IsString 函式會評估為 True，如果運算式可評估為字串型別。

**語法：** <br>
`bool IsString(var expression)`

**備註：** <br>
用來判斷 cstr （） 是否可成功剖析運算式。




----------
### IsPresent

**Description:** <br>
IsPresent 函數會傳回 true，如果運算式評估為不是 Null，而不是空的字串。

**語法：** <br>
`bool IsPresent(var expression)`

**備註：** <br>
此函式名稱為 IsNullOrEmpty。

**範例：** <br>

`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`




----------
### 項目

**Description:** <br>
Item 函數從多重值字串/屬性傳回一個項目。

**語法：** <br>
`var Item(mvstr attribute, num index)`

- attribute：多重值的屬性 <br>
- index：多重值字串中項目的索引。

**備註：** <br>
Item 函式可以與 Contains 函數一起搭配因為後者函式將會傳回多重值屬性中的項目索引。

如果索引超出範圍，即會擲回錯誤。

**範例：** <br>
`Mid(Item([proxyAddress],Contains([proxyAddress], ”SMTP:”)),6)`  <br>
傳回主要電子郵件地址。




----------
### ItemOrNull

**Description:** <br>
ItemOrNull 函數從多重值字串/屬性傳回一個項目。

**語法：** <br>
`var ItemOrNull(mvstr attribute, num index)`

- attribute：多重值的屬性 <br>
- index：多重值字串中項目的索引。

**備註：** <br>
ItemOrNull 函數適合與 Contains 函數一起因為後者函式將會傳回多重值屬性中的項目索引。

如果索引超出範圍，即會傳回 Null 值。




----------
### Join

**Description:** <br>
Join 函數接受多重值的字串，並傳回單一值的字串，其中每個項目之間插入指定的分隔符號。

**語法：** <br>
`str Join(mvstr attribute)` <br>
`str Join(mvstr attribute, str Delimiter)`

- attribute：包含要聯結之字串的多重值屬性。 <br>
- delimiter：任何字串，可用來分隔傳回字串中的子字串。 如果省略，即會使用空格字元 (" ")。 如果分隔符號是零長度字串 ("") 或 Nothing，就不會使用分隔符號來串連清單中的所有項目。

**備註**<br>
Join 和 Split 函式之間沒有同位檢查。 Join 函式可接受字串陣列，並使用分隔符號字串來聯結它們，以傳回單一字串。 Split 函式會取得字串並以分隔符號來分隔，以傳回字串陣列。 不過，主要的差別是 Join 可以使用任何分隔符號字串來串連字串，Split 只能使用單一字元分隔符號來分隔字串。

**範例：** <br>
`Join([proxyAddresses],”,”)` <br>
可能會傳回:"SMTP:john.doe@contoso.com，smtp:jd@contoso.com"




----------
### LCase

**Description:** <br>
LCase 函式會將字串中的所有字元都轉換為小寫。

**語法：** <br>
`str LCase(str value)`

**範例：** <br>
`LCase(“TeSt”)` <br>
傳回"test"。




----------
### Left

**Description:** <br>
Left 的函數從字串左邊傳回指定的字元數。

**語法：** <br>
`str Left(str string, num NumChars)`

- string：要傳回字元的字串 <br>
- NumChars：數字，識別從 string 開頭 (左邊) 傳回的字元數

**備註：** <br>
包含在字串中的前 numChars 個字元的字串 ︰

- 如果 numChars = 0，會傳回空字串。
- 如果 numChars < 0，會傳回輸入字串。
- 如果 string 為 Null，會傳回空字串。

如果 string 包含的字元數比 numChars 中指定的數目少，即會傳回與 string 完全相同的字串 (例如，包含參數 1 中的所有字元)。

**範例：** <br>
`Left(“John Doe”, 3)` <br>
傳回"Joh"。




----------
### Len

**Description:** <br>
Len 函數會傳回字串中的字元數。

**語法：** <br>
`num Len(str value)`

**範例：** <br>
`Len(“John Doe”)` <br>
傳回 8




----------
### LTrim

**Description:** <br>
LTrim 函數從字串移除開頭空白字元。

**語法：** <br>
`str LTrim(str value)`

**範例：** <br>
`LTrim(“ Test ”)` <br>
傳回"Test"




----------
### Mid

**Description:** <br>
Mid 函數從字串中指定的位置傳回指定的字元數。

**語法：** <br>
`str Mid(str string, num start, num NumChars)`

- string：要傳回字元的字串 <br>
- start：數字，可識別 string 中要傳回字元的起始位置
- NumChars：數字，可識別要從 string 中的位置傳回的字元數


**備註：** <br>
傳回從字串中的位置 start 開始的 numChars 個字元。<br>
包含從 string 中的位置 start 起算的 numChars 個字元的字串：

- 如果 numChars = 0，會傳回空字串。
- 如果 numChars < 0，會傳回輸入字串。
- 如果 start > 字串的長度，會傳回輸入字串。
- 如果 start < = 0，會傳回輸入字串。
- 如果 string 為 Null，會傳回空字串。

如果 string 中從位置 start 起算所剩餘的字元數不足 numChar 個字元，即會盡可能地傳回所有可傳回的字元。

**範例：** <br>

`Mid(“John Doe”, 3, 5)` <br>
傳回"hn Do"。

`Mid(“John Doe”, 6, 999)` <br>
傳回"Doe"




----------
### Now

**Description:** <br>
Now 函數會傳回指定目前的日期和時間，根據您的電腦系統日期和時間。

**語法：** <br>
`dt Now()`




----------
### NumFromDate

**Description:** <br>
NumFromDate 函式會傳回 AD 日期格式的日期。

**語法：** <br>
`num NumFromDate(dt value)`


**範例：** <br>
`NumFromDate(CDate("2012-01-01 23:00:00"))` <br>
傳回 129699324000000000




----------
### PadLeft

**Description:** <br>
PadLeft 函數左側填補到指定的長度，使用提供的填補字元的字串。

**語法：** <br>
`str PadLeft(str string, num length, str padCharacter)`

- string：要填補的字串。 <br>
- length：表示 string 所需長度的整數。 <br>
- padCharacter：字串，由用來做為填補字元的單一字元所組成



----------
### 備註

- 如果 string 的長度小於 length，則會將 padCharacter 重複附加至 string 的開頭 (左邊)，直到它的長度等於 length 為止。
- PadCharacter 可以是空白字元，但不能是 Null 值。
- 如果 string 的長度等於或大於 length，就會以原樣傳回 string。
- 如果 string 的長度大於或等於 length，即會傳回與 string 完全相同的字串。
- 如果 string 的長度小於 length，則會傳回所需長度的新字串，包含使用 padCharacter 填補的 string。
- 如果 string 為 Null，函式即會傳回空字串。

**範例：** <br>
`PadLeft(“User”, 10, “0”)` <br>
傳回"000000User"。




----------
### PadRight

**Description:** <br>
PadRight 函數，右邊填補到指定的長度，使用提供的填補字元的字串。

**語法：** <br>
`str PadRight(str string, num length, str padCharacter)`

- string：要填補的字串。
- length：表示 string 所需長度的整數。
- padCharacter：字串，由用來做為填補字元的單一字元所組成

**備註：**

- 如果 string 的長度小於 length，則會將 padCharacter 重複附加至 string 的結尾 (右邊)，直到它的長度等於 length 為止。
- padCharacter 可以是空白字元，但不能是 Null 值。
- 如果 string 的長度等於或大於 length，就會以原樣傳回 string。
- 如果 string 的長度大於或等於 length，即會傳回與 string 完全相同的字串。
- 如果 string 的長度小於 length，則會傳回所需長度的新字串，包含使用 padCharacter 填補的 string。
- 如果 string 為 Null，函式即會傳回空字串。


**範例：** <br>
`PadRight(“User”, 10, “0”)` <br>
傳回"User000000"。




----------
### PCase

**Description:** <br>
PCase 函數將字串中每個空格分隔字的第一個字元轉換為大寫，和其他所有字元都轉換為小寫。

**語法：** <br>
`String PCase(string)`

**範例：** <br>
`PCase(“TEsT”)` <br>
傳回"Test"。




----------
### RandomNum

**Description:** <br>
RandomNum 函數會傳回一個隨機數字指定間隔。

**語法：** <br>
`num RandomNum(num start, num end)`

- start：可識別要產生之隨機值下限的數字 <br>
- end：可識別要產生之隨機值上限的數字

**範例：** <br>
`Random(100,999)` <br>
傳回 734。




----------
### RemoveDuplicates

**Description:** <br>
RemoveDuplicates 函數接受多重值的字串，並確定每個值是唯一的。

**語法：** <br>  
`mvstr RemoveDuplicates(mvstr attribute)`

**範例：** <br>
`RemoveDuplicates([proxyAddresses])` <br>
傳回處理過的 proxyAddress 屬性，其中已移除所有重複的值。




----------
### 將

**Description:** <br>
Replace 函數會取代所有出現的另一個字串的字串。

**語法：** <br>
`str Replace(str string, str OldValue, str NewValue)`

- string：要取代其值的字串。 <br>
- OldValue：要搜尋並取代的字串。 <br>
- NewValue：要取代的字串。


**備註：** <br>
函式會辨識下列特殊的 moniker:

- \n – 新行
- \r – 歸位字元
- \t – 定位字元


**範例：** <br>

`Replace([address],”\r\n”,”, “)` <br>
CRLF 取代逗號和空格，而且可能會導致 」 一個 Microsoft Way，Redmond，WA，USA"




----------
### ReplaceChars

**Description:** <br>
ReplaceChars 函數取代 ReplacePattern 字串中字元的所有項目。

**語法：** <br>
`str ReplaceChars(str string, str ReplacePattern)`

- string：要取代字元的字串。
- ReplacePattern：字串，包含要取代之字元的字典。

格式為 {source1}:{target1},{source2}:{target2},{sourceN},{targetN}，其中 source 為要尋找的字元，而 target 則為要取代的字串。


**備註：**

- 此函式會取得已定義之來源的每個出現項目，並使用目標來取代它們。
- 來源必須是一個 (unicode) 字元。
- 來源不能是空字串或超過一個字元 (剖析錯誤)。
- 目標可以有多個字元，例如 ö:oe、β:ss。
- 目標可以空白，表示應移除此字元。
- 來源是區分大小寫且必須完全相符。
- , (逗號) 和 : (冒號) 是保留字元，無法使用這個函式來取代。
- 系統會忽略空格和 ReplacePattern 字串中的其他空白字元。


**範例：** <br>
' %Replacestring%= ':，å ︰ a，ä，å:、 a，ö，o '

`ReplaceChars(”Räksmörgås”,%ReplaceString%)` <br>
傳回 Raksmorgas

`ReplaceChars(“O’Neil”,%ReplaceString%)` <br>
傳回"ONeil"，將單一撇號定義中移除。




----------
### Right

**Description:** <br>
Right 函數從字串右邊 （結尾處） 傳回指定的字元數。

**語法：** <br>
`str Right(str string, num NumChars)`

- string：要傳回字元的字串
- NumChars：數字，可識別要從 string 結尾 (右邊) 傳回的字元數

**備註：** <br>
從字串的最後一個位置傳回 NumChars 個字元。

包含 string 中最後 numChars 個字元的字串：

- 如果 numChars = 0，會傳回空字串。
- 如果 numChars < 0，會傳回輸入字串。
- 如果 string 為 Null，會傳回空字串。

如果 string 包含的字元數比 numChars 中指定的數目少，即會傳回與 string 完全相同的字串。

**範例：** <br>
`Right(“John Doe”, 3)` <br>
傳回"Doe"。




----------
### RTrim

**Description:** <br>
RTrim 函數從字串中移除尾端泛空白字元。

**語法：** <br>
`str RTrim(str value)`

**範例：** <br>
`RTrim(“ Test ”)` <br>
傳回"Test"。




----------
### 分割

**Description:** <br>
Split 函數接受以分隔符號分隔的字串，並使其成為多重值的字串。


**語法：** <br>
`mvstr Split(str value, str delimiter)` < b？
`mvstr Split(str value, str delimiter, num limit)`

- value：以分隔符號字元分隔的字串。
- delimiter：用來做為分隔符號的單一字元。
- limit：將傳回的值數目上限。

**範例：** <br>
`Split(“SMTP:john.doe@contoso.com,smtp:jd@contoso.com”,”,”)` <br>
傳回 proxyAddress 屬性非常有用的 2 個元素的多重值的字串




----------
### StringFromGuid

**Description:** <br>
StringFromGuid 函數接受二進位 GUID，並將它轉換成字串

**語法：** <br>
`str StringFromGuid(bin GUID)`




----------
### StringFromSid

**Description:** <br>
StringFromSid 函式將轉換的位元組陣列或多重值的位元組陣列，包含安全性識別碼的字串或多重值的字串。

**語法：** <br>
`str StringFromSid(bin ObjectSID)` <br>
`mvstr StringFromSid(mvbin ObjectSID)`




----------
### Switch

**Description:** <br>
Switch 函數用來傳回單一值，根據評估的條件。

**語法：** <br>
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

- expr：您想要評估的 Variant 運算式。
- value：當對應的運算式為 True 時要傳回的值。

**備註：** <br>
Switch 函數引數清單是由運算式和值的配對所組成。 運算式是以從左到右的方式進行評估，並會傳回與要評估為 True 的第一個運算式相關聯的值。 如果未正確配對組件，就會發生執行階段錯誤。

例如，如果 expr1 為 True，Switch 就會傳回 value1。 如果 expr-1 為 False，但 expr-2 為 True，Switch 就會傳回 value-2，依此類推。

在下列情況下，參數會傳回 Nothing︰
- 沒有任何運算式為 True。
- 第一個 True 運算式具有對應值 Null。

雖然 Switch 只會傳回其中一個運算式，但它會評估所有運算式。 基於這個理由，您應該留意非預期的副作用。 例如，如果任何運算式的評估會產生除以零的錯誤，就會發生錯誤。

Value 也可以是會傳回自訂字串的 Error 函式。

**範例：** <br>
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error(“Unknown city”))` <br>
傳回一些主要城市中的語言，否則會傳回錯誤。




----------
### Trim

**Description:** <br>
Trim 函數移除開頭和尾端空白字元的字串。

**語法：** <br>
`str Trim(str value)` <br>
`mvstr Trim(mvstr value)`

**範例：** <br>
`Trim(“ Test ”)` <br>
傳回"Test"。

`Trim([proxyAddresses])` <br>
移除開頭和尾端空白的 proxyAddress 屬性中的每個值。




----------
### UCase

**Description:** <br>
UCase 函式會將字串中的所有字元都轉換為大寫。

**語法：** <br>
`str UCase(str string)`

**範例：** <br>
`UCase(“TeSt”)` <br>
傳回"TEST"。




----------
### Word

**Description:** <br>
Word 函數會傳回包含在字串中，說明要使用分隔符號及要傳回之字數的參數為基礎的單字。

**語法：** <br>
`str Word(str string, num WordNumber, str delimiters)`

- string：要傳回單字的字串
- WordNumber：數字，可識別應傳回的字數。
- delimiters：字串，表示應用來識別單字的分隔符號

**備註：** <br>
每個字串，其中一個分隔符號中的字元來分隔的字串中的字元會被識別為單字 ︰

- 如果 number < 1，會傳回空字串。
- 如果 string 為 Null，會傳回空字串。

如果 string 所含的字數少於 number 個字，或者 string 不包含任何 delimeters 所識別的單字，就會傳回空字串。


**範例：** <br>
`Word(“The quick brown fox”,3,” “)` <br>
傳回"brown"

`Word(“This,string!has&many seperators”,3,”,!&#”)` <br>
會傳回"has"


## 其他資源

* [了解宣告式佈建運算式](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
* [Azure AD Connect 同步處理：自訂同步處理選項](active-directory-aadconnectsync-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)


<!--Image references-->


