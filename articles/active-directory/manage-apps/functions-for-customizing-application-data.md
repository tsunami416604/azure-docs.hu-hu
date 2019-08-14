---
title: Az Azure Active Directoryban attribútumleképezések kifejezések írása |} A Microsoft Docs
description: Ismerje meg, hogyan SaaS-alkalmazás objektumok az Azure Active Directoryban az Automatikus kiépítés során elfogadható formátumot attribútumértékek alakítsa át a kifejezés-leképezések használatával.
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
ms.openlocfilehash: 3524f34773f4627dff478ee7cc9cbff9f674bf8e
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68931767"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Az Azure Active Directoryban attribútumleképezések kifejezések írása
Amikor konfigurál egy SaaS-alkalmazáshoz való üzembe helyezést, az Ön által megadott attribútum-leképezéshez típusú egyik egy kifejezés-hozzárendelést. Ezeknél a parancsfájl-szerű kifejezés, amely lehetővé teszi, hogy a felhasználók adatokat alakíthatja, amelyek esetében a SaaS-alkalmazás több elfogadható formátumok kell írnia.

## <a name="syntax-overview"></a>Szintaxis áttekintése
Attribútum-leképezéshez kifejezések szintaxisa reminiscent a Visual Basic függvényeihez kapcsolódó alkalmazások (VBA).

* A teljes kifejezésnek funkciók, amelyek követi zárójelben argumentumok nevét kell meghatározni: <br>
  *Függvénynév (`<<argument 1>>`,`<<argument N>>`)*
* Függvények beágyazhatók egymásba előfordulhat, hogy ágyazhatók egymásba. Példa: <br> *FunctionOne (FunctionTwo (`<<argument1>>`))*
* Az funkciók három eltérő típusú argumentumokat adhat át:
  
  1. Attribútumok, amelyek szögletes zárójelek közé kell tenni. Például: [attributeName]
  2. A karakterlánc-állandókat, amelyek dupla idézőjelek közé kell tenni. Példa: "Egyesült Államok"
  3. Más funkciók. Példa: FunctionOne (`<<argument1>>`, FunctionTwo (`<<argument2>>`))
* A karakterlánc-állandókat Ha egy fordított perjel (\) vagy az idézőjel (") a karakterláncban van szüksége, kell megjelölni a fordított perjel (\) szimbólum. Példa: "Cég neve: \\"Contoso\\" "

## <a name="list-of-functions"></a>Függvények listája.
[Hozzáfűzés](#append) &nbsp; [](#formatdatetime) FormatDateTime csatlakoztatása&nbsp; [](#join) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [A Mid](#mid) &nbsp; [](#normalizediacritics) [](#not) NormalizeDiacritics nem &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [](#selectuniquevalue) [](#replace) SelectUniqueValue cseréje &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [ SingleAppRoleAssignment](#singleapproleassignment) &nbsp; [felosztott](#split)[](#stripspaces) StripSpaces&nbsp;&nbsp; &nbsp; &nbsp; &nbsp;&nbsp;&nbsp; &nbsp; &nbsp; &nbsp; [](#tolower) [](#switch) ToLowerváltása&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [ToUpper](#toupper)

---
### <a name="append"></a>Hozzáfűzés
**Függvény:**<br> Append(Source, suffix)

**Leírás:**<br> Forrás karakterlánc értéket vesz fel, és az utótagot fűz a végén.

**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Karakterlánc |Általában az attribútum neve, az adatforrás-objektum. |
| **suffix** |Szükséges |Karakterlánc |Az adatforrás-értéke végére hozzáfűzni kívánt karakterlánc. |

---
### <a name="formatdatetime"></a>formatDateTime
**Függvény:**<br> FormatDateTime (forrás, inputFormat, outputFormat)

**Leírás:**<br> Egy adott formátumból egy dátum karakterláncot vesz fel, és a egy másik formátumba konvertálja.

**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Karakterlánc |Általában az attribútum neve, az adatforrás-objektum. |
| **inputFormat** |Szükséges |Karakterlánc |Az adatforrás-értéke formátumával. Tekintse meg a támogatott formátumok [ https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx ](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Szükséges |Karakterlánc |A kimeneti dátum formátuma. |

---
### <a name="join"></a>Csatlakozás
**Függvény:**<br> Csatlakozás (elválasztó, source1, source2,...)

**Leírás:**<br> JOIN() hasonlít Append(), azzal a különbséggel, hogy több képes kombinálni **forrás** karakterláncot egyetlen karakterlánccá értéket, majd az egyes értékek fogja elválasztani a **elválasztó** karakterlánc.

Ha a forrásadatok egyike egy többértékű attribútum, akkor az adott attribútum minden értéke együtt lesz egyesítve, az elválasztó értékkel elválasztva.

**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **separator** |Szükséges |Karakterlánc |Forrásértékek elválasztó, amikor azok vannak összefűzött egy karakterlánc, karakterlánc. Lehet "", ha nincs elválasztó nem szükséges. |
| **source1... sourceN** |Szükség esetén a változó-hányszor |Karakterlánc |A karakterlánc-értékek egymáshoz csatlakoztatni. |

---
### <a name="mid"></a>Mid
**Függvény:**<br> Mid (forrás, indítás, hossz)

**Leírás:**<br> Az érték egy részét adja vissza. Egy karakterláncrészletet csak egy részét a forrás-karakterlánc származó karaktereket tartalmazó karakterláncot.

**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Karakterlánc |Általában az attribútum neve. |
| **start** |Szükséges |egész szám |Az index a **forrás** karakterláncot, ahol karakterláncrészletet el kell indulnia. A karakterlánc első karaktere 1 index fog rendelkezni, a második karaktere index 2 rendelkezik, és így tovább. |
| **Hossza** |Szükséges |egész szám |A karakterláncrész hossza. Ha hossza kívül ér véget a **forrás** karakterlánc, a függvény a karakterláncrészt adja vissza **indítsa el** végén indextől **forrás** karakterlánc. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Függvény:**<br> NormalizeDiacritics(source)

**Leírás:**<br> Egy karakterlánc-argumentum szükséges. A karakterláncot ad vissza, de bármilyen diakritikus karakterrel egyenértékű nem diakritikus karakterek cserélni. Jogi különböző felhasználói azonosítókat, például az egyszerű felhasználónevek, SAM-fiók nevét és e-mail címek felhasználható értékekké jellemzően keresztnevét és vezetéknevét diakritikus karaktert (ékezetes) konvertálni.

**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Sztring | Általában utónév vagy vezetéknév attribútum. |

---
### <a name="not"></a>nem
**Függvény:**<br> Not(Source)

**Leírás:**<br> Logikai érték, amely tükrözi a **forrás**. Ha **forrás** értéke "*igaz*", adja vissza "*hamis*". Ellenkező esetben adja vissza "*igaz*".

**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Logikai típusú karakterlánc |A várt **források** értéke "true" vagy "false". |

---
### <a name="replace"></a>Csere
**Függvény:**<br> Cserélje le a (forrás, oldValue, regexPattern, regexGroupName, helyettesítő értéke, replacementAttributeName, sablon)

**Leírás:**<br>
Lecseréli az értékeket egy karakterláncból. A megadott paraméterek függően eltérően működik:

* Amikor **oldValue** és **helyettesítő értéke** biztosított:
  
  * Lecseréli a **forrásban** lévő **OldValue** összes előfordulását a **replacementValue**
* Amikor **oldValue** és **sablon** biztosított:
  
  * Összes előfordulását lecseréli a **oldValue** a a **sablon** együtt a **forrás** érték
* A **regexPattern** és a **replacementValue** megadása esetén:

  * A függvény a **regexPattern** alkalmazza a **forrás** sztringre, és a regex-csoportok nevét használhatja a **replacementValue** karakterlánc létrehozásához.
* Amikor **regexPattern**, **regexGroupName**, **helyettesítő értéke** biztosított:
  
  * A függvény a **regexPattern** alkalmazza a **forrás** sztringre, és lecseréli az összes olyan értéket, amely megfelel a **regexGroupName** a **replacementValue**
* A **regexPattern**, a **regexGroupName**és a **replacementAttributeName** megadásakor:
  
  * Ha **forrás** nem rendelkezik értékkel, **forrás** adja vissza
  * Ha a **forrás** értékkel rendelkezik, a függvény a **regexPattern** alkalmazza a **forrás** sztringre, és lecseréli az összes olyan értéket, amely megfelel a **regexGroupName** a **replacementAttributeName** társított értéknek.

**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Sztring |Az attribútum neve általában a **forrásoldali** objektumban. |
| **oldValue** |Optional |Karakterlánc |Értéket kell cserélni a **forrás** vagy **sablon**. |
| **regexPattern** |Optional |Karakterlánc |Az érték cseréli a Regex minta **forrás**. Vagy ha **replacementPropertyName** használ, a **replacementPropertyName**származó érték kinyerésére szolgáló minta. |
| **regexGroupName** |Optional |Karakterlánc |A csoport a nevét **regexPattern**. Csak **replacementPropertyName** használata esetén a csoport értékének kinyerése a **replacementPropertyName** **replacementValue** történik. |
| **replacementValue** |Optional |Karakterlánc |Új értéket cserélje le a régit. |
| **replacementAttributeName** |Optional |Sztring |A helyettesítő értékhez használandó attribútum neve |
| **sablon** |Optional |Sztring |Ha meg van adni a **sablon** értéke, megkeresjük a **OldValue** a sablonon belül, és lecseréljük a **forrás** értékre. |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Függvény:**<br> SelectUniqueValue (uniqueValueRule1, uniqueValueRule2, uniqueValueRule3,...)

**Leírás:**<br> Legalább két argumentumot, amelyek egyedi érték létrehozási szabályok definiált kifejezések használatával van szükség. A függvény minden egyes szabály kiértékeli, és ellenőrzi, az érték egyedi-e a cél alkalmazás/könyvtárban jönnek létre. Az első egyedi érték található egy adja vissza. Összes érték már létezik a célkiszolgálón, ha a bejegyzés lesz első szétválasztást és okát az auditnaplókban rendszer naplózza. Nincs megadható argumentumok számának felső korlátja.

> [!NOTE]
>1. Ez egy legfelső szintű függvényt, nem ágyazhatók egymásba.
>2. Ez a függvény csak hivatott bejegyzés létrehozások használható. Használhatja azt egy attribútumot, ha a **leképezése a alkalmazni** tulajdonságot **csak objektum létrehozásakor**.


**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **uniqueValueRule1... uniqueValueRuleN** |Legalább a 2 szükséges, nem felső határérték |Sztring | A kiértékelni kívánt egyedi érték-létrehozási szabályok listája. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Függvény:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Leírás:**<br> Egyetlen appRoleAssignment ad vissza egy adott alkalmazás felhasználóhoz rendelt összes appRoleAssignments listájáról. Ez a függvény szükséges ahhoz, hogy a appRoleAssignments objektumot egyetlen szerepkör-nevet megadó karakterláncba alakítsa át. Vegye figyelembe, hogy az ajánlott eljárás annak biztosítása, hogy egyszerre csak egy appRoleAssignment legyen hozzárendelve egy felhasználóhoz, és ha több szerepkör van hozzárendelve, a visszaadott szerepkör-karakterlánc nem lehet előre jelezhető. 

**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **[hozzárendeléseinek]** |Szükséges |Karakterlánc |**[hozzárendeléseinek]**  objektum. |

---
### <a name="split"></a>Megosztott
**Függvény:**<br> Felosztás (forrás, elválasztó karakter)

**Leírás:**<br> A karakterláncot egy Mulit értékű tömbre osztja fel a megadott elválasztó karakter használatával.

**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Karakterlánc |**forrás** érték frissítéséhez. |
| **delimiter** |Kötelező |Sztring |Meghatározza a karakterlánc felosztására szolgáló karaktert (példa: ",") |

---
### <a name="stripspaces"></a>StripSpaces
**Függvény:**<br> StripSpaces(source)

**Leírás:**<br> Eltávolítja az összes szóközt ("") karaktert a forrás karakterláncot.

**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Karakterlánc |**forrás** érték frissítéséhez. |

---
### <a name="switch"></a>Kapcsoló
**Függvény:**<br> Switch (forrás, defaultValue, 1. kulcs, érték1, 2. kulcs, value2,...)

**Leírás:**<br> Amikor **forrás** értéke megegyezik egy **kulcs**, adja vissza **érték** , amely a **kulcs**. Ha **forrás** értéke nem egyezik a bármely kulcsok beolvasása **defaultValue**.  **Kulcs** és **érték** paraméterek mindig párosával kell származnia. A függvény mindig paraméterek páros számú vár.

**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Karakterlánc |**forrás** érték frissítéséhez. |
| **defaultValue** |Optional |Karakterlánc |Alapértelmezett érték használható, ha a forrás nem felel meg minden olyan kulcsokat. Üres karakterlánc lehet (""). |
| **key** |Szükséges |Karakterlánc |**Kulcs** összehasonlítására **forrás** értéket. |
| **value** |Szükséges |Karakterlánc |Az érték a **forrás** összekapcsolja a kulcsot. |

---
### <a name="tolower"></a>ToLower
**Függvény:**<br> ToLower (forrás, kulturális környezet)

**Leírás:**<br> Egy *forrás* sztring értéket vesz igénybe, és a megadott kulturális szabályok alapján átalakítja a kisbetűsre. Ha nincs megadva *kulturális* információ, akkor a rendszer a semleges kultúrát fogja használni.

**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Karakterlánc |Általában az attribútum az az adatforrás-objektum neve |
| **kulturális környezet** |Választható |Sztring |Az RFC 4646 alapján a kulturális név formátuma *languagecode2-ország/regioncode2*, ahol a *languagecode2* a kétbetűs nyelvi kód, az *ország/regioncode2* pedig a kétbetűs alkulturális kód. Ilyenek például a japán (Japán) és az en-US angol (Egyesült Államok). Azokban az esetekben, amikor a kétbetűs nyelvi kód nem érhető el, az ISO 639-2-ből származtatott hárombetűs kód van használatban.|

---
### <a name="toupper"></a>ToUpper
**Függvény:**<br> ToUpper (forrás, kulturális környezet)

**Leírás:**<br> Egy *forrás* sztring értékét veszi át, és a megadott kulturális szabályok alapján átalakítja a nagybetűre. Ha nincs megadva *kulturális* információ, akkor a rendszer a semleges kultúrát fogja használni.

**Paraméterek:**<br> 

| Name (Név) | Szükséges / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Karakterlánc |Általában az attribútum neve, az adatforrás-objektum. |
| **kulturális környezet** |Választható |Sztring |Az RFC 4646 alapján a kulturális név formátuma *languagecode2-ország/regioncode2*, ahol a *languagecode2* a kétbetűs nyelvi kód, az *ország/regioncode2* pedig a kétbetűs alkulturális kód. Ilyenek például a japán (Japán) és az en-US angol (Egyesült Államok). Azokban az esetekben, amikor a kétbetűs nyelvi kód nem érhető el, az ISO 639-2-ből származtatott hárombetűs kód van használatban.|

## <a name="examples"></a>Példák
### <a name="strip-known-domain-name"></a>Sáv ismert tartománynév
Szerezzen be egy felhasználónevet a felhasználó e-mailben egy ismert tartománynévnek sáv kell. <br>
Például ha a tartomány a "contoso.com", majd használhatja a következő kifejezést:

**Kifejezés:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Bemeneti / kimeneti mintát:** <br>

* **BEMENETI** (levelezés): "john.doe@contoso.com"
* **KIMENETI**: "john.doe"

### <a name="append-constant-suffix-to-user-name"></a>Állandó utótagok hozzáfűzése a felhasználónevet
Ha egy Salesforce védőfal használja, szüksége lehet egy további utótagok hozzáfűzése a felhasználónevek őket szinkronizálása előtt.

**Kifejezés:** <br>
`Append([userPrincipalName], ".test")`

**Bemeneti/kimeneti. példa:** <br>

* **BEMENETI**: (userPrincipalName): "John.Doe@contoso.com"
* **KIMENETI**: "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Utónév és Vezetéknév összetűzésének részei felhasználói alias létrehozása
Létre kell hoznia egy felhasználói alias, a felhasználó utónevét első 3 betűket és a felhasználó vezetéknevét első 5 karakterét végrehajtásával.

**Kifejezés:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Bemeneti/kimeneti. példa:** <br>

* **Bemenet** (givenName): John
* **Bemenet** (vezetéknév): DOE
* **KIMENET**:  "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Távolítsa el az e mellékjeleket egy karakterláncból.
Cserélje le a megfelelő karakterek, ékezetes nem tartalmazó ékezetes karakter hosszúságúnak kell.

**Kifejezés:** <br>
NormalizeDiacritics([givenName])

**Bemeneti/kimeneti. példa:** <br>

* **Bemenet** (givenName): Zoë
* **KIMENET**:  Zoe

### <a name="split-a-string-into-a-multi-valued-array"></a>Sztring felosztása többértékű tömbbe
A karakterláncok vesszővel tagolt listáját kell megadnia, és azokat egy olyan tömbbe kell bontani, amely egy többértékű attribútumhoz, például a Salesforce PermissionSets attribútumához csatlakoztatható. Ebben a példában az extensionAttribute5 az Azure AD-ben az engedélyezési készletek listája lett feltöltve.

**Kifejezés:** <br>
Split ([extensionAttribute5], ",")

**Bemeneti/kimeneti. példa:** <br>

* **Bemenet** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **Kimenet**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Egy bizonyos formátumú karakterlánc formájában kimeneti dátum
Egy SaaS-alkalmazás egy bizonyos formátumú dátumok küldeni szeretné. <br>
Ha például szeretné formátumához servicenow-hoz készült.

**Kifejezés:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Bemeneti/kimeneti. példa:**

* **Bemenet** (extensionAttribute1): "20150123105347.1Z"
* **KIMENET**:  "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Cserélje le az előre megadott beállítások alapján értéket

Adja meg a felhasználó az Azure AD-ben tárolt kódja alapján az időzóna kell. <br>
Ha az állapot-kód nem egyezik az előre definiált beállításokat, használja a "Ausztrália/Sydney" alapértelmezett értékét.

**Kifejezés:** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Bemeneti/kimeneti. példa:**

* **Bemenet** (állapot): QLD
* **KIMENET**: "Ausztrália/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Karakterek cseréje reguláris kifejezés használatával
Meg kell keresnie a reguláris kifejezés értékének megfelelő karaktereket, és el kell távolítani őket.

**Kifejezés:** <br>

Replace ([mailNickname],, "[a-zA-Z_] *",, "",,)

**Bemeneti/kimeneti. példa:**

* **Bemenet** (mailNickname: "john_doe72"
* **KIMENET**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Generált userPrincipalName (UPN) értékének kisbetűvé alakítása
Az alábbi példában az UPN-érték a PreferredFirstName és a PreferredLastName forrás mezőinek összefűzésével jön létre, és a ToLower függvény a generált karakterláncon működik, hogy az összes karaktert kisbetűvé alakítsa. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Bemeneti/kimeneti. példa:**

* **Bemenet** (PreferredFirstName): John
* **Bemenet** (PreferredLastName): Smith
* **KIMENET**: "john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Egyedi érték a userPrincipalName (UPN) attribútum létrehozása
Alapján a felhasználó utónevét, a középső név és vezetéknevét, létre kell hoznia az UPN attribútum értékét, és ellenőrizze az egyedi-e a cél az AD címtárban az UPN attribútum értéke hozzárendelése előtt.

**Kifejezés:** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Bemeneti/kimeneti. példa:**

* **Bemenet** (PreferredFirstName): John
* **Bemenet** (PreferredLastName): Smith
* **KIMENETI**: "John.Smith@contoso.com" Ha UPN értékét John.Smith@contoso.com még nem létezik a címtárban
* **KIMENETI**: "J.Smith@contoso.com" Ha UPN értékét John.Smith@contoso.com a könyvtár már létezik
* **KIMENETI**: "Jo.Smith@contoso.com" Ha a fenti két UPN-érték már létezik a címtárban

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Felhasználói kiépítés és megszüntetés SaaS-alkalmazások automatizálása](user-provisioning.md)
* [A felhasználók átadásának attribútumleképezések testreszabása](customize-application-attributes.md)
* [A felhasználók átadásának Hatókörszűrő](define-conditional-rules-for-provisioning-user-accounts.md)
* [SCIM használata a felhasználók és csoportok automatikus üzembe helyezésének engedélyezéséhez az Azure Active Directoryból az alkalmazásokba](use-scim-to-provision-users-and-groups.md)
* [Alkalmazáskiépítési értesítések](user-provisioning.md)
* [Az SaaS-alkalmazások integrálásával foglalkozó oktatóanyagok listája](../saas-apps/tutorial-list.md)
