---
title: Azure AD Connect Cloud kiépítési kifejezések és függvények referenciája
description: Hivatkozás
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51c14fd7f427c29c47521a7355309e62ab2254ca
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78298615"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Kifejezések írása a Azure Active Directory attribútum-hozzárendelésekhez
A felhő-kiépítés konfigurálásakor a megadható attribútumok egyik típusa egy kifejezés-hozzárendelés. 

A kifejezés-hozzárendelés lehetővé teszi az attribútumok testreszabását egy parancsfájl-szerű kifejezés használatával.  Ez lehetővé teszi a helyszíni értékek új vagy eltérő értékre alakítását.  Előfordulhat például, hogy két attribútumot szeretne egyesíteni egyetlen attribútummal, mert ezt az egyetlen attribútumot használja az egyik felhőalapú alkalmazás.

A következő dokumentum az adatátalakításhoz használt parancsfájl-szerű kifejezéseket fedi le.  Ez csak a folyamat része.  Ezután ezt a kifejezést kell használnia, és egy webes kérelembe kell helyeznie a bérlője számára.  További információ az [átalakításokról](how-to-transformation.md) :

## <a name="syntax-overview"></a>Szintaxis áttekintése
Attribútum-leképezéshez kifejezések szintaxisa reminiscent a Visual Basic függvényeihez kapcsolódó alkalmazások (VBA).

* A teljes kifejezésnek funkciók, amelyek követi zárójelben argumentumok nevét kell meghatározni: <br>
  *Függvénynév (`<<argument 1>>`,`<<argument N>>`)*
* Függvények beágyazhatók egymásba előfordulhat, hogy ágyazhatók egymásba. Például: <br> *FunctionOne (FunctionTwo (`<<argument1>>`))*
* Az funkciók három eltérő típusú argumentumokat adhat át:
  
  1. Attribútumok, amelyek szögletes zárójelek közé kell tenni. Például: [attributeName]
  2. A karakterlánc-állandókat, amelyek dupla idézőjelek közé kell tenni. Például: "Egyesült Államok"
  3. Más funkciók. Például: FunctionOne (`<<argument1>>`, FunctionTwo (`<<argument2>>`))
* A karakterlánc-állandókat Ha egy fordított perjel (\) vagy az idézőjel (") a karakterláncban van szüksége, kell megjelölni a fordított perjel (\) szimbólum. Például: "cég neve: \\" contoso\\""

## <a name="list-of-functions"></a>Függvények listája
| Függvények listája | Leírás |
|-----|----|
|[Hozzáfűzése](#append)|Forrás karakterlánc értéket vesz fel, és az utótagot fűz a végén.|
|[BitAnd](#bitand)|A BitAnd függvény a megadott biteket egy értékre állítja be.|
|[CBool](#cbool)|A CBool függvény egy logikai értéket ad vissza a kiértékelt kifejezés alapján.|
|[ConvertFromBase64](#convertfrombase64)|A ConvertFromBase64 függvény a megadott Base64 kódolású értéket egy normál karakterlánccá alakítja át.|
|[ConvertToBase64](#converttobase64)|A ConvertToBase64 függvény egy karakterláncot Unicode Base64 karakterlánccá alakít át. |
|[ConvertToUTF8Hex](#converttoutf8hex)|A ConvertToUTF8Hex függvény egy karakterláncot UTF8 hexadecimális kódolású értékre alakít át.|
|[Száma](#count)|A Count függvény egy többértékű attribútum elemeinek számát adja vissza.|
|[CStr](#cstr)|A CStr függvény karakterlánc típusú adattípusra konvertál.|
|[DateFromNum](#datefromnum)|A DateFromNum függvény egy értéket AD meg az AD dátumformátum dátum és idő típusára.|
|[DNComponent](#dncomponent)|A DNComponent függvény a megadott DN-összetevő értékét adja vissza balról.|
|[Hiba](#error)|A Error függvény egyéni hiba visszaküldésére szolgál.|
|[FormatDateTime](#formatdatetime) |Egy adott formátumból egy dátum karakterláncot vesz fel, és a egy másik formátumba konvertálja.| 
|[GUID](#guid)|A függvény GUID-azonosítója új véletlenszerű GUID azonosítót hoz létre.|           
|[IIF](#iif)|Az IIF függvény a lehetséges értékek egy halmazát adja vissza egy megadott feltétel alapján.|
|[InStr](#instr)|A beosztási függvény megkeresi egy karakterlánc első előfordulását.|
|[IsNull](#isnull)|Ha a kifejezés értéke null, akkor a IsNull függvény Igaz értéket ad vissza.|
|[IsNullOrEmpty](#isnullorempty)|Ha a kifejezés null értékű vagy üres karakterlánc, akkor a IsNullOrEmpty függvény Igaz értéket ad vissza.|         
|[IsPresent](#ispresent)|Ha a kifejezés olyan karakterláncot ad vissza, amely nem null értékű, és nem üres, akkor a IsPresent függvény Igaz értéket ad vissza.|    
|[IsString](#isstring)|Ha a kifejezés kiértékelhető karakterlánc típusúra, akkor a IsString függvény Igaz értéket ad vissza.|
|[Elem](#item)|Az Item függvény egy elemet ad vissza egy többértékű karakterlánc/attribútumból.|
|[Csatlakozás](#join) |A JOIN () hasonló a hozzáfűzéshez (), azzal a kivétellel, hogy több **forrás** sztringet is egyesít egyetlen karakterláncban, és az egyes értékeket **elválasztó sztring választja** el egymástól.| 
|[Bal](#left)|A Left függvény egy karakterlánctól balra megadott számú karaktert ad vissza.|
|[Közepes](#mid) |Az érték egy részét adja vissza. Egy karakterláncrészletet csak egy részét a forrás-karakterlánc származó karaktereket tartalmazó karakterláncot.|
|[NormalizeDiacritics](#normalizediacritics)|Egy karakterlánc-argumentum szükséges. A karakterláncot ad vissza, de bármilyen diakritikus karakterrel egyenértékű nem diakritikus karakterek cserélni.|
|[Nem](#not) |Megfordítja a **forrás**logikai értékét. Ha a **forrás** értéke "*true*" (igaz), a "*false*" értéket adja vissza. Ellenkező esetben az "*igaz*" értéket adja vissza.| 
|[RemoveDuplicates](#removeduplicates)|A RemoveDuplicates függvény többértékű karakterláncot használ, és minden érték egyedi.| 
|[Csere](#replace) |Lecseréli az értékeket egy karakterláncból. | 
|[SelectUniqueValue](#selectuniquevalue)|Legalább két argumentumot, amelyek egyedi érték létrehozási szabályok definiált kifejezések használatával van szükség. A függvény minden egyes szabály kiértékeli, és ellenőrzi, az érték egyedi-e a cél alkalmazás/könyvtárban jönnek létre.| 
|[SingleAppRoleAssignment](#singleapproleassignment)|Egyetlen appRoleAssignment ad vissza egy adott alkalmazás felhasználóhoz rendelt összes appRoleAssignments listájáról.| 
|[Felosztása](#split)|A karakterláncot egy többértékű tömbre osztja fel a megadott elválasztó karakter használatával.|
|[StringFromSID](#stringfromsid)|A StringFromSid függvény egy olyan byte tömböt alakít át, amely biztonsági azonosítót tartalmaz egy karakterláncra.| 
|[StripSpaces](#stripspaces) |Eltávolítja az összes szóközt ("") karaktert a forrás karakterláncot.| 
|[Kapcsoló](#switch)|Ha a **forrás** értéke megegyezik egy **kulccsal**, az adott **kulcs** **értékét** adja vissza. | 
|[ToLower](#tolower)|Egy *forrás* sztring értéket vesz igénybe, és a megadott kulturális szabályok alapján átalakítja a kisbetűsre.| 
|[ToUpper](#toupper)|Egy *forrás* sztring értékét veszi át, és a megadott kulturális szabályok alapján átalakítja a nagybetűre.|
|[Trim](#trim)|A Trim függvény eltávolítja a kezdő és záró szóközöket egy karakterláncból.|
|[Word](#word)|A Word függvény egy karakterláncon belül található szót ad vissza, amely a használni kívánt határolójeleket és a visszaadni kívánt szó paramétereit írja le.|

---
### <a name="append"></a>Hozzáfűzés
**Függvény**<br> Append(Source, suffix)

**Leírás**<br> Forrás karakterlánc értéket vesz fel, és az utótagot fűz a végén.

**Paraméterek**<br> 

   | Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
   | --- | --- | --- | --- |
   | **forrás** |Kötelező |Sztring |Általában az attribútum neve, az adatforrás-objektum. |
   | **utótag** |Kötelező |Sztring |Az adatforrás-értéke végére hozzáfűzni kívánt karakterlánc. |

---
### <a name="bitand"></a>BitAnd
**Leírás**  
A BitAnd függvény a megadott biteket egy értékre állítja be.

**Szintaxis**  
`num BitAnd(num value1, num value2)`

* érték1, érték2: numerikus értékek, amelyeket össze kell AND'ed

**Megjegyzéseket tartalmazó**  
Ez a függvény mindkét paramétert a bináris ábrázolásra konvertálja, és egy kis mennyiséget állít be a következőre:

* 0 – Ha a *érték1* -ben és a *érték2* -ben található megfelelő bitek közül egyet vagy mindkettőt 0
* 1 – Ha a megfelelő bitek mindegyike 1.

Más szóval a 0 értéket adja vissza minden esetben, kivéve, ha mindkét paraméternek megfelelő bitek értéke 1.

**Példa**  
 
 `BitAnd(&HF, &HF7)`</br>
 A 7 értéket adja vissza, mert a hexadecimális "F" és "F7" kifejezés kiértékelése erre az értékre történik.

---

### <a name="cbool"></a>CBool
**Leírás**  
A CBool függvény egy logikai értéket ad vissza a kiértékelt kifejezés alapján.

**Szintaxis**  
`bool CBool(exp Expression)`

**Megjegyzéseket tartalmazó**  
Ha a kifejezés kiértékelése nem nulla értékű, akkor a CBool igaz értéket ad vissza, máskülönben hamis értéket ad vissza.

**Példa**  
`CBool([attrib1] = [attrib2])`  

Igaz értéket ad vissza, ha mindkét attribútum ugyanazzal az értékkel rendelkezik.

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**Leírás**  
A ConvertFromBase64 függvény a megadott Base64 kódolású értéket egy normál karakterlánccá alakítja át.

**Szintaxis**  
`str ConvertFromBase64(str source)` – Unicode-ot feltételez a kódoláshoz  
`str ConvertFromBase64(str source, enum Encoding)`

* Forrás: Base64 kódolású karakterlánc  
* Kódolás: Unicode, ASCII, UTF8

**Példa**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Mindkét példa "*Helló világ!* " értéket ad vissza.

---
### <a name="converttobase64"></a>ConvertToBase64
**Leírás**  
A ConvertToBase64 függvény egy karakterláncot Unicode Base64 karakterlánccá alakít át.  
Egész számokból álló tömb értékét konvertálja az egyenértékű karakterlánc-ábrázolásra, amely Base-64 számjegyekkel van kódolva.

**Szintaxis**  
`str ConvertToBase64(str source)`

**Példa**  
`ConvertToBase64("Hello world!")`  
Returns "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Leírás**  
A ConvertToUTF8Hex függvény egy karakterláncot UTF8 hexadecimális kódolású értékre alakít át.

**Szintaxis**  
`str ConvertToUTF8Hex(str source)`

**Megjegyzéseket tartalmazó**  
A függvény kimeneti formátumát a Azure Active Directory használja DN-attribútum formátumként.

**Példa**  
`ConvertToUTF8Hex("Hello world!")`  
48656C6C6F20776F726C6421 visszaadása

---
### <a name="count"></a>Darabszám
**Leírás**  
A Count függvény egy többértékű attribútum elemeinek számát adja vissza.

**Szintaxis**  
`num Count(mvstr attribute)`

---
### <a name="cstr"></a>CStr
**Leírás**  
A CStr függvény karakterlánc típusú adattípusra konvertál.

**Szintaxis**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* Value: numerikus érték, hivatkozási attribútum vagy logikai érték lehet.

**Példa**  
`CStr([dn])`  
Visszatérhet a "CN = Joe, DC = contoso, DC = com" értékre.

---
### <a name="datefromnum"></a>DateFromNum
**Leírás**  
A DateFromNum függvény egy értéket AD meg az AD dátumformátum dátum és idő típusára.

**Szintaxis**  
`dt DateFromNum(num value)`

**Példa**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
A 2012-01-01 23:00:00-et jelölő DateTime értéket ad vissza.

---
### <a name="dncomponent"></a>DNComponent
**Leírás**  
A DNComponent függvény a megadott DN-összetevő értékét adja vissza balról.

**Szintaxis**  
`str DNComponent(ref dn, num ComponentNumber)`

* DN: az értelmezni kívánt hivatkozási attribútum
* ComponentNumber: a DN által visszaadott összetevő

**Példa**  
`DNComponent(CRef([dn]),1)`  
Ha a DN a következő: "CN = Joe, ou =...", visszaadja a következőt: Joe

---
### <a name="error"></a>Hiba
**Leírás**  
A Error függvény egyéni hiba visszaküldésére szolgál.

**Szintaxis**  
`void Error(str ErrorMessage)`

**Példa**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Ha a accountName attribútum nem található, hibát jelez az objektumon.

---
### <a name="formatdatetime"></a>formatDateTime
**Függvény**<br> FormatDateTime (forrás, inputFormat, outputFormat)

**Leírás**<br> Egy adott formátumból egy dátum karakterláncot vesz fel, és a egy másik formátumba konvertálja.

**Paraméterek**<br> 

   | Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
   | --- | --- | --- | --- |
   | **forrás** |Kötelező |Sztring |Általában az attribútum neve, az adatforrás-objektum. |
   | **inputFormat** |Kötelező |Sztring |Az adatforrás-értéke formátumával. Támogatott formátumok: [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
   | **outputFormat** |Kötelező |Sztring |A kimeneti dátum formátuma. |

---
### <a name="guid"></a>Guid
**Leírás**  
A függvény GUID-azonosítója új véletlenszerű GUID azonosítót hoz létre

**Szintaxis**  
`str Guid()`

---
### <a name="iif"></a>IIF
**Leírás**  
Az IIF függvény a lehetséges értékek egy halmazát adja vissza egy megadott feltétel alapján.

**Szintaxis**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* feltétel: bármely olyan érték vagy kifejezés, amelynek kiértékelése igaz vagy hamis lehet.
* valueIfTrue: Ha a feltétel igaz értéket ad vissza, a visszaadott érték.
* valueIfFalse: Ha a feltétel hamis értéket ad vissza, a visszaadott érték.

**Példa**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Ha a felhasználó egy gyakornok, a a "t-" értékkel rendelkező felhasználó aliasát adja vissza, a másik pedig a felhasználó aliasát adja vissza.

---
### <a name="instr"></a>InStr
**Leírás**  
A beosztási függvény megkeresi egy karakterláncban szereplő alsztring első előfordulását.

**Szintaxis**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* karakterlánc: keresendő karakterlánc
* keresettkarakterlánc: a rendszer megtalálni kívánt karakterláncot
* Kezdés: a pozíció megkeresése az alsztring megtalálásához
* összehasonlítás: vbTextCompare vagy vbBinaryCompare

**Megjegyzéseket tartalmazó**  
Azt a pozíciót adja vissza, ahol az alkarakterlánc található vagy 0, ha nem található.

**Példa**  
`InStr("The quick brown fox","quick")`  
Evalues – 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Értékelés 7-re

---
### <a name="isnull"></a>IsNull
**Leírás**  
Ha a kifejezés értéke null, akkor a IsNull függvény Igaz értéket ad vissza.

**Szintaxis**  
`bool IsNull(var Expression)`

**Megjegyzéseket tartalmazó**  
Attribútum esetén a null értéket az attribútum hiánya fejezi ki.

**Példa**  
`IsNull([displayName])`  
Igaz értéket ad vissza, ha az attribútum nem szerepel a CS vagy az MV-ban.

---
### <a name="isnullorempty"></a>IsNullOrEmpty
**Leírás**  
Ha a kifejezés null értékű vagy üres karakterlánc, akkor a IsNullOrEmpty függvény Igaz értéket ad vissza.

**Szintaxis**  
`bool IsNullOrEmpty(var Expression)`

**Megjegyzéseket tartalmazó**  
Egy attribútum esetében ez igaz értékre értékeli, ha az attribútum hiányzik vagy létezik, de egy üres karakterlánc.  
A függvény inverzének neve IsPresent.

**Példa**  
`IsNullOrEmpty([displayName])`  
Igaz értéket ad vissza, ha az attribútum nincs jelen, vagy üres karakterlánc a CS vagy az MV.

---
### <a name="ispresent"></a>IsPresent
**Leírás**  
Ha a kifejezés olyan karakterláncot ad vissza, amely nem null értékű, és nem üres, akkor a IsPresent függvény Igaz értéket ad vissza.

**Szintaxis**  
`bool IsPresent(var expression)`

**Megjegyzéseket tartalmazó**  
A függvény inverzének neve IsNullOrEmpty.

**Példa**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Elem
**Leírás**  
Az Item függvény egy elemet ad vissza egy többértékű karakterlánc/attribútumból.

**Szintaxis**  
`var Item(mvstr attribute, num index)`

* attribútum: többértékű attribútum
* index: index a Többértékű karakterlánc egyik elemébe.

**Megjegyzéseket tartalmazó**  
Az Item függvény a tartalmaz függvénnyel együtt használható, mivel az utóbbi függvény az indexet a többértékű attribútum egy elemébe adja vissza.

Hibát jelez, ha az index tartományon kívül esik.

**Példa**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Az elsődleges e-mail-címet adja vissza.

---
### <a name="isstring"></a>IsString
**Leírás**  
Ha a kifejezés kiértékelhető karakterlánc típusúra, akkor a IsString függvény Igaz értéket ad vissza.

**Szintaxis**  
`bool IsString(var expression)`

**Megjegyzéseket tartalmazó**  
Annak megállapítására szolgál, hogy a CStr () sikeres lehet-e a kifejezés értelmezéséhez.

---
### <a name="join"></a>Csatlakozás
**Függvény**<br> Csatlakozás (elválasztó, source1, source2,...)

**Leírás**<br> A JOIN () hasonló a hozzáfűzéshez (), azzal a kivétellel, hogy több **forrás** sztringet is egyesít egyetlen karakterláncban, és az egyes értékeket **elválasztó sztring választja** el egymástól.

Ha a forrásadatok egyike egy többértékű attribútum, akkor az adott attribútum minden értéke együtt lesz egyesítve, az elválasztó értékkel elválasztva.

**Paraméterek**<br> 

   | Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
   | --- | --- | --- | --- |
   | **elválasztó** |Kötelező |Sztring |Forrásértékek elválasztó, amikor azok vannak összefűzött egy karakterlánc, karakterlánc. Lehet "", ha nincs elválasztó nem szükséges. |
   | **source1 ... sourceN** |Szükség esetén a változó-hányszor |Sztring |A karakterlánc-értékek egymáshoz csatlakoztatni. |

---
### <a name="left"></a>Bal
**Leírás**  
A Left függvény egy karakterlánctól balra megadott számú karaktert ad vissza.

**Szintaxis**  
`str Left(str string, num NumChars)`

* string: az a karakterlánc, amelyből karaktereket kell visszaadni
* NumChars: a sztring elejétől (balra) visszaadott karakterek számát azonosító szám.

**Megjegyzéseket tartalmazó**  
A karakterlánc első numChars karaktert tartalmazó karakterlánc:

* Ha a numChars = 0, üres karakterláncot ad vissza.
* Ha a numChars < 0 értéket, adja vissza a bemeneti karakterláncot.
* Ha a karakterlánc null értékű, üres karakterláncot ad vissza.

Ha a sztring kevesebb karaktert tartalmaz a numChars megadott számnál, akkor a rendszer a karakterlánctal megegyező karakterláncot (azaz az 1. paraméterben szereplő összes karaktert tartalmazza) adja vissza.

**Példa**  
`Left("John Doe", 3)`  
`Joh`visszaadása.

---
### <a name="mid"></a>Mid
**Függvény**<br> Mid (forrás, indítás, hossz)

**Leírás**<br> Az érték egy részét adja vissza. Egy karakterláncrészletet csak egy részét a forrás-karakterlánc származó karaktereket tartalmazó karakterláncot.

**Paraméterek**<br> 

   | Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
   | --- | --- | --- | --- |
   | **forrás** |Kötelező |Sztring |Általában az attribútum neve. |
   | **start** |Kötelező |egész szám |Az index a **forrás** sztringben, ahol az alsztringnek el kell indulnia. A karakterlánc első karaktere 1 index fog rendelkezni, a második karaktere index 2 rendelkezik, és így tovább. |
   | **hossza** |Kötelező |egész szám |A karakterláncrész hossza. Ha a hossz a **forrás** sztringen kívülre végződik, a függvény a **kezdő** indexből származó alsztringet ad vissza a **forrás** sztring végéig. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Függvény**<br> NormalizeDiacritics(source)

**Leírás**<br> Egy karakterlánc-argumentum szükséges. A karakterláncot ad vissza, de bármilyen diakritikus karakterrel egyenértékű nem diakritikus karakterek cserélni. Jogi különböző felhasználói azonosítókat, például az egyszerű felhasználónevek, SAM-fiók nevét és e-mail címek felhasználható értékekké jellemzően keresztnevét és vezetéknevét diakritikus karaktert (ékezetes) konvertálni.

**Paraméterek**<br> 

   | Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
   | --- | --- | --- | --- |
   | **forrás** |Kötelező |Sztring | Általában utónév vagy vezetéknév attribútum. |

---
### <a name="not"></a>Not
**Függvény**<br> Not(Source)

**Leírás**<br> Megfordítja a **forrás**logikai értékét. Ha a **forrás** értéke "*true*" (igaz), a "*false*" értéket adja vissza. Ellenkező esetben az "*igaz*" értéket adja vissza.

**Paraméterek**<br> 

   | Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
   | --- | --- | --- | --- |
   | **forrás** |Kötelező |Logikai típusú karakterlánc |A várt **források** értéke "true" vagy "false". |

---
### <a name="removeduplicates"></a>RemoveDuplicates
**Leírás**  
A RemoveDuplicates függvény többértékű karakterláncot használ, és minden érték egyedi.

**Szintaxis**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Példa**  
`RemoveDuplicates([proxyAddresses])`  
Egy megtisztított proxyAddress attribútumot ad vissza, amelyben az összes duplikált érték el lett távolítva.

---
### <a name="replace"></a>Csere
**Függvény**<br> Cserélje le a (forrás, oldValue, regexPattern, regexGroupName, helyettesítő értéke, replacementAttributeName, sablon)

**Leírás**<br>
Lecseréli az értékeket egy karakterláncból. A megadott paraméterek függően eltérően működik:

* A **OldValue** és a **replacementValue** megadása esetén:
  
  * Lecseréli a **forrásban** lévő **OldValue** összes előfordulását a **replacementValue**
* A **OldValue** és a **sablon** megadásakor:
  
  * A **sablonban** lévő **OldValue** összes előfordulását lecseréli a **forrás** értékkel
* A **regexPattern** és a **replacementValue** megadása esetén:

  * A függvény a **regexPattern** alkalmazza a **forrás** sztringre, és a regex-csoportok nevét használhatja a **replacementValue** karakterlánc létrehozásához.
* A **regexPattern**, a **regexGroupName**és a **replacementValue** megadásakor:
  
  * A függvény a **regexPattern** alkalmazza a **forrás** sztringre, és lecseréli az összes olyan értéket, amely megfelel a **regexGroupName** a **replacementValue**
* A **regexPattern**, a **regexGroupName**és a **replacementAttributeName** megadásakor:
  
  * Ha a **forrásnak** nincs értéke, a rendszer visszaadja a **forrást** .
  * Ha a **forrás** értékkel rendelkezik, a függvény a **regexPattern** alkalmazza a **forrás** sztringre, és lecseréli az összes olyan értéket, amely megfelel a **regexGroupName** a **replacementAttributeName** társított értéknek.

**Paraméterek**<br> 

   | Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
   | --- | --- | --- | --- |
   | **forrás** |Kötelező |Sztring |Az attribútum neve általában a **forrásoldali** objektumban. |
   | **oldValue** |Optional |Sztring |A **forrásban** vagy **sablonban**cserélni kívánt érték. |
   | **regexPattern** |Optional |Sztring |A **forrásban**lecserélni kívánt érték regex-mintája. Vagy ha **replacementPropertyName** használ, a **replacementPropertyName**származó érték kinyerésére szolgáló minta. |
   | **regexGroupName** |Optional |Sztring |A csoport neve a **regexPattern**belül. Csak **replacementPropertyName** használata esetén a csoport értékének kinyerése a **replacementPropertyName** **replacementValue** történik. |
   | **replacementValue** |Optional |Sztring |Új értéket cserélje le a régit. |
   | **replacementAttributeName** |Optional |Sztring |A helyettesítő értékhez használandó attribútum neve |
   | **sablon** |Optional |Sztring |Ha meg van adni a **sablon** értéke, megkeresjük a **OldValue** a sablonon belül, és lecseréljük a **forrás** értékre. |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Függvény**<br> SelectUniqueValue (uniqueValueRule1, uniqueValueRule2, uniqueValueRule3,...)

**Leírás**<br> Legalább két argumentumot, amelyek egyedi érték létrehozási szabályok definiált kifejezések használatával van szükség. A függvény minden egyes szabály kiértékeli, és ellenőrzi, az érték egyedi-e a cél alkalmazás/könyvtárban jönnek létre. Az első egyedi érték található egy adja vissza. Összes érték már létezik a célkiszolgálón, ha a bejegyzés lesz első szétválasztást és okát az auditnaplókban rendszer naplózza. Nincs megadható argumentumok számának felső korlátja.

> [!NOTE]
> - Ez egy legfelső szintű függvényt, nem ágyazhatók egymásba.
> - Ez a függvény nem alkalmazható olyan attribútumokra, amelyek egyező elsőbbséggel rendelkeznek.  
> - Ez a függvény csak hivatott bejegyzés létrehozások használható. Ha attribútummal használja, állítsa a **leképezés alkalmazása** tulajdonságot csak az **objektum létrehozása során**.
> - Ez a függvény jelenleg csak a "munkanap Active Directory a felhasználók kiosztásához" támogatott. Más kiépítési alkalmazásokkal nem használható. 


**Paraméterek**<br> 

   | Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
   | --- | --- | --- | --- |
   | **uniqueValueRule1 ... uniqueValueRuleN** |Legalább a 2 szükséges, nem felső határérték |Sztring | A kiértékelni kívánt egyedi érték-létrehozási szabályok listája. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Függvény**<br> SingleAppRoleAssignment([appRoleAssignments])

**Leírás**<br> Egyetlen appRoleAssignment ad vissza egy adott alkalmazás felhasználóhoz rendelt összes appRoleAssignments listájáról. Ez a függvény szükséges ahhoz, hogy a appRoleAssignments objektumot egyetlen szerepkör-nevet megadó karakterláncba alakítsa át. Vegye figyelembe, hogy az ajánlott eljárás annak biztosítása, hogy egyszerre csak egy appRoleAssignment legyen hozzárendelve egy felhasználóhoz, és ha több szerepkör van hozzárendelve, a visszaadott szerepkör-karakterlánc nem lehet előre jelezhető. 

**Paraméterek**<br> 

  | Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
  |--- | --- | --- | --- |
  | **AppRoleAssignments** |Kötelező |Sztring |**[appRoleAssignments]** objektum. |

---
### <a name="split"></a>Felosztás
**Függvény**<br> Felosztás (forrás, elválasztó karakter)

**Leírás**<br> A karakterláncot egy többértékű tömbre osztja fel a megadott elválasztó karakter használatával.

**Paraméterek**<br> 

   | Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
   | --- | --- | --- | --- |
   | **forrás** |Kötelező |Sztring |a frissítendő **forrás** értéke. |
   | **elválasztó** |Kötelező |Sztring |Meghatározza a karakterlánc felosztására szolgáló karaktert (példa: ",") |

---
### <a name="stringfromsid"></a>StringFromSid
**Leírás**  
A StringFromSid függvény egy olyan byte tömböt alakít át, amely biztonsági azonosítót tartalmaz egy karakterláncra.

**Szintaxis**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="stripspaces"></a>StripSpaces
**Függvény**<br> StripSpaces(source)

**Leírás**<br> Eltávolítja az összes szóközt ("") karaktert a forrás karakterláncot.

**Paraméterek**<br> 

   | Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
   | --- | --- | --- | --- |
   | **forrás** |Kötelező |Sztring |a frissítendő **forrás** értéke. |

---
### <a name="switch"></a>Kapcsoló
**Függvény**<br> Switch (forrás, defaultValue, 1. kulcs, érték1, 2. kulcs, value2,...)

**Leírás**<br> Ha a **forrás** értéke megegyezik egy **kulccsal**, az adott **kulcs** **értékét** adja vissza. Ha a **forrás** értéke nem felel meg a kulcsoknak, a a **defaultValue**értéket adja vissza.  A **kulcsok** és **értékek** paramétereit mindig párokban kell megadni. A függvény mindig paraméterek páros számú vár.

**Paraméterek**<br> 

   | Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
   | --- | --- | --- | --- |
   | **forrás** |Kötelező |Sztring |Az ellenőrzési **forrás** értéke. |
   | **defaultValue** |Optional |Sztring |Alapértelmezett érték használható, ha a forrás nem felel meg minden olyan kulcsokat. Üres karakterlánc lehet (""). |
   | **key** |Kötelező |Sztring |A **kulcs** a **forrás** értékének összehasonlításához a következővel:. |
   | **value** |Kötelező |Sztring |A kulcsnak megfelelő **forrás** helyettesítő értéke. |

---
### <a name="tolower"></a>toLower
**Függvény**<br> ToLower (forrás, kulturális környezet)

**Leírás**<br> Egy *forrás* sztring értéket vesz igénybe, és a megadott kulturális szabályok alapján átalakítja a kisbetűsre. Ha nincs megadva *kulturális* információ, akkor a rendszer a semleges kultúrát fogja használni.

**Paraméterek**<br> 

   | Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
   | --- | --- | --- | --- |
   | **forrás** |Kötelező |Sztring |Általában az attribútum az az adatforrás-objektum neve |
   | **kulturális környezet** |Optional |Sztring |Az RFC 4646 alapján a kulturális név formátuma *languagecode2-ország/regioncode2*, ahol a *languagecode2* a kétbetűs nyelvi kód, az *ország/regioncode2* pedig a kétbetűs alkulturális kód. Ilyenek például a japán (Japán) és az en-US angol (Egyesült Államok). Azokban az esetekben, amikor a kétbetűs nyelvi kód nem érhető el, az ISO 639-2-ből származtatott hárombetűs kód van használatban.|

---

### <a name="toupper"></a>toUpper
**Függvény**<br> ToUpper (forrás, kulturális környezet)

**Leírás**<br> Egy *forrás* sztring értékét veszi át, és a megadott kulturális szabályok alapján átalakítja a nagybetűre. Ha nincs megadva *kulturális* információ, akkor a rendszer a semleges kultúrát fogja használni.

**Paraméterek**<br> 

  | Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
  | --- | --- | --- | --- |
  | **forrás** |Kötelező |Sztring |Általában az attribútum neve, az adatforrás-objektum. |
  | **kulturális környezet** |Optional |Sztring |Az RFC 4646 alapján a kulturális név formátuma *languagecode2-ország/regioncode2*, ahol a *languagecode2* a kétbetűs nyelvi kód, az *ország/regioncode2* pedig a kétbetűs alkulturális kód. Ilyenek például a japán (Japán) és az en-US angol (Egyesült Államok). Azokban az esetekben, amikor a kétbetűs nyelvi kód nem érhető el, az ISO 639-2-ből származtatott hárombetűs kód van használatban.|

---

### <a name="trim"></a>Trim
**Leírás**  
A Trim függvény eltávolítja a kezdő és záró szóközöket egy karakterláncból.

**Szintaxis**  
`str Trim(str value)`  

**Példa**  
`Trim(" Test ")`  
A "test" értéket adja vissza.

`Trim([proxyAddresses])`  
Eltávolítja a kezdő és záró szóközöket a proxyAddress attribútum minden értékéhez.

---
### <a name="word"></a>Word
**Leírás**  
A Word függvény egy karakterláncon belül található szót ad vissza, amely a használni kívánt határolójeleket és a visszaadni kívánt szó paramétereit írja le.

**Szintaxis**  
`str Word(str string, num WordNumber, str delimiters)`

* string: az a karakterlánc, amelyből a szót vissza kell adni.
* WordNumber: az a szám, amelyből a szót vissza kell adni.
* határolójelek: a szavak azonosítására szolgáló elválasztó karakter (eke) t jelölő sztring

**Megjegyzéseket tartalmazó**  
A rendszer az elválasztó karakterek egyikével elválasztott karakterláncban szereplő összes karakterláncot szavakként azonosítja:

* Ha a szám < 1, üres karakterláncot ad vissza.
* Ha a sztring null, üres karakterláncot ad vissza.

Ha a karakterlánc kevesebb, mint szám szót tartalmaz, vagy a sztring nem tartalmaz határolójelekkel azonosított szavakat, akkor a rendszer üres karakterláncot ad vissza.

**Példa**  
`Word("The quick brown fox",3," ")`  
A "barna" értéket adja vissza.

`Word("This,string!has&many separators",3,",!&#")`  
Visszatérési érték: "has"

## <a name="examples"></a>Példák
### <a name="strip-known-domain-name"></a>Sáv ismert tartománynév
Szerezzen be egy felhasználónevet a felhasználó e-mailben egy ismert tartománynévnek sáv kell. <br>
Például ha a tartomány a "contoso.com", majd használhatja a következő kifejezést:

**Kifejezés** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Minta bemenet/kimenet:** <br>

* **Bemenet** (e-mail): "john.doe@contoso.com"
* **Kimenet**: "John. DOE"

### <a name="append-constant-suffix-to-user-name"></a>Állandó utótagok hozzáfűzése a felhasználónevet
Ha egy Salesforce védőfal használja, szüksége lehet egy további utótagok hozzáfűzése a felhasználónevek őket szinkronizálása előtt.

**Kifejezés** <br>
`Append([userPrincipalName], ".test")`

**Minta bemenet/kimenet:** <br>

* **Bemenet**: (userPrincipalName): "John.Doe@contoso.com"
* **Kimenet**: "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Utónév és Vezetéknév összetűzésének részei felhasználói alias létrehozása
Létre kell hoznia egy felhasználói alias, a felhasználó utónevét első 3 betűket és a felhasználó vezetéknevét első 5 karakterét végrehajtásával.

**Kifejezés** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Minta bemenet/kimenet:** <br>

* **Bemenet** (givenName): "John"
* **Bemenet** (vezetéknév): "DOE"
* **Kimenet**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Távolítsa el az e mellékjeleket egy karakterláncból.
Cserélje le a megfelelő karakterek, ékezetes nem tartalmazó ékezetes karakter hosszúságúnak kell.

**Kifejezés** <br>
NormalizeDiacritics([givenName])

**Minta bemenet/kimenet:** <br>

* **Bemenet** (givenName): "Zoë"
* **Kimenet**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Sztring felosztása többértékű tömbbe
A karakterláncok vesszővel tagolt listáját kell megadnia, és azokat egy olyan tömbbe kell bontani, amely egy többértékű attribútumhoz, például a Salesforce PermissionSets attribútumához csatlakoztatható. Ebben a példában az extensionAttribute5 az Azure AD-ben az engedélyezési készletek listája lett feltöltve.

**Kifejezés** <br>
Split ([extensionAttribute5], ",")

**Minta bemenet/kimenet:** <br>

* **Bemenet** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **Kimenet**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Egy bizonyos formátumú karakterlánc formájában kimeneti dátum
Egy SaaS-alkalmazás egy bizonyos formátumú dátumok küldeni szeretné. <br>
Ha például szeretné formátumához servicenow-hoz készült.

**Kifejezés** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Minta bemenet/kimenet:**

* **Bemenet** (extensionAttribute1): "20150123105347.1 z"
* **Kimenet**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Cserélje le az előre megadott beállítások alapján értéket

Adja meg a felhasználó az Azure AD-ben tárolt kódja alapján az időzóna kell. <br>
Ha az állapot-kód nem egyezik az előre definiált beállításokat, használja a "Ausztrália/Sydney" alapértelmezett értékét.

**Kifejezés** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Minta bemenet/kimenet:**

* **Bemenet** (állapot): "QLD"
* **Kimenet**: "Ausztrália/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Karakterek cseréje reguláris kifejezés használatával
Meg kell keresnie a reguláris kifejezés értékének megfelelő karaktereket, és el kell távolítani őket.

**Kifejezés** <br>

Replace ([mailNickname],, "[a-zA-Z_] *",, "",,)

**Minta bemenet/kimenet:**

* **Bemenet** (mailNickname: "john_doe72"
* **Kimenet**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Generált userPrincipalName (UPN) értékének kisbetűvé alakítása
Az alábbi példában az UPN-érték a PreferredFirstName és a PreferredLastName forrás mezőinek összefűzésével jön létre, és a ToLower függvény a generált karakterláncon működik, hogy az összes karaktert kisbetűvé alakítsa. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Minta bemenet/kimenet:**

* **Bemenet** (PreferredFirstName): "John"
* **Bemenet** (PreferredLastName): "Kovács"
* **Kimenet**: "john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Egyedi érték a userPrincipalName (UPN) attribútum létrehozása
Alapján a felhasználó utónevét, a középső név és vezetéknevét, létre kell hoznia az UPN attribútum értékét, és ellenőrizze az egyedi-e a cél az AD címtárban az UPN attribútum értéke hozzárendelése előtt.

**Kifejezés** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Minta bemenet/kimenet:**

* **Bemenet** (PreferredFirstName): "John"
* **Bemenet** (PreferredLastName): "Kovács"
* **Kimenet**: "John.Smith@contoso.com", ha John.Smith@contoso.com UPN-értéke még nem létezik a címtárban.
* **Kimenet**: "J.Smith@contoso.com", ha John.Smith@contoso.com UPN-értéke már létezik a címtárban
* **Kimenet**: "Jo.Smith@contoso.com", ha a fenti két UPN-érték már létezik a címtárban


## <a name="next-steps"></a>Következő lépések 

- [Mi a kiépítés?](what-is-provisioning.md)
- [Mi az Azure AD Connect Cloud kiépítés?](what-is-cloud-provisioning.md)
