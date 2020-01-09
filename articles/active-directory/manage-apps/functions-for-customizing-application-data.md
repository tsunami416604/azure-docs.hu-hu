---
title: Kifejezések írása az attribútum-hozzárendelésekhez az Azure AD-ben
description: Megtudhatja, hogyan alakíthatja át a kifejezés-hozzárendeléseket az attribútumok értékének elfogadható formátumba való átalakításához az SaaS-alkalmazások objektumainak automatikus kiépítés során Azure Active Directoryban.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93b8387d4453a3d83bcce55c739548d914545f2f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430063"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Kifejezések írása attribútumleképezéshez az Azure Active Directoryban
Ha egy SaaS-alkalmazásra konfigurálja az üzembe helyezést, a megadható attribútumok egyike egy kifejezés-hozzárendelés. Ilyen esetben olyan parancsfájl-szerű kifejezést kell írnia, amely lehetővé teszi, hogy a felhasználói adatait olyan formátumokba alakítsa át, amelyek az SaaS-alkalmazás számára elfogadhatóak.

## <a name="syntax-overview"></a>Szintaxis áttekintése
Az attribútum-hozzárendelések kifejezések szintaxisa Visual Basic for Applications (VBA) függvények emlékeztetője.

* A teljes kifejezést a functions kifejezésben kell definiálni, amely egy, a zárójelben szereplő argumentumokkal kiegészített nevet tartalmaz: <br>
  *Függvénynév (`<<argument 1>>`,`<<argument N>>`)*
* A függvények egymásba ágyazhatók. Példa: <br> *FunctionOne (FunctionTwo (`<<argument1>>`))*
* A függvények három különböző típusú argumentumot adhat át:
  
  1. Attribútumok, amelyeket szögletes zárójelbe kell foglalni. Például: [attributeName]
  2. Karakterlánc-konstansok, amelyek idézőjelek közé kell, hogy legyenek. Például: "Egyesült Államok"
  3. Egyéb függvények. Például: FunctionOne (`<<argument1>>`, FunctionTwo (`<<argument2>>`))
* Karakterlánc-konstansok esetén, ha a karakterláncban a fordított perjel (\) vagy az idézőjel (") értékre van szüksége, akkor azt a fordított perjel (\) szimbólummal kell megadnia. Például: "cég neve: \\" contoso\\""

## <a name="list-of-functions"></a>Függvények listája
[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [BitAnd](#bitand) &nbsp;&nbsp;&nbsp;&nbsp; [CBool](#cbool) &nbsp;&nbsp;&nbsp;&nbsp; [Coalesce](#coalesce) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToBase64](#converttobase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [Count](#count) &nbsp;&nbsp;&nbsp;&nbsp; [CStr](#cstr) &nbsp;&nbsp;&nbsp;&nbsp; [DateFromNum](#datefromnum) &nbsp;[FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Guid](#guid) &nbsp;&nbsp;&nbsp;&nbsp; [InStr](#instr) &nbsp;&nbsp;&nbsp;&nbsp; [IsNull](#isnull) &nbsp;&nbsp;&nbsp;&nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsPresent](#ispresent) &nbsp;&nbsp;&nbsp;&nbsp; [IsString](#isstring) &nbsp;&nbsp;&nbsp;&nbsp; [Item](#item) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Left](#left) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [RemoveDuplicates](#removeduplicates) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)&nbsp;&nbsp;&nbsp;&nbsp; [Word](#word)

---
### <a name="append"></a>Hozzáfűzés
**Függvény**<br> Hozzáfűzés (forrás, utótag)

**Leírás:**<br> A forrás sztring értéket veszi fel, és hozzáfűzi az utótagot a végéhez.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **forrás** |Szükséges |Sztring |Az attribútum neve általában a forrásoldali objektumban. |
| **utótag** |Szükséges |Sztring |A forrás érték végéhez hozzáfűzni kívánt karakterlánc. |

---
### <a name="bitand"></a>BitAnd
**Függvény**<br> BitAnd (érték1, érték2)

**Leírás:**<br> Ez a függvény mindkét paramétert a bináris ábrázolásra konvertálja, és egy kis mennyiséget állít be a következőre:

0 – Ha a érték1-ben és a érték2-ben található megfelelő bitek közül egyet vagy mindkettőt 0                                                  
1 – Ha a megfelelő bitek mindegyike 1.                                    

Más szóval a 0 értéket adja vissza minden esetben, kivéve, ha mindkét paraméternek megfelelő bitek értéke 1.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **érték1** |Szükséges |num |Numerikus érték, amelynek AND'ed kell lennie a érték2|
| **érték2** |Szükséges |num |Numerikus érték, amelynek AND'ed kell lennie a érték1|

**Példa**<br>
BitAnd (& HF, & HF7)                                                                                
11110111 és 00000111 = 00000111 Szóval a BitAnd 7 értéket ad vissza, a 00000111 bináris értékét

---
### <a name="cbool"></a>CBool
**Függvény**<br> CBool (kifejezés)

**Leírás:**<br> A CBool egy logikai értéket ad vissza a kiértékelt kifejezés alapján. Ha a kifejezés kiértékelése nem nulla értékű, akkor a CBool igaz értéket ad vissza, máskülönben hamis értéket ad vissza.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **kifejezés** |Szükséges | kifejezés | Bármely érvényes kifejezés |

**Példa**<br>
CBool ([attribute1] = [attribute2])                                                                    
Igaz értéket ad vissza, ha mindkét attribútum ugyanazzal az értékkel rendelkezik.

---
### <a name="coalesce"></a>Coalesce
**Függvény**<br> Egyesítés (source1, source2,..., defaultValue)

**Leírás:**<br> Az első olyan értéket adja vissza, amely nem NULL. Ha az összes argumentum értéke NULL, és a defaultValue szerepel, a rendszer a defaultValue értéket adja vissza. Ha az összes argumentum NULL értékű, és a defaultValue értéke nem létezik, az egyesítés NULL értéket ad vissza.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **source1 ... sourceN** | Szükséges | Sztring |Kötelező, változó számú alkalommal. Az attribútum neve általában a forrásoldali objektumban. |
| **defaultValue** | Választható | Sztring | Az alapértelmezett érték, amelyet akkor kell használni, ha az összes forrás értéke NULL. Üres karakterlánc ("") lehet.

---
### <a name="converttobase64"></a>ConvertToBase64
**Függvény**<br> ConvertToBase64 (forrás)

**Leírás:**<br> A ConvertToBase64 függvény egy karakterláncot Unicode Base64 karakterlánccá alakít át.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **forrás** |Szükséges |Sztring |Az alap 64-re konvertálandó karakterlánc|

**Példa**<br>
ConvertToBase64 ("Helló világ!")                                                                                                        
A "SABlAGwAbABvACAAdwBvAHIAbABkACEA" értéket adja vissza

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Függvény**<br> ConvertToUTF8Hex (forrás)

**Leírás:**<br> A ConvertToUTF8Hex függvény egy karakterláncot UTF8 hexadecimális kódolású értékre alakít át.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **forrás** |Szükséges |Sztring |Az UTF8 hexadecimális értékre konvertálandó karakterlánc|

**Példa**<br>
ConvertToUTF8Hex ("Helló világ!")                                                                                                         
48656C6C6F20776F726C6421 visszaadása

---
### <a name="count"></a>Mennyiség
**Függvény**<br> Darabszám (attribútum)

**Leírás:**<br> A Count függvény egy többértékű attribútum elemeinek számát adja vissza.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **attribútum** |Szükséges |attribútum |Többértékű attribútum, amely megszámolja az elemeket|

---
### <a name="cstr"></a>CStr
**Függvény**<br> CStr (érték)

**Leírás:**<br> A CStr függvény egy értéket karakterlánc típusú adattípusra konvertál.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **value** |Szükséges | numerikus, hivatkozás vagy logikai | Számérték, hivatkozási attribútum vagy logikai érték lehet. |

**Példa**<br>
CStr ([DN])                                                            
A "CN = Joe, DC = contoso, DC = com" értéket adja vissza.

---
### <a name="datefromnum"></a>DateFromNum
**Függvény**<br> DateFromNum (érték)

**Leírás:**<br> A DateFromNum függvény egy értéket AD meg az AD dátumformátum dátum és idő típusára.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **value** |Szükséges | Dátum | A DateTime típusúra konvertálandó AD-dátum |

**Példa**<br>
DateFromNum ([lastLogonTimestamp])                                                                                                   
DateFromNum(129699324000000000)                                                            
A 2012-01-01 23:00:00-et jelölő DateTime értéket ad vissza.

---
### <a name="formatdatetime"></a>FormatDateTime
**Függvény**<br> FormatDateTime (forrás, inputFormat, outputFormat)

**Leírás:**<br> Egy dátum sztringet vesz fel az egyik formátumból, és átalakítja azt más formátumra.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **forrás** |Szükséges |Sztring |Az attribútum neve általában a forrásoldali objektumban. |
| **inputFormat** |Szükséges |Sztring |A forrás értékének várt formátuma. Támogatott formátumok: [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Szükséges |Sztring |A kimeneti dátum formátuma. |

---
### <a name="guid"></a>GUID
**Függvény**<br> GUID ()

**Leírás:**<br> A függvény GUID-azonosítója új véletlenszerű GUID azonosítót hoz létre

---
### <a name="instr"></a>InStr
**Függvény**<br> Érték1 (érték2, Start, compareType)

**Leírás:**<br> A beosztási függvény megkeresi egy karakterláncban szereplő alsztring első előfordulását.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **érték1** |Szükséges |Sztring |Keresendő karakterlánc |
| **érték2** |Szükséges |Sztring |A megtalálni kívánt karakterlánc |
| **start** |Választható |Egész szám |A kiindulási pozíció a karakterlánc megkereséséhez|
| **compareType** |Választható |Felsorolás |VbTextCompare vagy vbBinaryCompare lehet |

**Példa**<br>
("A gyors barna Róka", "gyors")                                                                             
Evalues – 5

VbBinaryCompare ("ismétlődő", "e", 3,)                                                                                  
Értékelés 7-re

---
### <a name="isnull"></a>IsNull
**Függvény**<br> IsNull (kifejezés)

**Leírás:**<br> Ha a kifejezés értéke null, akkor a IsNull függvény Igaz értéket ad vissza. Attribútum esetén a null értéket az attribútum hiánya fejezi ki.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **kifejezés** |Szükséges |kifejezés |Kiértékelni kívánt kifejezés |

**Példa**<br>
IsNull ([displayName])                                                                                                
Igaz értéket ad vissza, ha az attribútum nincs jelen.

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Függvény**<br> IsNullOrEmpty (kifejezés)

**Leírás:**<br> Ha a kifejezés null értékű vagy üres karakterlánc, akkor a IsNullOrEmpty függvény Igaz értéket ad vissza. Egy attribútum esetében ez igaz értékre értékeli, ha az attribútum hiányzik vagy létezik, de egy üres karakterlánc.
A függvény inverzének neve IsPresent.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **kifejezés** |Szükséges |kifejezés |Kiértékelni kívánt kifejezés |

**Példa**<br>
IsNullOrEmpty ([displayName])                                               
Igaz értéket ad vissza, ha az attribútum nincs jelen, vagy üres karakterlánc

---
### <a name="ispresent"></a>IsPresent
**Függvény**<br> IsNullOrEmpty (kifejezés)

**Leírás:**<br> Ha a kifejezés olyan karakterláncot ad vissza, amely nem null értékű, és nem üres, akkor a IsPresent függvény Igaz értéket ad vissza. A függvény inverzének neve IsNullOrEmpty.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **kifejezés** |Szükséges |kifejezés |Kiértékelni kívánt kifejezés |

**Példa**<br>
Switch (IsPresent ([directManager]), [directManager], IsPresent ([skiplevelManager]), [skiplevelManager], IsPresent ([rendező]), [Director])

---
### <a name="isstring"></a>IsString
**Függvény**<br> IsString (kifejezés)

**Leírás:**<br> Ha a kifejezés kiértékelhető karakterlánc típusúra, akkor a IsString függvény Igaz értéket ad vissza.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **kifejezés** |Szükséges |kifejezés |Kiértékelni kívánt kifejezés |

---
### <a name="item"></a>Tétel
**Függvény**<br> Elem (attribútum, index)

**Leírás:**<br> Az Item függvény egy elemet ad vissza egy többértékű karakterlánc/attribútumból.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **attribútum** |Szükséges |Attribútum |Keresendő többértékű attribútum |
| **index** |Szükséges |Egész szám | Index a Többértékű karakterlánc egyik elemébe|

**Példa**<br>
Elem ([proxyAddresses], 1)

---
### <a name="join"></a>Csatlakozás
**Függvény**<br> Join (elválasztó, source1, source2,...)

**Leírás:**<br> A JOIN () hasonló a hozzáfűzéshez (), azzal a kivétellel, hogy több **forrás** sztringet is egyesít egyetlen karakterláncban, és az egyes értékeket **elválasztó sztring választja** el egymástól.

Ha a forrásadatok egyike egy többértékű attribútum, akkor az adott attribútum minden értéke együtt lesz egyesítve, az elválasztó értékkel elválasztva.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **elválasztó** |Szükséges |Sztring |A forrásadatok elválasztására szolgáló karakterlánc, amely egyetlen sztringbe van fűzve. Lehet "", ha nem kötelező elválasztó. |
| **source1 ... sourceN** |Kötelező, változó – ennyiszer |Sztring |A egyesíteni kívánt karakterlánc-értékek. |

---
### <a name="left"></a>Bal
**Függvény**<br> Left (karakterlánc, NumChars)

**Leírás:**<br> A Left függvény egy karakterlánctól balra megadott számú karaktert ad vissza. Ha a numChars = 0, üres karakterláncot ad vissza.
Ha a numChars < 0 értéket, adja vissza a bemeneti karakterláncot.
Ha a karakterlánc null értékű, üres karakterláncot ad vissza.
Ha a sztring kevesebb karaktert tartalmaz a numChars megadott számnál, akkor a rendszer a karakterlánctal megegyező karakterláncot (azaz az 1. paraméterben szereplő összes karaktert tartalmazza) adja vissza.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **Sztring** |Szükséges |Attribútum | A karakterlánc, amelyből karaktereket kell visszaadni |
| **NumChars** |Szükséges |Egész szám | Egy szám, amely a sztring elejétől (balra) való visszatéréshez szükséges karakterek számát azonosítja.|

**Példa**<br>
Left ("John Doe", 3)                                                            
A "Joh" értéket adja vissza

---
### <a name="mid"></a>Mid
**Függvény**<br> Mid (forrás, Kezdés, hossz)

**Leírás:**<br> A forrás értékének egy alsztringjét adja vissza. Az alsztringek olyan karakterláncok, amelyek csak néhány karaktert tartalmaznak a forrás sztringből.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **forrás** |Szükséges |Sztring |Az attribútum neve általában. |
| **start** |Szükséges |egész szám |Az index a **forrás** sztringben, ahol az alsztringnek el kell indulnia. A karakterlánc első karakterének indexe 1, a második karakter pedig a 2. indexet fogja tartalmazni. |
| **hossza** |Szükséges |egész szám |Az alsztring hossza. Ha a hossz a **forrás** sztringen kívülre végződik, a függvény a **kezdő** indexből származó alsztringet ad vissza a **forrás** sztring végéig. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Függvény**<br> NormalizeDiacritics (forrás)

**Leírás:**<br> Egy karakterlánc-argumentumot igényel. A karakterláncot adja vissza, de bármilyen diakritikus karakterrel egyenértékű, nem diakritikus karakterrel helyettesíthető. Jellemzően a különböző felhasználói azonosítókban, például egyszerű felhasználónevek, SAM-fiókok és e-mail-címekben használható, mellékjeleket és vezetékneveket tartalmazó nevek és vezetéknévek átalakítására szolgálnak.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **forrás** |Szükséges |Sztring | Általában utónév vagy vezetéknév attribútum. |

---
### <a name="not"></a>Not
**Függvény**<br> Nem (forrás)

**Leírás:**<br> Megfordítja a **forrás**logikai értékét. Ha a **forrás** értéke "*true*" (igaz), a "*false*" értéket adja vissza. Ellenkező esetben az "*igaz*" értéket adja vissza.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **forrás** |Szükséges |Logikai karakterlánc |A várt **források** értéke "true" vagy "false". |

---
### <a name="removeduplicates"></a>RemoveDuplicates
**Függvény**<br> RemoveDuplicates (attribútum)

**Leírás:**<br> A RemoveDuplicates függvény többértékű karakterláncot használ, és minden érték egyedi.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **attribútum** |Szükséges |Többértékű attribútum |Több értékkel rendelkező attribútum, amelyből duplikált elemek lesznek eltávolítva|

**Példa**<br>
RemoveDuplicates ([proxyAddresses])                                                                                                       
Egy megtisztított proxyAddress attribútumot ad vissza, amelyben az összes duplikált érték el lett távolítva

---
### <a name="replace"></a>Csere
**Függvény**<br> Replace (forrás, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, sablon)

**Leírás:**<br>
Egy karakterláncon belüli értékeket cserél le. A megadott paraméterektől függően másképp működik:

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

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **forrás** |Szükséges |Sztring |Az attribútum neve általában a **forrásoldali** objektumban. |
| **oldValue** |Választható |Sztring |A **forrásban** vagy **sablonban**cserélni kívánt érték. |
| **regexPattern** |Választható |Sztring |A **forrásban**lecserélni kívánt érték regex-mintája. Vagy ha **replacementPropertyName** használ, a **replacementPropertyName**származó érték kinyerésére szolgáló minta. |
| **regexGroupName** |Választható |Sztring |A csoport neve a **regexPattern**belül. Csak **replacementPropertyName** használata esetén a csoport értékének kinyerése a **replacementPropertyName** **replacementValue** történik. |
| **replacementValue** |Választható |Sztring |Új érték a régi helyett. |
| **replacementAttributeName** |Választható |Sztring |A helyettesítő értékhez használandó attribútum neve |
| **sablon** |Választható |Sztring |Ha meg van adni a **sablon** értéke, megkeresjük a **OldValue** a sablonon belül, és lecseréljük a **forrás** értékre. |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Függvény**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, ...)

**Leírás:**<br> Legalább két argumentumot igényel, amelyek a kifejezések használatával definiált egyedi érték-létrehozási szabályok. A függvény kiértékeli az egyes szabályokat, majd ellenőrzi az egyediséghez generált értéket a cél alkalmazásban vagy könyvtárban. A rendszer az első egyedi értéket találta vissza. Ha az összes érték már létezik a célhelyen, a bejegyzést letétbe helyezi a rendszer, és az OK bekerül a naplóba. Nincs felső korlát a megadható argumentumok számához.

> [!NOTE]
> - Ez egy legfelső szintű függvény, nem ágyazható be.
> - Ez a függvény nem alkalmazható olyan attribútumokra, amelyek egyező elsőbbséggel rendelkeznek.  
> - Ez a függvény csak a bejegyzések létrehozásához használható. Ha attribútummal használja, állítsa a **leképezés alkalmazása** tulajdonságot csak az **objektum létrehozása során**.
> - Ez a függvény jelenleg csak a "munkanap Active Directory a felhasználók kiosztásához" támogatott. Más kiépítési alkalmazásokkal nem használható. 


**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **uniqueValueRule1 ... uniqueValueRuleN** |Legalább 2 szükséges, nincs felső korlát |Sztring | A kiértékelni kívánt egyedi érték-létrehozási szabályok listája. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Függvény**<br> SingleAppRoleAssignment ([appRoleAssignments])

**Leírás:**<br> Egyetlen appRoleAssignment ad vissza egy adott alkalmazás felhasználóhoz rendelt összes appRoleAssignments listájáról. Ez a függvény szükséges ahhoz, hogy a appRoleAssignments objektumot egyetlen szerepkör-nevet megadó karakterláncba alakítsa át. Vegye figyelembe, hogy az ajánlott eljárás annak biztosítása, hogy egyszerre csak egy appRoleAssignment legyen hozzárendelve egy felhasználóhoz, és ha több szerepkör van hozzárendelve, a visszaadott szerepkör-karakterlánc nem lehet előre jelezhető. 

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **AppRoleAssignments** |Szükséges |Sztring |**[appRoleAssignments]** objektum. |

---
### <a name="split"></a>Felosztás
**Függvény**<br> Felosztás (forrás, elválasztó karakter)

**Leírás:**<br> A karakterláncot egy többértékű tömbre osztja fel a megadott elválasztó karakter használatával.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **forrás** |Szükséges |Sztring |a frissítendő **forrás** értéke. |
| **elválasztó** |Szükséges |Sztring |Meghatározza a karakterlánc felosztására szolgáló karaktert (példa: ",") |

---
### <a name="stripspaces"></a>StripSpaces
**Függvény**<br> StripSpaces (forrás)

**Leírás:**<br> Eltávolítja az összes szóköz ("") karaktert a forrás sztringből.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **forrás** |Szükséges |Sztring |a frissítendő **forrás** értéke. |

---
### <a name="switch"></a>Kapcsoló
**Függvény**<br> Kapcsoló (forrás, defaultValue, key1, érték1, key2, érték2,...)

**Leírás:**<br> Ha a **forrás** értéke megegyezik egy **kulccsal**, az adott **kulcs** **értékét** adja vissza. Ha a **forrás** értéke nem felel meg a kulcsoknak, a a **defaultValue**értéket adja vissza.  A **kulcsok** és **értékek** paramétereit mindig párokban kell megadni. A függvény mindig páros számú paramétert vár. A függvény nem használható hivatkozási attribútumokhoz, például a kezelőhöz. 

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **forrás** |Szükséges |Sztring |A frissítendő **forrás** értéke. |
| **defaultValue** |Választható |Sztring |Az alapértelmezett érték, amelyet akkor kell használni, ha a forrás nem felel meg a kulcsoknak. Üres karakterlánc ("") lehet. |
| **key** |Szükséges |Sztring |A **kulcs** a **forrás** értékének összehasonlításához a következővel:. |
| **value** |Szükséges |Sztring |A kulcsnak megfelelő **forrás** helyettesítő értéke. |

---
### <a name="tolower"></a>ToLower
**Függvény**<br> ToLower (forrás, kulturális környezet)

**Leírás:**<br> Egy *forrás* sztring értéket vesz igénybe, és a megadott kulturális szabályok alapján átalakítja a kisbetűsre. Ha nincs megadva *kulturális* információ, akkor a rendszer a semleges kultúrát fogja használni.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **forrás** |Szükséges |Sztring |Az attribútum neve általában a forrásoldali objektumból |
| **kulturális környezet** |Választható |Sztring |Az RFC 4646 alapján a kulturális név formátuma *languagecode2-ország/regioncode2*, ahol a *languagecode2* a kétbetűs nyelvi kód, az *ország/regioncode2* pedig a kétbetűs alkulturális kód. Ilyenek például a japán (Japán) és az en-US angol (Egyesült Államok). Azokban az esetekben, amikor a kétbetűs nyelvi kód nem érhető el, az ISO 639-2-ből származtatott hárombetűs kód van használatban.|

---
### <a name="toupper"></a>ToUpper
**Függvény**<br> ToUpper (forrás, kulturális környezet)

**Leírás:**<br> Egy *forrás* sztring értékét veszi át, és a megadott kulturális szabályok alapján átalakítja a nagybetűre. Ha nincs megadva *kulturális* információ, akkor a rendszer a semleges kultúrát fogja használni.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **forrás** |Szükséges |Sztring |Az attribútum neve általában a forrásoldali objektumban. |
| **kulturális környezet** |Választható |Sztring |Az RFC 4646 alapján a kulturális név formátuma *languagecode2-ország/regioncode2*, ahol a *languagecode2* a kétbetűs nyelvi kód, az *ország/regioncode2* pedig a kétbetűs alkulturális kód. Ilyenek például a japán (Japán) és az en-US angol (Egyesült Államok). Azokban az esetekben, amikor a kétbetűs nyelvi kód nem érhető el, az ISO 639-2-ből származtatott hárombetűs kód van használatban.|

---
### <a name="word"></a>Word
**Függvény**<br> Szó (karakterlánc, WordNumber, határolójelek)

**Leírás:**<br> A Word függvény egy karakterláncon belül található szót ad vissza, amely a használni kívánt határolójeleket és a visszaadni kívánt szó paramétereit írja le. A rendszer az elválasztó karakterek egyikével elválasztott karakterláncban szereplő összes karakterláncot szavakként azonosítja:

Ha a szám < 1, üres karakterláncot ad vissza.
Ha a sztring null, üres karakterláncot ad vissza.
Ha a karakterlánc kevesebb, mint szám szót tartalmaz, vagy a sztring nem tartalmaz határolójelekkel azonosított szavakat, akkor a rendszer üres karakterláncot ad vissza.

**Paraméterek:**<br> 

| Név | Szükséges/ismétlődő | Type (Típus) | Megjegyzések |
| --- | --- | --- | --- |
| **Sztring** |Szükséges |Többértékű attribútum |Karakterlánc egy szó a következőből való visszaküldéséhez:.|
| **WordNumber** |Szükséges | Egész szám | Az a szám, amelyből vissza kell adni a Word-számot|
| **határolójelek** |Szükséges |Sztring| A szavak azonosításához használandó elválasztó karakter (eke) t jelölő sztring|

**Példa**<br>
Word ("a gyors barna Róka", 3, "")                                                                                       
A "barna" értéket adja vissza.

Word ("this, string! & sok elválasztó", 3, ",! & #")                                                                       
A "has" értéket adja vissza.

---

## <a name="examples"></a>Példák
### <a name="strip-known-domain-name"></a>Szalag ismert tartományneve
A Felhasználónév beszerzéséhez a felhasználó e-mail-címéből egy ismert tartománynevet kell megadnia. <br>
Ha például a tartomány "contoso.com", akkor a következő kifejezést használhatja:

**Kifejezés** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Minta bemenet/kimenet:** <br>

* **Bemenet** (e-mail): "john.doe@contoso.com"
* **Kimenet**: "John. DOE"

### <a name="append-constant-suffix-to-user-name"></a>Állandó utótag hozzáfűzése a felhasználónévhez
Ha Salesforce-munkaterületet használ, előfordulhat, hogy a szinkronizálás előtt hozzá kell fűzni egy további utótagot az összes felhasználónevehöz.

**Kifejezés** <br>
`Append([userPrincipalName], ".test")`

**Minta bemenet/kimenet:** <br>

* **Bemenet**: (userPrincipalName): "John.Doe@contoso.com"
* **Kimenet**: "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Felhasználói alias előállítása az utónév és a vezetéknév összefűzésével
Felhasználói aliast kell létrehoznia úgy, hogy az első 3 betűt a felhasználó utónevét és az első 5 betűt adja meg.

**Kifejezés** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Minta bemenet/kimenet:** <br>

* **Bemenet** (givenName): "John"
* **Bemenet** (vezetéknév): "DOE"
* **Kimenet**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Mellékjelek eltávolítása egy sztringből
Az ékezetes jeleket tartalmazó karaktereket olyan karakterekkel kell helyettesíteni, amelyek nem tartalmaznak ékezetes jeleket.

**Kifejezés** <br>
NormalizeDiacritics ([givenName])

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

### <a name="output-date-as-a-string-in-a-certain-format"></a>Kimeneti dátum karakterláncként egy adott formátumban
Bizonyos formátumban szeretné elküldeni a dátumokat egy SaaS-alkalmazásnak. <br>
Például a ServiceNow dátumát szeretné formázni.

**Kifejezés** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Minta bemenet/kimenet:**

* **Bemenet** (extensionAttribute1): "20150123105347.1 z"
* **Kimenet**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Érték cseréje előre megadott beállítások alapján

Meg kell határoznia a felhasználó időzónáját az Azure AD-ben tárolt állapot kódja alapján. <br>
Ha az állapotkód nem egyezik az előre definiált beállításokkal, használja az "Australia/Sydney" alapértelmezett értékét.

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

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Egyedi érték előállítása a userPrincipalName (UPN) attribútumhoz
A felhasználó utóneve, középső neve és vezetékneve alapján értéket kell létrehoznia az UPN-attribútumhoz, és meg kell adnia annak egyediségét a cél AD-címtárban, mielőtt az értéket az UPN-attribútumhoz rendeli.

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
* [A felhasználók üzembe helyezésének és megszüntetésének automatizálása az SaaS-alkalmazásokban](user-provisioning.md)
* [Attribútum-hozzárendelések testreszabása a felhasználók üzembe helyezéséhez](customize-application-attributes.md)
* [Felhasználói kiépítési szűrők hatóköre](define-conditional-rules-for-provisioning-user-accounts.md)
* [SCIM használata a felhasználók és csoportok automatikus üzembe helyezésének engedélyezéséhez az Azure Active Directoryból az alkalmazásokba](use-scim-to-provision-users-and-groups.md)
* [Fiók kiépítési értesítései](user-provisioning.md)
* [Az SaaS-alkalmazások integrálásával kapcsolatos oktatóanyagok listája](../saas-apps/tutorial-list.md)
