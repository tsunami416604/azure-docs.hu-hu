---
title: Kifejezések írása az attribútum-hozzárendelésekhez az Azure AD-ben
description: Ismerje meg, hogyan SaaS-alkalmazás objektumok az Azure Active Directoryban az Automatikus kiépítés során elfogadható formátumot attribútumértékek alakítsa át a kifejezés-leképezések használatával.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd78c78a711b64c58290f09eb2ee52263375002f
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77522509"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Kifejezések írása attribútumleképezéshez az Azure Active Directoryban
Amikor konfigurál egy SaaS-alkalmazáshoz való üzembe helyezést, az Ön által megadott attribútum-leképezéshez típusú egyik egy kifejezés-hozzárendelést. Ezeknél a parancsfájl-szerű kifejezés, amely lehetővé teszi, hogy a felhasználók adatokat alakíthatja, amelyek esetében a SaaS-alkalmazás több elfogadható formátumok kell írnia.

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

## <a name="list-of-functions"></a>Függvények listája.
[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [BitAnd](#bitand) &nbsp;&nbsp;&nbsp;&nbsp; [CBool](#cbool) &nbsp;&nbsp;&nbsp;&nbsp; [Coalesce](#coalesce) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToBase64](#converttobase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [Count](#count) &nbsp;&nbsp;&nbsp;&nbsp; [CStr](#cstr) &nbsp;&nbsp;&nbsp;&nbsp; [DateFromNum](#datefromnum) &nbsp;[FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Guid](#guid) &nbsp;&nbsp;&nbsp;&nbsp; [InStr](#instr) &nbsp;&nbsp;&nbsp;&nbsp; [IsNull](#isnull) &nbsp;&nbsp;&nbsp;&nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsPresent](#ispresent) &nbsp;&nbsp;&nbsp;&nbsp; [IsString](#isstring) &nbsp;&nbsp;&nbsp;&nbsp; [Item](#item) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Left](#left) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [NumFromDate](#numfromdate) &nbsp;&nbsp;&nbsp;&nbsp;[RemoveDuplicates](#removeduplicates) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)&nbsp;&nbsp;&nbsp;&nbsp; [Word](#word)

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
**Függvény**<br> BitAnd (érték1, érték2)

**Leírás**<br> Ez a függvény mindkét paramétert a bináris ábrázolásra konvertálja, és egy kis mennyiséget állít be a következőre:

0 – Ha a érték1-ben és a érték2-ben található megfelelő bitek közül egyet vagy mindkettőt 0                                                  
1 – Ha a megfelelő bitek mindegyike 1.                                    

Más szóval a 0 értéket adja vissza minden esetben, kivéve, ha mindkét paraméternek megfelelő bitek értéke 1.

**Paraméterek**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **érték1** |Kötelező |NUM |Numerikus érték, amelynek AND'ed kell lennie a érték2|
| **érték2** |Kötelező |NUM |Numerikus érték, amelynek AND'ed kell lennie a érték1|

**Példa**<br>
BitAnd (& HF, & HF7)                                                                                
11110111 és 00000111 = 00000111 Szóval a BitAnd 7 értéket ad vissza, a 00000111 bináris értékét

---
### <a name="cbool"></a>CBool
**Függvény**<br> CBool (kifejezés)

**Leírás**<br> A CBool egy logikai értéket ad vissza a kiértékelt kifejezés alapján. Ha a kifejezés kiértékelése nem nulla értékű, akkor a CBool igaz értéket ad vissza, máskülönben hamis értéket ad vissza.

**Paraméterek**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **kifejezés** |Kötelező | expression | Bármely érvényes kifejezés |

**Példa**<br>
CBool ([attribute1] = [attribute2])                                                                    
Igaz értéket ad vissza, ha mindkét attribútum ugyanazzal az értékkel rendelkezik.

---
### <a name="coalesce"></a>összefonódik
**Függvény**<br> Egyesítés (source1, source2,..., defaultValue)

**Leírás**<br> Az első olyan értéket adja vissza, amely nem NULL. Ha az összes argumentum értéke NULL, és a defaultValue szerepel, a rendszer a defaultValue értéket adja vissza. Ha az összes argumentum NULL értékű, és a defaultValue értéke nem létezik, az egyesítés NULL értéket ad vissza.

**Paraméterek**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source1 ... sourceN** | Kötelező | Sztring |Kötelező, változó számú alkalommal. Általában az attribútum neve, az adatforrás-objektum. |
| **defaultValue** | Optional | Sztring | Az alapértelmezett érték, amelyet akkor kell használni, ha az összes forrás értéke NULL. Üres karakterlánc lehet ("").

---
### <a name="converttobase64"></a>ConvertToBase64
**Függvény**<br> ConvertToBase64 (forrás)

**Leírás**<br> A ConvertToBase64 függvény egy karakterláncot Unicode Base64 karakterlánccá alakít át.

**Paraméterek**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **forrás** |Kötelező |Sztring |Az alap 64-re konvertálandó karakterlánc|

**Példa**<br>
ConvertToBase64 ("Helló világ!")                                                                                                        
Returns "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Függvény**<br> ConvertToUTF8Hex (forrás)

**Leírás**<br> A ConvertToUTF8Hex függvény egy karakterláncot UTF8 hexadecimális kódolású értékre alakít át.

**Paraméterek**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **forrás** |Kötelező |Sztring |Az UTF8 hexadecimális értékre konvertálandó karakterlánc|

**Példa**<br>
ConvertToUTF8Hex ("Helló világ!")                                                                                                         
48656C6C6F20776F726C6421 visszaadása

---
### <a name="count"></a>Darabszám
**Függvény**<br> Darabszám (attribútum)

**Leírás**<br> A Count függvény egy többértékű attribútum elemeinek számát adja vissza.

**Paraméterek**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **attribútum** |Kötelező |attribútum |Többértékű attribútum, amely megszámolja az elemeket|

---
### <a name="cstr"></a>CStr
**Függvény**<br> CStr (érték)

**Leírás**<br> A CStr függvény egy értéket karakterlánc típusú adattípusra konvertál.

**Paraméterek**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **value** |Kötelező | numerikus, hivatkozás vagy logikai | Számérték, hivatkozási attribútum vagy logikai érték lehet. |

**Példa**<br>
CStr ([DN])                                                            
A "CN = Joe, DC = contoso, DC = com" értéket adja vissza.

---
### <a name="datefromnum"></a>DateFromNum
**Függvény**<br> DateFromNum (érték)

**Leírás**<br> A DateFromNum függvény egy értéket AD meg az AD dátumformátum dátum és idő típusára.

**Paraméterek**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **value** |Kötelező | Dátum | A DateTime típusúra konvertálandó AD-dátum |

**Példa**<br>
DateFromNum ([lastLogonTimestamp])                                                                                                   
DateFromNum(129699324000000000)                                                            
A 2012-01-01 23:00:00-et jelölő DateTime értéket ad vissza.

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
**Függvény**<br> GUID ()

**Leírás**<br> A függvény GUID-azonosítója új véletlenszerű GUID azonosítót hoz létre

---
### <a name="instr"></a>InStr
**Függvény**<br> Érték1 (érték2, Start, compareType)

**Leírás**<br> A beosztási függvény megkeresi egy karakterláncban szereplő alsztring első előfordulását.

**Paraméterek**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **érték1** |Kötelező |Sztring |Keresendő karakterlánc |
| **érték2** |Kötelező |Sztring |A megtalálni kívánt karakterlánc |
| **start** |Optional |Egész szám |A kiindulási pozíció a karakterlánc megkereséséhez|
| **compareType** |Optional |Felsorolás |VbTextCompare vagy vbBinaryCompare lehet |

**Példa**<br>
("A gyors barna Róka", "gyors")                                                                             
Evalues – 5

VbBinaryCompare ("ismétlődő", "e", 3,)                                                                                  
Értékelés 7-re

---
### <a name="isnull"></a>IsNull
**Függvény**<br> IsNull (kifejezés)

**Leírás**<br> Ha a kifejezés értéke null, akkor a IsNull függvény Igaz értéket ad vissza. Attribútum esetén a null értéket az attribútum hiánya fejezi ki.

**Paraméterek**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **kifejezés** |Kötelező |expression |Kiértékelni kívánt kifejezés |

**Példa**<br>
IsNull ([displayName])                                                                                                
Igaz értéket ad vissza, ha az attribútum nincs jelen.

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Függvény**<br> IsNullOrEmpty (kifejezés)

**Leírás**<br> Ha a kifejezés null értékű vagy üres karakterlánc, akkor a IsNullOrEmpty függvény Igaz értéket ad vissza. Egy attribútum esetében ez igaz értékre értékeli, ha az attribútum hiányzik vagy létezik, de egy üres karakterlánc.
A függvény inverzének neve IsPresent.

**Paraméterek**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **kifejezés** |Kötelező |expression |Kiértékelni kívánt kifejezés |

**Példa**<br>
IsNullOrEmpty ([displayName])                                               
Igaz értéket ad vissza, ha az attribútum nincs jelen, vagy üres karakterlánc

---
### <a name="ispresent"></a>IsPresent
**Függvény**<br> IsPresent (kifejezés)

**Leírás**<br> Ha a kifejezés olyan karakterláncot ad vissza, amely nem null értékű, és nem üres, akkor a IsPresent függvény Igaz értéket ad vissza. A függvény inverzének neve IsNullOrEmpty.

**Paraméterek**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **kifejezés** |Kötelező |expression |Kiértékelni kívánt kifejezés |

**Példa**<br>
Switch (IsPresent ([directManager]), [directManager], IsPresent ([skiplevelManager]), [skiplevelManager], IsPresent ([rendező]), [Director])

---
### <a name="isstring"></a>IsString
**Függvény**<br> IsString (kifejezés)

**Leírás**<br> Ha a kifejezés kiértékelhető karakterlánc típusúra, akkor a IsString függvény Igaz értéket ad vissza.

**Paraméterek**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **kifejezés** |Kötelező |expression |Kiértékelni kívánt kifejezés |

---
### <a name="item"></a>Elem
**Függvény**<br> Elem (attribútum, index)

**Leírás**<br> Az Item függvény egy elemet ad vissza egy többértékű karakterlánc/attribútumból.

**Paraméterek**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **attribútum** |Kötelező |Attribútum |Keresendő többértékű attribútum |
| **index** |Kötelező |Egész szám | Index a Többértékű karakterlánc egyik elemébe|

**Példa**<br>
Elem ([proxyAddresses], 1)

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
**Függvény**<br> Left (karakterlánc, NumChars)

**Leírás**<br> A Left függvény egy karakterlánctól balra megadott számú karaktert ad vissza. Ha a numChars = 0, üres karakterláncot ad vissza.
Ha a numChars < 0 értéket, adja vissza a bemeneti karakterláncot.
Ha a karakterlánc null értékű, üres karakterláncot ad vissza.
Ha a sztring kevesebb karaktert tartalmaz a numChars megadott számnál, akkor a rendszer a karakterlánctal megegyező karakterláncot (azaz az 1. paraméterben szereplő összes karaktert tartalmazza) adja vissza.

**Paraméterek**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Karakterlánc** |Kötelező |Attribútum | A karakterlánc, amelyből karaktereket kell visszaadni |
| **NumChars** |Kötelező |Egész szám | Egy szám, amely a sztring elejétől (balra) való visszatéréshez szükséges karakterek számát azonosítja.|

**Példa**<br>
Left ("John Doe", 3)                                                            
A "Joh" értéket adja vissza

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
### <a name="numfromdate"></a>NumFromDate
**Függvény**<br> NumFromDate (érték)

**Leírás**<br> A NumFromDate függvény egy DateTime értéket konvertál Active Directory formátumra, amely az attribútumok (például a [accountExpires](https://docs.microsoft.com/windows/win32/adschema/a-accountexpires)) beállításához szükséges. Ezzel a függvénnyel a Felhőbeli HR-alkalmazásokból (például a munkanapokból és a SuccessFactors) kapott dátum-és időértékek konvertálhatók 

**Paraméterek**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **value** |Kötelező | Sztring | A dátum és idő karakterlánca támogatott formátumban. Támogatott formátumok: https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx. |

**Példa**<br>
* Példa munkanapokra <br>
  Feltételezve, hogy a *ContractEndDate* attribútumot szeretné leképezni a munkaterületről, amely a *2020-12-31-08:00* érték *ACCOUNTEXPIRES* mezőjében szerepel az ad-ben, itt látható, hogyan használhatja ezt a függvényt, és módosíthatja az időzóna-eltolást a területi beállításnak megfelelően. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* SuccessFactors példa <br>
  Feltételezve, hogy le szeretné képezni a *endDate* attribútumot a SuccessFactors-ből, amely a következő formátumban van *: M/d/éééé óó: PP: SS tt* to *accountExpires* Field in ad, itt látható, hogyan használhatja ezt a függvényt, és módosíthatja az időzóna eltolását a területi beállításnak megfelelően.
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>RemoveDuplicates
**Függvény**<br> RemoveDuplicates (attribútum)

**Leírás**<br> A RemoveDuplicates függvény többértékű karakterláncot használ, és minden érték egyedi.

**Paraméterek**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **attribútum** |Kötelező |Többértékű attribútum |Több értékkel rendelkező attribútum, amelyből duplikált elemek lesznek eltávolítva|

**Példa**<br>
RemoveDuplicates ([proxyAddresses])                                                                                                       
Egy megtisztított proxyAddress attribútumot ad vissza, amelyben az összes duplikált érték el lett távolítva

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
| --- | --- | --- | --- |
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

**Leírás**<br> Ha a **forrás** értéke megegyezik egy **kulccsal**, az adott **kulcs** **értékét** adja vissza. Ha a **forrás** értéke nem felel meg a kulcsoknak, a a **defaultValue**értéket adja vissza.  A **kulcsok** és **értékek** paramétereit mindig párokban kell megadni. A függvény mindig paraméterek páros számú vár. A függvény nem használható hivatkozási attribútumokhoz, például a kezelőhöz. 

**Paraméterek**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **forrás** |Kötelező |Sztring |A frissítendő **forrás** értéke. |
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
### <a name="word"></a>Word
**Függvény**<br> Szó (karakterlánc, WordNumber, határolójelek)

**Leírás**<br> A Word függvény egy karakterláncon belül található szót ad vissza, amely a használni kívánt határolójeleket és a visszaadni kívánt szó paramétereit írja le. A rendszer az elválasztó karakterek egyikével elválasztott karakterláncban szereplő összes karakterláncot szavakként azonosítja:

Ha a szám < 1, üres karakterláncot ad vissza.
Ha a sztring null, üres karakterláncot ad vissza.
Ha a karakterlánc kevesebb, mint szám szót tartalmaz, vagy a sztring nem tartalmaz határolójelekkel azonosított szavakat, akkor a rendszer üres karakterláncot ad vissza.

**Paraméterek**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Karakterlánc** |Kötelező |Többértékű attribútum |Karakterlánc egy szó a következőből való visszaküldéséhez:.|
| **WordNumber** |Kötelező | Egész szám | Az a szám, amelyből vissza kell adni a Word-számot|
| **határolójelek** |Kötelező |Sztring| A szavak azonosításához használandó elválasztó karakter (eke) t jelölő sztring|

**Példa**<br>
Word ("a gyors barna Róka", 3, "")                                                                                       
A "barna" értéket adja vissza.

Word ("this, string! & sok elválasztó", 3, ",! & #")                                                                       
A "has" értéket adja vissza.

---

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

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>A flow e-mail-értéke, ha nem NULL értékű, máskülönben flow userPrincipalName
Ha megtalálható a mail attribútuma. Ha nem, akkor inkább a userPrincipalName értékét kell átvennie.

**Kifejezés** <br>
`Coalesce([mail],[userPrincipalName])`

**Minta bemenet/kimenet:** <br>

* **Bemenet** (e-mail): NULL
* **Bemenet** (userPrincipalName): "John.Doe@contoso.com"
* **Kimenet**: "John.Doe@contoso.com"

## <a name="related-articles"></a>Kapcsolódó cikkek
* [A felhasználók üzembe helyezésének és megszüntetésének automatizálása az SaaS-alkalmazásokban](../app-provisioning/user-provisioning.md)
* [Attribútum-hozzárendelések testreszabása a felhasználók üzembe helyezéséhez](../app-provisioning/customize-application-attributes.md)
* [Felhasználói kiépítési szűrők hatóköre](define-conditional-rules-for-provisioning-user-accounts.md)
* [SCIM használata a felhasználók és csoportok automatikus üzembe helyezésének engedélyezéséhez az Azure Active Directoryból az alkalmazásokba](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Fiók kiépítési értesítései](../app-provisioning/user-provisioning.md)
* [Az SaaS-alkalmazások integrálásával kapcsolatos oktatóanyagok listája](../saas-apps/tutorial-list.md)
