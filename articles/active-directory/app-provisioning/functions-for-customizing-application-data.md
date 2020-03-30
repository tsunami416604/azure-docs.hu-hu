---
title: Kifejezések írása attribútumleképezéshez az Azure AD-ben
description: Ismerje meg, hogyan használhatja a kifejezésleképezések az attribútumértékek elfogadható formátumgá alakításához az Azure Active Directory SaaS-alkalmazásobjektumainak automatikus kiépítése során.
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
ms.openlocfilehash: cc41a18063202bfefb9ddf7238de17fc691984af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612137"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Kifejezések írása attribútumleképezéshez az Azure Active Directoryban
SaaS-alkalmazásba való kiépítés konfigurálásakor az attribútumleképezések egyik megadott típusa egy kifejezésleképezés. Ezekhez meg kell írnia egy parancsfájl-szerű kifejezést, amely lehetővé teszi a felhasználók adatainak a SaaS-alkalmazás számára elfogadhatóbb formátumgá alakítását.

## <a name="syntax-overview"></a>Szintaxis – áttekintés
Az attribútumleképezések kifejezéseinek szintaxisa a Visual Basic for Applications (VBA) függvényekre emlékeztet.

* A teljes kifejezést függvényekben kell definiálni, amelyek egy névből állnak, amelyet zárójelben lévő argumentumok követnek: <br>
  *Függvénynév(`<<argument 1>>``<<argument N>>`, )*
* A függvényeket egymásba ágyazhatják. Példa: <br> *FunctionOne(FunctionTwo(`<<argument1>>`))*
* A függvények három különböző típusú argumentumot adhat át:
  
  1. Jellemzők, amelyeket szögletes zárójelek közé kell tenni. Például: [attribútumnév]
  2. Karakterlánc-állandók, amelyeket idézőjelek közé kell tenni. Például: "Egyesült Államok"
  3. Egyéb funkciók. Például: FunctionOne(`<<argument1>>`,`<<argument2>>`FunctionTwo( ))
* Karakterlánc-állandók esetén, ha a karakterláncban fordított perjelre ( \ ) vagy idézőjelre ( ) van szüksége, akkor azt a fordított perjel ( \ ) szimbólummal kell megkerülni. Például: "Vállalat \\neve: "Contoso\\""

## <a name="list-of-functions"></a>Függvénylista
[Bit hozzáfűzése](#append) &nbsp; &nbsp; &nbsp; &nbsp; [BitAnd](#bitand) &nbsp; &nbsp; &nbsp; És &nbsp; [CBool](#cbool) &nbsp; &nbsp; &nbsp; &nbsp; [Count](#count) &nbsp; &nbsp; [Coalesce](#coalesce) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [CStr](#cstr) [ConvertToBase64](#converttobase64) &nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp; Coalesce &nbsp; &nbsp; ConvertToBase64 ConvertToUTF8Hex Count CStr &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [Guid](#guid) &nbsp; [IIF](#iif) &nbsp; [InStr](#instr) &nbsp; [DateFromNum](#datefromnum) &nbsp; &nbsp; &nbsp; &nbsp; [FormatDateTime](#formatdatetime) &nbsp; &nbsp; [IsNull](#isnull) [IsNullOrEmpty](#isnullorempty) DateFromNum &nbsp; FormatDateTime &nbsp; Guid &nbsp; &nbsp; IIF &nbsp; &nbsp; InStr IsNull &nbsp; IsNullVagyEmpty &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [IsPresent](#ispresent) &nbsp; &nbsp; &nbsp; [Mid](#mid) [Item](#item) &nbsp; [IsString](#isstring) &nbsp; [Not](#not) [Left](#left) [Join](#join) [NormalizeDiacritics](#normalizediacritics) IsString &nbsp; &nbsp; elem &nbsp; &nbsp; illesztése &nbsp; bal &nbsp; középső &nbsp; normalizálásaMellékjelek nem &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [Split](#split) [Replace](#replace) &nbsp; &nbsp; [RemoveDuplicates](#removeduplicates) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [SelectUniqueValue](#selectuniquevalue) &nbsp; [SingleAppRoleAssignment](#singleapproleassignment) &nbsp; Duplikált elemek&nbsp; cseréje SelectUniqueValue&nbsp; &nbsp; SingleAppRoleAssignment&nbsp; split&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [ StripSpaces](#stripspaces) &nbsp; &nbsp; &nbsp; &nbsp; [Switch](#switch) &nbsp; [ToLower](#tolower) [Word](#word) kapcsolóalsó&nbsp; &nbsp; [ToUpper](#toupper) toupper&nbsp; szó&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;

---
### <a name="append"></a>Hozzáfűzés
**Funkció:**<br> Hozzáfűzés(forrás, utótag)

**Leírás:**<br> Vesz egy forráskarakterlánc értékét, és hozzáfűzi az utótagot a végén.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Forrás** |Kötelező |Sztring |A forrásobjektum attribútumának általában neve. |
| **Utótag** |Kötelező |Sztring |A forrásérték végéhez hozzáfűző karakterlánc. |

---
### <a name="bitand"></a>Bitand (bitand)
**Funkció:**<br> BitAnd(érték1; érték2)

**Leírás:**<br> Ez a függvény mindkét paramétert bináris reprezentációvá alakítja, és egy kicsit a következőkre állítja be:

0 - ha az egyik vagy mindkét megfelelő bit érték1 és érték2 0                                                  
1 - ha mindkét megfelelő bit 1.                                    

Más szóval minden esetben 0 értéket ad vissza, kivéve, ha mindkét paraméter megfelelő bitje 1.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **érték1** |Kötelező |num |Az AND'ed értékkel rendelkező numerikus érték2|
| **érték2** |Kötelező |num |Az AND'ed értékkel rendelkező numerikus érték1|

**Példa:**<br>
BitAnd(&HF, &HF7)                                                                                
11110111 ÉS 00000111 = 00000111 so BitAnd 7 értéket ad vissza, a bináris értéke 00000111

---
### <a name="cbool"></a>Kbool
**Funkció:**<br> CBool(Kifejezés)

**Leírás:**<br> A CBool egy logikai értéket ad vissza a kiértékelt kifejezés alapján. Ha a kifejezés nem nulla értéket ad eredményül, akkor a CBool függvény igaz értéket ad vissza, máskülönben hamis értéket ad vissza.If the expression evaluates to a non-zero value, then CBool returns True, else it returns False..

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Kifejezés** |Kötelező | kifejezés | Bármely érvényes kifejezés |

**Példa:**<br>
CBool([attribútum1] = [attribútum2])                                                                    
Igaz értéket ad vissza, ha mindkét attribútum értéke megegyezik.

---
### <a name="coalesce"></a>Coalesce
**Funkció:**<br> Összeolvadás(forrás1; forrás2, ..., defaultValue)

**Leírás:**<br> Az első nem NULL forrásértéket adja eredményül. Ha az összes argumentum NULL, és a defaultValue értéke jelen van, a defaultValue függvény t ad vissza. Ha az összes argumentum NULL, és a defaultValue értéke nincs jelen, az Egyesítés null értéket ad vissza.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **forrás1 ... forrásN** | Kötelező | Sztring |Kötelező, változó számú alkalommal. A forrásobjektum attribútumának általában neve. |
| **defaultValue** | Optional | Sztring | Alapértelmezett érték, amelyet akkor használ, ha az összes forrásérték NULL. Lehet üres karakterlánc ("").

---
### <a name="converttobase64"></a>KonvertálásBase64
**Funkció:**<br> ConvertToBase64(forrás)

**Leírás:**<br> A ConvertToBase64 függvény egy karakterláncot Unicode base64 karakterláncgá alakít át.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Forrás** |Kötelező |Sztring |A 64-es alapra konvertálandó karakterlánc|

**Példa:**<br>
ConvertToBase64("Hello világ!")                                                                                                        
Eredmény: "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Funkció:**<br> ConvertToUTF8Hex(forrás)

**Leírás:**<br> A ConvertToUTF8Hex függvény egy karakterláncot UTF8 Hex kódolású értékké alakít át.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Forrás** |Kötelező |Sztring |UTF8 hexúrrá konvertálandó karakterlánc|

**Példa:**<br>
ConvertToUTF8Hex("Hello világ!")                                                                                                         
Eredménye 48656C6C6F20776F726C6421

---
### <a name="count"></a>Darabszám
**Funkció:**<br> Count(attribútum)

**Leírás:**<br> A Count függvény egy többértékű attribútum elemeinek számát adja eredményül.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Attribútum** |Kötelező |attribute |Többértékű attribútum, amelynek elemei meg vannak számlálva|

---
### <a name="cstr"></a>CStr
**Funkció:**<br> CStr(érték)

**Leírás:**<br> A CStr függvény egy értéket karakterlánc-adattípussá alakít át.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **value** |Kötelező | numerikus, referencia vagy logikai | Lehet numerikus érték, hivatkozási attribútum vagy logikai érték. |

**Példa:**<br>
CStr([dn])                                                            
Eredmény: "cn=Joe,dc=contoso,dc=com"

---
### <a name="datefromnum"></a>DateFromNum (Dátumfromnum)
**Funkció:**<br> DateFromNum(érték)

**Leírás:**<br> A DateFromNum függvény az AD dátumformátumában lévő értéket DateTime típussá alakítja.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **value** |Kötelező | Dátum | AD dátum datetime típusra konvertálandó dátum |

**Példa:**<br>
DateFromNum([lastLogonTimestamp])                                                                                                   
DateFromNum(12969932400000000)                                                            
2012-01-01 23:00:00 dátumidőt ad eredményül.

---
### <a name="formatdatetime"></a>FormatDateTime
**Funkció:**<br> FormatDateTime(forrás, inputFormat, outputFormat)

**Leírás:**<br> Egy dátumkarakterláncot vesz fel egy formátumból, és más formátumba konvertálja.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Forrás** |Kötelező |Sztring |A forrásobjektum attribútumának általában neve. |
| **inputFormat** |Kötelező |Sztring |A forrásérték várható formátuma. A támogatott formátumokat [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)a témakörben t. |
| **outputFormat** |Kötelező |Sztring |A kimeneti dátum formátuma. |

---
### <a name="guid"></a>Guid
**Funkció:**<br> Guid()

**Leírás:**<br> A Guid függvény új véletlenszerű GUID-t hoz létre

---
### <a name="iif"></a>Iif
**Funkció:**<br> IIF(feltétel;értékHaIgaz,értékHahamis)

**Leírás:**<br> Az IIF függvény egy megadott feltételen alapuló lehetséges értékek egyikét adja vissza.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Feltétel** |Kötelező |Változó vagy kifejezés |Bármely érték vagy kifejezés, amely igaz vagy hamis értékű. |
| **értékHaIgaz** |Kötelező |Változó vagy karakterlánc | Ha a feltétel értéke igaz, a visszaadott érték. |
| **értékHaFalse** |Kötelező |Változó vagy karakterlánc |Ha a feltétel eredménye hamis, a visszaadott érték.|

**Példa:**<br>
IIF([ország]="USA",[ország],[osztály])

---
### <a name="instr"></a>Instr
**Funkció:**<br> InStr(érték1;érték2;kezdés,compareType)

**Leírás:**<br> Az InStr függvény megkeresi egy karakterlánc részkarakterláncának első előfordulását

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **érték1** |Kötelező |Sztring |Keresendő karakterlánc |
| **érték2** |Kötelező |Sztring |A megtalálandó karakterlánc |
| **Elkezd** |Optional |Egész szám |Kezdő pozíció a karakterlánc részhalmazának megkereséséhez|
| **compareType (compareType)** |Optional |Felsorolás |Lehet vbTextCompare vagy vbBinaryCompare |

**Példa:**<br>
InStr("A gyors barna róka","gyors")                                                                             
Eértékek értéke 5-re

InStr("repEated","e",3,vbBinaryCompare)                                                                                  
Kiértékeljük a 7

---
### <a name="isnull"></a>IsNull
**Funkció:**<br> IsNull(kifejezés)

**Leírás:**<br> Ha a kifejezés eredménye Null, akkor az IsNull függvény igaz értéket ad vissza. Egy attribútum esetében a Null értéket az attribútum hiánya fejezi ki.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Kifejezés** |Kötelező |kifejezés |Kiértékelendő kifejezés |

**Példa:**<br>
IsNull([displayName])                                                                                                
Igaz értéket ad vissza, ha az attribútum nincs jelen.

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Funkció:**<br> IsNullOrEmpty(kifejezés)

**Leírás:**<br> Ha a kifejezés null vagy üres karakterlánc, akkor az IsNullOrEmpty függvény igaz értéket ad vissza. Egy attribútum esetében ez igaz értéket ad ki, ha az attribútum hiányzik, vagy jelen van, de üres karakterlánc.
A függvény inverzének neve IsPresent.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Kifejezés** |Kötelező |kifejezés |Kiértékelendő kifejezés |

**Példa:**<br>
IsNullOrEmpty([displayName])                                               
Igaz értéket ad vissza, ha az attribútum nincs jelen, vagy üres karakterlánc.

---
### <a name="ispresent"></a>IsPresent (Jelen van)
**Funkció:**<br> IsPresent(kifejezés)

**Leírás:**<br> Ha a kifejezés nem Null karakterláncot ad eredményül, akkor az IsPresent függvény igaz értéket ad vissza. A függvény inverzneve IsNullOrEmpty.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Kifejezés** |Kötelező |kifejezés |Kiértékelendő kifejezés |

**Példa:**<br>
Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([rendező][igazgató])

---
### <a name="isstring"></a>Karakterlánc
**Funkció:**<br> IsString(kifejezés)

**Leírás:**<br> Ha a kifejezés karakterlánctípusra értékelhető, akkor az IsString függvény értéke Igaz lesz.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Kifejezés** |Kötelező |kifejezés |Kiértékelendő kifejezés |

---
### <a name="item"></a>Elem
**Funkció:**<br> Elem(attribútum, index)

**Leírás:**<br> A Cikk függvény egy elemet ad vissza egy többértékű karakterláncból/attribútumból.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Attribútum** |Kötelező |Attribútum |Keresendő többértékű attribútum |
| **Index** |Kötelező |Egész szám | Index a többértékű karakterlánc egy eleméhez|

**Példa:**<br>
Elem([proxyAddresses], 1)

---
### <a name="join"></a>Csatlakozás
**Funkció:**<br> Csatlakozás(elválasztó, forrás1, forrás2, ...)

**Leírás:**<br> A Join() hasonló a Hozzáfűzés(hez), **source** azzal a különbséggel, hogy több forráskarakterlánc-értéket egyesíthet egyetlen karakterláncba, és minden értéket **elválasztó** karakterlánc választ el egymástól.

Ha az egyik forrásérték többértékű attribútum, akkor az attribútum minden értéke össze lesz kötve, és az elválasztó érték választja el egymástól.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Elválasztó** |Kötelező |Sztring |A karakterlánc a forrásértékek elkülönítésére szolgál, amikor azok egy karakterláncba vannak összefűzve. Lehet "", ha nincs szükség elválasztóra. |
| **forrás1 ... forrásN** |Kötelező, változó számú alkalommal |Sztring |Az összeadandó karakterlánc-értékek. |

---
### <a name="left"></a>Bal
**Funkció:**<br> Balra(Karakterlánc;Hányas karakter)

**Leírás:**<br> A Bal függvény megadott számú karaktert ad vissza a karakterlánc bal oldalán. Ha numChars = 0, üres karakterláncot ad vissza.
Ha a numChars < 0, adja vissza a bemeneti karakterláncot.
Ha a karakterlánc null értékű, üres karakterláncot ad vissza.
Ha a karakterlánc kevesebb karaktert tartalmaz, mint a numChars paraméterben megadott szám, akkor a karakterlánccal (azaz az 1. paraméter összes karakterét tartalmazó) karakterláncot ad vissza.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Sztring** |Kötelező |Attribútum | A karaktereket visszaadó karakterlánc |
| **NumChars** |Kötelező |Egész szám | A karakterlánc elejétől (balra) visszaadandó karakterek számát azonosító szám|

**Példa:**<br>
Balra ("John Doe", 3)                                                            
Visszaadja a "Joh"

---
### <a name="mid"></a>Közepes
**Funkció:**<br> Közép(forrás, kezdés, hossz)

**Leírás:**<br> A forrásérték részkarakterláncát adja eredményül. A részkarakterlánc olyan karakterlánc, amely csak a forráskarakterlánc néhány karakterét tartalmazza.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Forrás** |Kötelező |Sztring |Általában az attribútum neve. |
| **Elkezd** |Kötelező |egész szám |Index a **source** forráskarakterláncban, ahol a karakterlánc részkarakterláncának el kell kezdődnie. A karakterlánc első karaktere indexe 1, a második karakterindex 2 lesz, és így tovább. |
| **Hossza** |Kötelező |egész szám |A karakterlánc részhossza. Ha a hossz a **forráskarakterláncon** kívül reked, a függvény karakterláncot **ad** vissza a kezdőindextől a **forráskarakterlánc** végéig. |

---
### <a name="normalizediacritics"></a>A jelek normalizálása
**Funkció:**<br> A nagykritikusok normalizálása(forrás)

**Leírás:**<br> Egy karakterlánc-argumentumot igényel. A karakterláncot adja vissza, de a diakritikus karaktereket egyenértékű, nem diakritikus karakterekre cseréli. Általában diakritikus karaktereket (ékezetes jeleket) tartalmazó utónevek és vezetéknevek átalakítására szolgálnak olyan jogi értékekké, amelyek különböző felhasználói azonosítókban használhatók, például egyszerű felhasználónevekben, SAM-fióknevekben és e-mail címekben.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Forrás** |Kötelező |Sztring | Általában egy keresztnév vagy vezetéknév attribútum. |

---
### <a name="not"></a>Not
**Funkció:**<br> Not(forrás)

**Leírás:**<br> Megfordítja a **forrás**logikai értékét. Ha a **forrásérték** "*True*", akkor a "*Hamis*" értéket adja vissza. Ellenkező esetben a "*True*" értéket adja vissza.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Forrás** |Kötelező |Logikai karakterlánc |A várt **forrásértékek** "True" vagy "False". |

---
### <a name="numfromdate"></a>NumFromDate (Dátum kezdőszáma)
**Funkció:**<br> NumFromDate(érték)

**Leírás:**<br> A NumFromDate függvény egy DateTime értéket konvertál Active Directory formátumba, amely az olyan attribútumok beállításához szükséges, mint a [accountExpires](https://docs.microsoft.com/windows/win32/adschema/a-accountexpires). Ezzel a funkcióval konvertálhatja a felhőbeli HR-alkalmazásokból , például a Workday-ből és a SuccessFactors-ból kapott DateTime-értékeket azok egyenértékű AD-ábrázolására. 

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **value** |Kötelező | Sztring | A dátumidő-karakterlánc a támogatott formátumban. A támogatott formátumokat https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspxa témakörben t. |

**Példa:**<br>
* Példa munkanapra <br>
  Feltételezve, hogy a *Workday 2020-12-31-08:00* formátumú *ContractEndDate* attribútumot szeretné leképezni az AD *accountExpires* mezőjéhez, így használhatja ezt a függvényt, és módosíthatja az időzóna eltolását a területi beállításnak megfelelően. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* SuccessFactors példa <br>
  Feltételezve, hogy le szeretné képezni a successatényezők endtribute *attribútumát,* amely *M/d/yyyy hh:mm:ss tt* to *accountExpires* mezőben az AD-ben, így használhatja ezt a funkciót, és módosíthatja az időzóna eltolását a területi beállításhoz.
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>Másolateltávolítása
**Funkció:**<br> Duplikált példányok eltávolítása(attribútum)

**Leírás:**<br> A RemoveDuplicates függvény egy többértékű karakterláncot vesz fel, és győződjön meg arról, hogy minden érték egyedi.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Attribútum** |Kötelező |Többértékű attribútum |Többértékű attribútum, amelynek ismétlődéseit eltávolítják|

**Példa:**<br>
RemoveDuplicates([proxyAddresses])                                                                                                       
Sanitized proxyAddress attribútumot ad vissza, amelyben az összes ismétlődő értéket eltávolították.

---
### <a name="replace"></a>Csere
**Funkció:**<br> Csere(forrás; oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**Leírás:**<br>
Lecseréli a karakterláncon belüli értékeket. A megadott paraméterektől függően másképp működik:

* Ha **az oldValue** és **a replacementValue** meg van adva:
  
  * A **forrásban** lévő **oldValue** összes előfordulását **lecseréli a replacementValue értékre**
* Ha **az oldValue** és **a sablon** meg van adva:
  
  * A **sablonban** lévő **régiÉrték** összes előfordulását lecseréli a **forrásértékre**
* Ha **regexPattern** és **replacementValue** vannak megadva:

  * A függvény a **regexPattern-et** alkalmazza a forráskarakterláncra, és a regex csoportnevek segítségével létrehozhatja a **replacementValue karakterláncát.** **source**
* A **regexPattern**, **regexGroupName**, **replacementValue** beállítás esetén a következőket adják meg:
  
  * A függvény a **regexPattern-et** alkalmazza a forráskarakterláncra, és a **regexGroupName-nek** megfelelő összes értéket **lecseréli replacementValue értékre.** **source**
* Ha **regexPattern**, **regexGroupName**, **replacementAttributeName** meg vannak adva:
  
  * Ha **a forrásnak** nincs értéke, a **forrás** t ad vissza
  * Ha **a forrásnak** van értéke, a függvény a **regexPattern-et** alkalmazza a **forráskarakterláncra,** és lecseréli a **regexGroupName-nek** megfelelő összes értéket a **replacementAttributeName** értékkel.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Forrás** |Kötelező |Sztring |A **forrásobjektum** attribútumának általában neve. |
| **régiérték** |Optional |Sztring |A **forrásban** vagy **sablonban**helyettesítendő érték . |
| **regexPattern** |Optional |Sztring |Regex minta a **forrásban**helyettesítendő értékhez . Vagy, ha **replacementPropertyName van** használva, minta kinyerni értéket **replacementPropertyName**. |
| **regexGroupName** |Optional |Sztring |A **regexPattern**csoporton belüli csoport neve . Csak **akkor, ha replacementPropertyName** van használva, akkor kivonjuk a csoport értékét **replacementValue** a **replacementPropertyName**értékként. |
| **replacementValue** |Optional |Sztring |Új érték, amivel a régit helyettesítheti. |
| **replacementAttributeName** |Optional |Sztring |A helyettesítő értékhez használandó attribútum neve |
| **Sablon** |Optional |Sztring |Amikor **sablon** érték van megadva, megkeressük a **régiÉrték** a sablonon belül, és helyette **forrásérték.** |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Funkció:**<br> SelectUniqueValue(uniqueValueRule1; uniqueValueRule2; uniqueValueRule3, ...)

**Leírás:**<br> Legalább két argumentumot igényel, amelyek egyedi értékgenerálási szabályok, amelyeket kifejezések segítségével határoznak meg. A függvény kiértékeli az egyes szabályokat, majd ellenőrzi a létrehozott értéket a célalkalmazásban/könyvtárban. Az első talált egyedi érték a visszaadott érték lesz. Ha az összes érték már létezik a célban, a bejegyzés lesz letéti ben, és az ok a naplónaplókba kerül. A megadható argumentumok számának nincs felső határa.

> [!NOTE]
> - Ez egy legfelső szintű függvény, nem ágyazható be.
> - Ez a függvény nem alkalmazható olyan attribútumokra, amelyeknek megfelelő a prioritásuk.  
> - Ez a függvény csak a bejegyzés-létrehozáshoz használható. Ha attribútummal használja, állítsa a **Leképezés alkalmazása** tulajdonságot **Csak az objektum létrehozása kor**.
> - Ez a funkció jelenleg csak a "Workday to Active Directory felhasználói kiépítés" esetén támogatott. Nem használható más létesítési alkalmazásokkal. 


**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **uniqueValueRule1 ... uniqueValueRuleN** |Legalább 2 szükséges, nincs felső határ |Sztring | Kiértékelhető egyedi értékgenerálási szabályok listája. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Funkció:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Leírás:**<br> Egyetlen appRoleAssignment értéket ad vissza az adott alkalmazás felhasználójának hozzárendelt összes appRoleAssignment listájából. Ez a függvény szükséges az appRoleAssignments objektum egyetlen szerepkörnév-karakterláncmá alakításához. Vegye figyelembe, hogy az ajánlott eljárás annak biztosítása, hogy egyszerre csak egy appRoleAssignment legyen hozzárendelve egy felhasználóhoz, és ha több szerepkör van hozzárendelve, a visszaadott szerepkör-karakterlánc nem lehet kiszámítható. 

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Kötelező |Sztring |**[appRoleAssignments]** objektum. |

---
### <a name="split"></a>Felosztás
**Funkció:**<br> Split(forrás; határoló)

**Leírás:**<br> A karakterláncot többértékű tömbre osztja fel a megadott határoló karakter használatával.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Forrás** |Kötelező |Sztring |**forrásértéket.** |
| **elválasztókarakter** |Kötelező |Sztring |Megadja a karakterlánc felosztásához használt karaktert (például: ",") |

---
### <a name="stripspaces"></a>StripSpaces között
**Funkció:**<br> Csíkozott területek(forrás)

**Leírás:**<br> Eltávolítja az összes szóköz (" ") karaktert a forráskarakterláncból.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Forrás** |Kötelező |Sztring |**forrásértéket.** |

---
### <a name="switch"></a>Kapcsoló
**Funkció:**<br> Kapcsoló(forrás; defaultValue, key1, value1, key2, value2, ...)

**Leírás:**<br> Ha **a forrásérték** megegyezik egy **kulcssal,** az adott **kulcs** **értékét** adja vissza. Ha **a forrásérték** nem egyezik egyetlen billentyűvel sem, akkor **a defaultValue értéket**adja vissza.  **A kulcs-** és **értékparamétereknek** mindig párban kell jönniük. A függvény mindig páros számú paramétert vár el. A függvény nem használható hivatkozási attribútumokhoz, például a kezelőhöz. 

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Forrás** |Kötelező |Sztring |**Frissítandó forrásérték.** |
| **defaultValue** |Optional |Sztring |Alapértelmezett érték, amelyet akkor használ, ha a forrás nem egyezik meg egyetlen kulcssal sem. Lehet üres karakterlánc (""). |
| **Kulcs** |Kötelező |Sztring |**A** **forrásérték** összehasonlításához. |
| **value** |Kötelező |Sztring |A kulcsnak megfelelő **forrás** csereértéke. |

---
### <a name="tolower"></a>ToLower (ToLower)
**Funkció:**<br> ToLower(forrás, kultúra)

**Leírás:**<br> Vesz egy *forrás* karakterlánc értékét, és alakítja át a kisbetűs a kulturális környezet szabályok, amelyek a megadott. Ha nincs megadva *kulturális* adat, akkor az Invariant kulturális környezetet fogja használni.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Forrás** |Kötelező |Sztring |A forrásobjektum attribútumának általában neve |
| **Kultúra** |Optional |Sztring |A 4646-os számú Kulturális környezet nevének formátuma *languagecode2-country/regioncode2,* ahol a *languagecode2* a kétbetűs nyelvkód, *az ország/régiókód2* pedig a kétbetűs szubkultúra kódja. Ilyen például a japán (Japán) és az en-US az angol (Egyesült Államok) esetében. Azokban az esetekben, amikor nem áll rendelkezésre kétbetűs nyelvkód, az ISO 639-2-ből származó hárombetűs kódot kell használni.|

---
### <a name="toupper"></a>Toupper között
**Funkció:**<br> ToUpper(forrás, kultúra)

**Leírás:**<br> Vesz egy *forráskarakterlánc* értékét, és alakítja át a nagybetűs a kulturális környezet szabályok, amelyek meg vannak adva. Ha nincs megadva *kulturális* adat, akkor az Invariant kulturális környezetet fogja használni.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Forrás** |Kötelező |Sztring |A forrásobjektum attribútumának általában neve. |
| **Kultúra** |Optional |Sztring |A 4646-os számú Kulturális környezet nevének formátuma *languagecode2-country/regioncode2,* ahol a *languagecode2* a kétbetűs nyelvkód, *az ország/régiókód2* pedig a kétbetűs szubkultúra kódja. Ilyen például a japán (Japán) és az en-US az angol (Egyesült Államok) esetében. Azokban az esetekben, amikor nem áll rendelkezésre kétbetűs nyelvkód, az ISO 639-2-ből származó hárombetűs kódot kell használni.|

---
### <a name="word"></a>Word
**Funkció:**<br> Szó(karakterlánc;WordNumber;Határolók)

**Leírás:**<br> A Word függvény egy karakterláncban lévő szót ad vissza a használandó határolókat és a visszaadandó szószámot leíró paraméterek alapján. A határolójelek ben lévő karakterek egyikével elválasztott karakterlánc-karakterláncokat a következő szavakként azonosítják:

Ha a szám < 1, üres karakterláncot ad vissza.
Ha a karakterlánc null értékű, üres karakterláncot ad vissza.
Ha a karakterlánc kevesebb, mint számszavakat tartalmaz, vagy a karakterlánc nem tartalmaz határolószavakat, a program üres karakterláncot ad vissza.

**Paraméterek:**<br> 

| Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **Sztring** |Kötelező |Többértékű attribútum |A szót visszaadandó karakterlánc.|
| **WordNumber** |Kötelező | Egész szám | Azazt azonosító szám, hogy melyik szószámot kell visszaadni|
| **határolók** |Kötelező |Sztring| A szavak azonosítására használandó határoló(k)at jelölő karakterlánc|

**Példa:**<br>
Szó("A gyors barna róka",3," ")                                                                                       
"Barna" értéket ad eredményül.

Word("This,string!has&many separators",3,",!&#")                                                                       
"Van" értéket ad eredményül.

---

## <a name="examples"></a>Példák
### <a name="strip-known-domain-name"></a>Ismert tartománynév csíkozása
A felhasználónév megszerzéséhez le kell választania egy ismert tartománynevet a felhasználó e-mail címéről. <br>
Ha például a tartomány "contoso.com", akkor a következő kifejezést használhatja:

**Kifejezés:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Minta bemenete / kimenete:** <br>

* **BEMENET** (mail):john.doe@contoso.com" "
* **KIMENET**: "john.doe"

### <a name="append-constant-suffix-to-user-name"></a>Állandó utótag hozzáfűzése a felhasználónévhez
Ha Salesforce sandboxot használ, előfordulhat, hogy a szinkronizálás előtt további utótagot kell hozzáfűzött az összes felhasználónévhez.

**Kifejezés:** <br>
`Append([userPrincipalName], ".test")`

**Minta bemenete/kimenete:** <br>

* **BEMENET**: (userPrincipalName): " "John.Doe@contoso.com
* **KIMENET**:John.Doe@contoso.com.test" "

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Felhasználói alias létrehozása a vezeték- és keresztnév részeinek összefűzésével
Felhasználói aliast kell létrehoznia a felhasználó keresztnevének első 3 betűjével és a felhasználó vezetéknevének első 5 betűjével.

**Kifejezés:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Minta bemenete/kimenete:** <br>

* **BEMENET** (givenName): "János"
* **BEMENET** (vezetéknév): "Doe"
* **KIMENET**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Mellékjelek eltávolítása karakterláncból
Az ékezetes jeleket tartalmazó karaktereket olyan egyenértékű karakterekre kell cserélni, amelyek nem tartalmaznak ékezetes jeleket.

**Kifejezés:** <br>
NormalizáljaATónus-jelek([givenName])

**Minta bemenete/kimenete:** <br>

* **BEMENET** (givenName): "Zoë"
* **KIMENET**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Karakterlánc felosztása többértékű tömbre
Vesszővel tagolt karakterlánclistát kell választania, és fel kell osztania őket egy tömbre, amely egy többértékű attribútumhoz, például a Salesforce PermissionSets attribútumához csatlakoztatható. Ebben a példában az engedélykészletek listáját az Azure AD-ben az Attribute5 bővítményben töltötték fel.

**Kifejezés:** <br>
Split([extensionAttribute5], ",")

**Minta bemenete/kimenete:** <br>

* **BEMENET** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **OUTPUT**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Kimeneti dátum karakterláncként egy bizonyos formátumban
Dátumokat szeretne küldeni egy SaaS-alkalmazásnak egy bizonyos formátumban. <br>
Például a ServiceNow dátumait szeretné formázni.

**Kifejezés:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Minta bemenete/kimenete:**

* **BEMENET** (extensionAttribute1): "20150123105347.1Z"
* **KIBOCSÁTÁS**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Érték cseréje előre meghatározott beállítások alapján

Meg kell határoznia a felhasználó időzónáját az Azure AD-ben tárolt állapotkód alapján. <br>
Ha az állapotkód nem egyezik az előre definiált beállítások egyikével sem, használja az "Ausztrália/Sydney" alapértelmezett értéket.

**Kifejezés:** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Minta bemenete/kimenete:**

* **BEMENET** (állapot): "QLD"
* **OUTPUT**: "Ausztrália/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Karakterek cseréje reguláris kifejezéssel
Meg kell találnia azokat a karaktereket, amelyek megfelelnek a reguláris kifejezés értékének, és el kell távolítania őket.

**Kifejezés:** <br>

Replace([mailNickname], , "[a-zA-Z_]*", "", , )

**Minta bemenete/kimenete:**

* **BEMENET** (mailBecenév: "john_doe72"
* **KIMENET**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Létrehozott userPrincipalName (UPN) érték átalakítása kisbetűssé
Az alábbi példában az UPN-értéket a PreferredFirstName és a PreferredLastName forrásmezők összefűzésével hozza létre, és a ToLower függvény a létrehozott karakterláncon működik, hogy az összes karaktert kisbetűssé konvertálja. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Minta bemenete/kimenete:**

* **BEMENET** (PreferredFirstName): "János"
* **BEMENET** (PreferredLastName): "Smith"
* **KIMENET**:john.smith@contoso.com" "

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Egyedi érték létrehozása a userPrincipalName (UPN) attribútumhoz
A felhasználó keresztneve, középső neve és vezetékneve alapján létre kell hoznia egy értéket az UPN attribútumhoz, és ellenőriznie kell annak egyediségét a cél AD könyvtárban, mielőtt hozzárendeli az értéket az UPN attribútumhoz.

**Kifejezés:** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Minta bemenete/kimenete:**

* **BEMENET** (PreferredFirstName): "János"
* **BEMENET** (PreferredLastName): "Smith"
* **OUTPUT**:John.Smith@contoso.com" " ", ha az John.Smith@contoso.com UPN értéke még nem létezik a címtárban
* **OUTPUT**:J.Smith@contoso.com" ", John.Smith@contoso.com ha már létezik upn érték a címtárban
* **OUTPUT**:Jo.Smith@contoso.com" ", ha a fenti két UPN érték már létezik a címtárban

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>Flow mail érték, ha nem NULL, egyébként flow userPrincipalName
Ha jelen van, a mail attribútumot szeretné folyatni. Ha nem, akkor a userPrincipalName értékét szeretné folyatni.

**Kifejezés:** <br>
`Coalesce([mail],[userPrincipalName])`

**Minta bemenete/kimenete:** <br>

* **BEMENET** (mail): NULL
* **BEMENET** (userPrincipalName):John.Doe@contoso.com" "
* **KIMENET**:John.Doe@contoso.com" "

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Felhasználói kiépítés/sas-alkalmazásokba való kiépítés automatizálása](../app-provisioning/user-provisioning.md)
* [Attribútumleképezések testreszabása a felhasználók kiépítéséhez](../app-provisioning/customize-application-attributes.md)
* [Hatókörszűrők a felhasználók kiépítéshez](define-conditional-rules-for-provisioning-user-accounts.md)
* [SCIM használata a felhasználók és csoportok automatikus üzembe helyezésének engedélyezéséhez az Azure Active Directoryból az alkalmazásokba](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Alkalmazáskiépítési értesítések](../app-provisioning/user-provisioning.md)
* [Az SaaS-alkalmazások integrálásával foglalkozó oktatóanyagok listája](../saas-apps/tutorial-list.md)
