---
title: Az Azure AD Connect felhőalapú kiépítési kifejezései és függvényhivatkozása
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78298615"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Kifejezések írása attribútumleképezéshez az Azure Active Directoryban
A felhőalapú kiépítés konfigurálásakor az attribútumleképezések egyik megadott típusa egy kifejezésleképezés. 

A kifejezésleképezés lehetővé teszi az attribútumok parancsfájlszerű kifejezéssel történő testreszabását.  Ez lehetővé teszi, hogy a helyszíni adatokat új vagy eltérő értékké alakítsa át.  Előfordulhat például, hogy két attribútumot szeretne egyetlen attribútumba egyesíteni, mivel ezt az attribútumot az egyik felhőalapú alkalmazás használja.

A következő dokumentum az adatok átalakításához használt parancsfájlszerű kifejezéseket tartalmazza.  Ez csak egy része a folyamatnak.  Ezután ezt a kifejezést kell használnia, és helyezze el egy webes kérelemben a bérlőnek.  Erről további információt az [Átalakítások című](how-to-transformation.md) témakörben talál.

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

## <a name="list-of-functions"></a>A függvények listája
| A függvények listája | Leírás |
|-----|----|
|[Hozzáfűzés](#append)|Vesz egy forráskarakterlánc értékét, és hozzáfűzi az utótagot a végén.|
|[Bitand (bitand)](#bitand)|A BitAnd függvény megadott biteket állít be egy értékre.|
|[Kbool](#cbool)|A CBool függvény egy logikai értéket ad vissza a kiértékelt kifejezés alapján.|
|[ConvertFromBase64](#convertfrombase64)|A ConvertFromBase64 függvény a megadott base64 kódolású értéket normál karakterláncsá alakítja.|
|[KonvertálásBase64](#converttobase64)|A ConvertToBase64 függvény egy karakterláncot Unicode base64 karakterláncgá alakít át. |
|[ConvertToUTF8Hex](#converttoutf8hex)|A ConvertToUTF8Hex függvény egy karakterláncot UTF8 Hex kódolású értékké alakít át.|
|[Darabszám](#count)|A Count függvény egy többértékű attribútum elemeinek számát adja eredményül.|
|[Cstr között](#cstr)|A CStr függvény karakterlánc-adattípussá alakul át.|
|[DateFromNum (Dátumfromnum)](#datefromnum)|A DateFromNum függvény az AD dátumformátumában lévő értéket DateTime típussá alakítja.|
|[DNösszetevő](#dncomponent)|A DNComponent függvény egy megadott, balról haladó DN-összetevő értékét adja eredményül.|
|[Hiba](#error)|A Hiba funkció egyéni hiba visszaadására szolgál.|
|[FormatDateTime](#formatdatetime) |Egy dátumkarakterláncot vesz fel egy formátumból, és más formátumba konvertálja.| 
|[Guid](#guid)|Az Guid függvény új véletlenszerű GUID-ot hoz létre.|           
|[Iif](#iif)|Az IIF függvény egy megadott feltételen alapuló lehetséges értékek egyikét adja vissza.|
|[Instr](#instr)|Az InStr függvény megkeresi egy karakterlánc részkarakterláncának első előfordulását.|
|[IsNull](#isnull)|Ha a kifejezés eredménye Null, akkor az IsNull függvény igaz értéket ad vissza.|
|[IsNullOrEmpty](#isnullorempty)|Ha a kifejezés null vagy üres karakterlánc, akkor az IsNullOrEmpty függvény igaz értéket ad vissza.|         
|[IsPresent (Jelen van)](#ispresent)|Ha a kifejezés nem Null karakterláncot ad eredményül, akkor az IsPresent függvény igaz értéket ad vissza.|    
|[Karakterlánc](#isstring)|Ha a kifejezés karakterlánctípusra értékelhető, akkor az IsString függvény értéke Igaz lesz.|
|[Elem](#item)|A Cikk függvény egy elemet ad vissza egy többértékű karakterláncból/attribútumból.|
|[Csatlakozás](#join) |A Join() hasonló a Hozzáfűzés(hez), **source** azzal a különbséggel, hogy több forráskarakterlánc-értéket egyesíthet egyetlen karakterláncba, és minden értéket **elválasztó** karakterlánc választ el egymástól.| 
|[Bal](#left)|A Bal függvény megadott számú karaktert ad vissza a karakterlánc bal oldalán.|
|[Közepén](#mid) |A forrásérték részkarakterláncát adja eredményül. A részkarakterlánc olyan karakterlánc, amely csak a forráskarakterlánc néhány karakterét tartalmazza.|
|[A jelek normalizálása](#normalizediacritics)|Egy karakterlánc-argumentumot igényel. A karakterláncot adja vissza, de a diakritikus karaktereket egyenértékű, nem diakritikus karakterekre cseréli.|
|[Not](#not) |Megfordítja a **forrás**logikai értékét. Ha a **forrásérték** "*True*", akkor a "*Hamis*" értéket adja vissza. Ellenkező esetben a "*True*" értéket adja vissza.| 
|[Másolateltávolítása](#removeduplicates)|A RemoveDuplicates függvény egy többértékű karakterláncot vesz fel, és győződjön meg arról, hogy minden érték egyedi.| 
|[Helyettesít](#replace) |Lecseréli a karakterláncon belüli értékeket. | 
|[SelectUniqueValue](#selectuniquevalue)|Legalább két argumentumot igényel, amelyek egyedi értékgenerálási szabályok, amelyeket kifejezések segítségével határoznak meg. A függvény kiértékeli az egyes szabályokat, majd ellenőrzi a létrehozott értéket a célalkalmazásban/könyvtárban.| 
|[SingleAppRoleAssignment](#singleapproleassignment)|Egyetlen appRoleAssignment értéket ad vissza az adott alkalmazás felhasználójának hozzárendelt összes appRoleAssignment listájából.| 
|[Split](#split)|A karakterláncot többértékű tömbre osztja fel a megadott határoló karakter használatával.|
|[KarakterláncFROMSID](#stringfromsid)|A StringFromSid függvény egy biztonsági azonosítót tartalmazó bájttömböt konvertál karakterláncsá.| 
|[StripSpaces között](#stripspaces) |Eltávolítja az összes szóköz (" ") karaktert a forráskarakterláncból.| 
|[Kapcsoló](#switch)|Ha **a forrásérték** megegyezik egy **kulcssal,** az adott **kulcs** **értékét** adja vissza. | 
|[ToLower (ToLower)](#tolower)|Vesz egy *forrás* karakterlánc értékét, és alakítja át a kisbetűs a kulturális környezet szabályok, amelyek a megadott.| 
|[Toupper között](#toupper)|Vesz egy *forráskarakterlánc* értékét, és alakítja át a nagybetűs a kulturális környezet szabályok, amelyek meg vannak adva.|
|[Berendezés](#trim)|A Vágás funkció eltávolítja a kezdő és záró fehér szóközöket a karakterláncból.|
|[Word](#word)|A Word függvény egy karakterláncban lévő szót ad vissza a használandó határolókat és a visszaadandó szószámot leíró paraméterek alapján.|

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
**Leírás:**  
A BitAnd függvény megadott biteket állít be egy értékre.

**Szintaxis:**  
`num BitAnd(num value1, num value2)`

* érték1, érték2: numerikus értékek, amelyeket együtt kell

**Megjegyzések:**  
Ez a függvény mindkét paramétert bináris reprezentációvá alakítja, és egy kicsit a következőkre állítja be:

* 0 - ha az egyik vagy mindkét megfelelő bit *érték1* és *érték2* 0
* 1 - ha mindkét megfelelő bit 1.

Más szóval minden esetben 0 értéket ad vissza, kivéve, ha mindkét paraméter megfelelő bitje 1.

**Példa:**  
 
 `BitAnd(&HF, &HF7)`</br>
 7-et ad eredményül, mert hexadecimális "F" és "F7" kiértékelt értéket erre az értékre.

---

### <a name="cbool"></a>Kbool
**Leírás:**  
A CBool függvény egy logikai értéket ad vissza a kiértékelt kifejezés alapján.

**Szintaxis:**  
`bool CBool(exp Expression)`

**Megjegyzések:**  
Ha a kifejezés nem nulla értéket ad eredményül, akkor a CBool függvény igaz értéket ad vissza, máskülönben hamis értéket ad vissza.

**Példa:**  
`CBool([attrib1] = [attrib2])`  

Igaz értéket ad vissza, ha mindkét attribútum értéke megegyezik.

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**Leírás:**  
A ConvertFromBase64 függvény a megadott base64 kódolású értéket normál karakterláncsá alakítja.

**Szintaxis:**  
`str ConvertFromBase64(str source)`- feltételezi, Unicode kódolás  
`str ConvertFromBase64(str source, enum Encoding)`

* forrás: Base64 kódolású karakterlánc  
* Kódolás: Unicode, ASCII, UTF8

**Példa**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Mindkét példa vissza "*Hello world!*"

---
### <a name="converttobase64"></a>KonvertálásBase64
**Leírás:**  
A ConvertToBase64 függvény egy karakterláncot Unicode base64 karakterláncgá alakít át.  
Egy egész tömb értékét a 64-es alapjegyekkel kódolt karakterlánc-ábrázolási értékké alakítja.

**Szintaxis:**  
`str ConvertToBase64(str source)`

**Példa:**  
`ConvertToBase64("Hello world!")`  
Eredmény: "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Leírás:**  
A ConvertToUTF8Hex függvény egy karakterláncot UTF8 Hex kódolású értékké alakít át.

**Szintaxis:**  
`str ConvertToUTF8Hex(str source)`

**Megjegyzések:**  
A függvény kimeneti formátumát az Azure Active Directory dn attribútumformátumként használja.

**Példa:**  
`ConvertToUTF8Hex("Hello world!")`  
Eredménye 48656C6C6F20776F726C6421

---
### <a name="count"></a>Darabszám
**Leírás:**  
A Count függvény egy többértékű attribútum elemeinek számát adja eredményül.

**Szintaxis:**  
`num Count(mvstr attribute)`

---
### <a name="cstr"></a>CStr
**Leírás:**  
A CStr függvény karakterlánc-adattípussá alakul át.

**Szintaxis:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* érték: Lehet numerikus érték, hivatkozási attribútum vagy logikai érték.

**Példa:**  
`CStr([dn])`  
Visszaadhatja a "cn=Joe,dc=contoso,dc=com"

---
### <a name="datefromnum"></a>DateFromNum (Dátumfromnum)
**Leírás:**  
A DateFromNum függvény az AD dátumformátumában lévő értéket DateTime típussá alakítja.

**Szintaxis:**  
`dt DateFromNum(num value)`

**Példa:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
2012-01-01 23:00:00 dátumidőt ad eredményül.

---
### <a name="dncomponent"></a>DNösszetevő
**Leírás:**  
A DNComponent függvény egy megadott, balról haladó DN-összetevő értékét adja eredményül.

**Szintaxis:**  
`str DNComponent(ref dn, num ComponentNumber)`

* dn: a értelmezhető referenciaattribútum
* ComponentNumber: A DN visszaadandó összetevője

**Példa:**  
`DNComponent(CRef([dn]),1)`  
Ha a dn "cn=Joe,ou=...", akkor Joe-t ad vissza.

---
### <a name="error"></a>Hiba
**Leírás:**  
A Hiba funkció egyéni hiba visszaadására szolgál.

**Szintaxis:**  
`void Error(str ErrorMessage)`

**Példa:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Ha a accountName attribútum nem található, jelenítsen meg egy hibát az objektumon.

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
**Leírás:**  
A Guid függvény új véletlenszerű GUID-t hoz létre

**Szintaxis:**  
`str Guid()`

---
### <a name="iif"></a>Iif
**Leírás:**  
Az IIF függvény egy megadott feltételen alapuló lehetséges értékek egyikét adja vissza.

**Szintaxis:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* feltétel: bármely érték vagy kifejezés, amely igaz vagy hamis értékű.
* valueIfTrue: Ha a feltétel értéke igaz, a visszaadott érték.
* valueIfFalse: Ha a feltétel értéke hamis, a visszaadott érték.

**Példa:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Ha a felhasználó gyakornok, akkor egy olyan felhasználó aliasát adja vissza, akinek a elején "t-" van hozzáadva, máshol pedig a felhasználó aliasát adja vissza.

---
### <a name="instr"></a>Instr
**Leírás:**  
Az InStr függvény megkeresi egy karakterlánc részkarakterláncának első előfordulását

**Szintaxis:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* stringcheck: keresendő karakterlánc
* stringmatch: karakterlánc található
* start: kezdő pozíció a karakterlánc részhalmazának megkereséséhez
* összehasonlítás: vbTextCompare vagy vbBinaryCompare

**Megjegyzések:**  
Azt a pozíciót adja eredményül, ahol a karakterláncrész található, vagy a 0 értéket, ha nem.

**Példa:**  
`InStr("The quick brown fox","quick")`  
Eértékek értéke 5-re

`InStr("repEated","e",3,vbBinaryCompare)`  
Kiértékeljük a 7

---
### <a name="isnull"></a>IsNull
**Leírás:**  
Ha a kifejezés eredménye Null, akkor az IsNull függvény igaz értéket ad vissza.

**Szintaxis:**  
`bool IsNull(var Expression)`

**Megjegyzések:**  
Egy attribútum esetében a Null értéket az attribútum hiánya fejezi ki.

**Példa:**  
`IsNull([displayName])`  
Igaz értéket ad vissza, ha az attribútum nem szerepel a CS vagy az MV értékben.

---
### <a name="isnullorempty"></a>IsNullOrEmpty
**Leírás:**  
Ha a kifejezés null vagy üres karakterlánc, akkor az IsNullOrEmpty függvény igaz értéket ad vissza.

**Szintaxis:**  
`bool IsNullOrEmpty(var Expression)`

**Megjegyzések:**  
Egy attribútum esetében ez igaz értéket ad ki, ha az attribútum hiányzik, vagy jelen van, de üres karakterlánc.  
A függvény inverzének neve IsPresent.

**Példa:**  
`IsNullOrEmpty([displayName])`  
Igaz értéket ad vissza, ha az attribútum nincs jelen, vagy üres karakterlánc a CS-ben vagy az MV-ben.

---
### <a name="ispresent"></a>IsPresent (Jelen van)
**Leírás:**  
Ha a kifejezés nem Null karakterláncot ad eredményül, akkor az IsPresent függvény igaz értéket ad vissza.

**Szintaxis:**  
`bool IsPresent(var expression)`

**Megjegyzések:**  
A függvény inverzneve IsNullOrEmpty.

**Példa:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Elem
**Leírás:**  
A Cikk függvény egy elemet ad vissza egy többértékű karakterláncból/attribútumból.

**Szintaxis:**  
`var Item(mvstr attribute, num index)`

* attribútum: többértékű attribútum
* index: index a többértékű karakterlánc egy eleméhez.

**Megjegyzések:**  
A Cikk függvény a Tartalmazza függvénnyel együtt hasznos, mivel az utóbbi függvény visszaadja az indexet a többértékű attribútum egy elemének.

Hibát okoz, ha az index kívül esik a határokon.

**Példa:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Az elsődleges e-mail címet adja vissza.

---
### <a name="isstring"></a>Karakterlánc
**Leírás:**  
Ha a kifejezés karakterlánctípusra értékelhető, akkor az IsString függvény értéke Igaz lesz.

**Szintaxis:**  
`bool IsString(var expression)`

**Megjegyzések:**  
Annak meghatározására szolgál, hogy a CStr() sikeres lehet-e a kifejezés elemzéséhez.

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
**Leírás:**  
A Bal függvény megadott számú karaktert ad vissza a karakterlánc bal oldalán.

**Szintaxis:**  
`str Left(str string, num NumChars)`

* karakterlánc: az a karakterlánc, amelyből a karaktereket ad vissza
* NumChars: a karakterlánc elejétől (balra) visszaadandó karakterek számát azonosító szám

**Megjegyzések:**  
Karakterlánc első numChars karakterét tartalmazó karakterlánc:

* Ha numChars = 0, üres karakterláncot ad vissza.
* Ha a numChars < 0, adja vissza a bemeneti karakterláncot.
* Ha a karakterlánc null értékű, üres karakterláncot ad vissza.

Ha a karakterlánc kevesebb karaktert tartalmaz, mint a numChars paraméterben megadott szám, akkor a karakterlánccal (azaz az 1. paraméter összes karakterét tartalmazó) karakterláncot ad vissza.

**Példa:**  
`Left("John Doe", 3)`  
Visszatér `Joh`.

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
### <a name="removeduplicates"></a>Másolateltávolítása
**Leírás:**  
A RemoveDuplicates függvény egy többértékű karakterláncot vesz fel, és győződjön meg arról, hogy minden érték egyedi.

**Szintaxis:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Példa:**  
`RemoveDuplicates([proxyAddresses])`  
Egy fertőtlenített proxyAddress attribútumot ad vissza, amelyben az összes ismétlődő értéket eltávolították.

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
  |--- | --- | --- | --- |
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
### <a name="stringfromsid"></a>Karakterláncszisid
**Leírás:**  
A StringFromSid függvény egy biztonsági azonosítót tartalmazó bájttömböt konvertál karakterláncsá.

**Szintaxis:**  
`str StringFromSid(bin ObjectSID)`  

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

**Leírás:**<br> Ha **a forrásérték** megegyezik egy **kulcssal,** az adott **kulcs** **értékét** adja vissza. Ha **a forrásérték** nem egyezik egyetlen billentyűvel sem, akkor **a defaultValue értéket**adja vissza.  **A kulcs-** és **értékparamétereknek** mindig párban kell jönniük. A függvény mindig páros számú paramétert vár el.

**Paraméterek:**<br> 

   | Név | Kötelező/ Ismétlődő | Típus | Megjegyzések |
   | --- | --- | --- | --- |
   | **Forrás** |Kötelező |Sztring |**Ellenőrizve** a forrásérték. |
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

### <a name="trim"></a>Trim
**Leírás:**  
A Vágás funkció eltávolítja a kezdő és záró fehér szóközöket a karakterláncból.

**Szintaxis:**  
`str Trim(str value)`  

**Példa:**  
`Trim(" Test ")`  
A "Teszt" értéket adja eredményül.

`Trim([proxyAddresses])`  
Eltávolítja a proxyAddress attribútum egyes értékeinek kezdő és záró szóközeit.

---
### <a name="word"></a>Word
**Leírás:**  
A Word függvény egy karakterláncban lévő szót ad vissza a használandó határolókat és a visszaadandó szószámot leíró paraméterek alapján.

**Szintaxis:**  
`str Word(str string, num WordNumber, str delimiters)`

* karakterlánc: az a karakterlánc, amelyből egy szót vissza kell adni.
* WordNumber: egy szám, amely meghatározza, hogy melyik szószámot kell visszaadni.
* határolók: a határolójel(ek)et jelölő karakterlánc, amelyet a szavak azonosítására kell használni

**Megjegyzések:**  
A határolójelek ben lévő karakterek egyikével elválasztott karakterlánc-karakterláncokat a következő szavakként azonosítják:

* Ha a szám < 1, üres karakterláncot ad vissza.
* Ha a karakterlánc null értékű, üres karakterláncot ad vissza.

Ha a karakterlánc kevesebb, mint számszavakat tartalmaz, vagy a karakterlánc nem tartalmaz határolószavakat, a program üres karakterláncot ad vissza.

**Példa:**  
`Word("The quick brown fox",3," ")`  
"Barna" értéket ad eredményül.

`Word("This,string!has&many separators",3,",!&#")`  
Visszatérne "van"

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


## <a name="next-steps"></a>További lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-provisioning.md)
