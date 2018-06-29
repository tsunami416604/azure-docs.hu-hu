---
title: Attribútum-leképezésekhez kifejezések írása az Azure Active Directoryban |} Microsoft Docs
description: 'Útmutató: a kifejezés leképezéseit segítségével attribútumértékek átalakítása elfogadható formátumot SaaS app objektumok Azure Active Directoryban automatikus kiépítése során.'
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: b13c51cd-1bea-4e5e-9791-5d951a518943
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.openlocfilehash: 24b20766997a9a41956f575f6cab8ee5ef0d9e25
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036104"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Attribútum-leképezésekhez kifejezések írása az Azure Active Directoryban
Egy SaaS-alkalmazáshoz történő konfigurálásakor megadhatja attribútum-leképezésekhez típusú egyik egy kifejezés-hozzárendelést. Ezeknél a parancsfájl-szerű kifejezés, amely lehetővé teszi a felhasználók adatok átalakítása több biztosítható a SaaS-alkalmazás formátumokba kell írnia.

## <a name="syntax-overview"></a>Szintaxis áttekintése
Attribútum-leképezésekhez kifejezések szintaxisa a Visual Basic Applications (VBA) funkciók reminiscent.

* A teljes kifejezés függvények, amelyek a nevét, majd argumentumait zárójelek áll kell meghatározni: <br>
  *Függvénynév (<< argumentumának 1 >>, <<argument N>>)*
* Előfordulhat, hogy ágyazhatók be funkciók belül egymással. Példa: <br> *FunctionOne(FunctionTwo(<<argument1>>))*
* Három különböző típusú argumentumok függvényekké átadhatók:
  
  1. Attribútumok, amelyek négyzetes szögletes zárójelbe kell foglalni. Például: [attributeName]
  2. A karakterlánckonstansokat, amelyek dupla idézőjelek közé kell. Például: "Az Amerikai Egyesült Államok"
  3. Egyéb funkciót. Például: FunctionOne (<<argument1>>, FunctionTwo (<<argument2>>))
* A karakterlánckonstansokat Ha egy fordított perjel (\) vagy az idézőjel (") a karakterláncban kell azt kell megjelölni a fordított perjel (\) szimbólum. Például: "vállalatnév: \"Contoso\""

## <a name="list-of-functions"></a>Függvények listája
[Hozzáfűzendő](#append) &nbsp; &nbsp; &nbsp; &nbsp; [FormatDateTime](#formatdatetime) &nbsp; &nbsp; &nbsp; &nbsp; [Csatlakozás](#join) &nbsp; &nbsp; &nbsp; &nbsp; [Mid](#mid) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [NormalizeDiacritics](#normalizediacritics) [nem](#not) &nbsp; &nbsp; &nbsp; &nbsp; [cserélje le](#replace) &nbsp; &nbsp; &nbsp; &nbsp; [SingleAppRoleAssignment](#singleapproleassignment) &nbsp; &nbsp; &nbsp; &nbsp; [StripSpaces](#stripspaces) &nbsp; &nbsp; &nbsp; &nbsp; [Kapcsoló](#switch)

- - -
### <a name="append"></a>Hozzáfűzés
**Függvény:**<br> Append(Source, suffix)

**Leírás:**<br> Értéket vesz fel forrás karakterlánc, és az utótag hozzáfűzi a végén.

**Paraméterek:**<br> 

| Name (Név) | Kötelező / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Sztring |Általában az adatforrás-objektum az az attribútum neve |
| **suffix** |Szükséges |Sztring |A karakterlánc, amely szeretne hozzáfűzni, az érték végén. |

- - -
### <a name="formatdatetime"></a>FormatDateTime
**Függvény:**<br> FormatDateTime (forrás, inputFormat, outputFormat)

**Leírás:**<br> Egy adott formátumból egy dátumkarakterlánc vesz igénybe, és egy másik formátumba konvertálja.

**Paraméterek:**<br> 

| Name (Név) | Kötelező / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Sztring |Általában az attribútum neve a forrás-objektumból. |
| **inputFormat** |Szükséges |Sztring |Az érték formátumúak. A támogatott formátumok, lásd: [ http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx ](http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Szükséges |Sztring |A kimeneti dátum formátumát. |

- - -
### <a name="join"></a>Csatlakozás
**Függvény:**<br> Csatlakozás (elválasztó, source1, source2,...)

**Leírás:**<br> JOIN() hasonlít Append(), azzal a különbséggel, hogy kombinálhatja a több **forrás** karakterlánc az egyetlen karakterlánc értéket, majd minden egyes érték fogja elválasztani a **elválasztó** karakterlánc.

Ha a forrás-értékeket egy többértékű attribútum, akkor minden értékét attribútum, amely tartományhoz fog csatlakozni egymáshoz, akkor az elválasztó érték.

**Paraméterek:**<br> 

| Name (Név) | Kötelező / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **separator** |Szükséges |Sztring |A karakterlánc elválasztó forrás értékek, amikor azok a halmaz zónanevének egyetlen karakterlánccá egyesít. Lehet "" Ha nem elválasztó szükség. |
| ** source1... sourceN ** |Szükség esetén a változó-szám, ahányszor |Sztring |A karakterlánc-értékek együtt tartományhoz. |

- - -
### <a name="mid"></a>Mid
**Függvény:**<br> Mid (forrás, kezdet, hossz)

**Leírás:**<br> Az érték egy részét adja vissza. Egy karakterláncrészletet: csak egy részét a forráskarakterlánc származó karaktereket tartalmazó karakterlánc.

**Paraméterek:**<br> 

| Name (Név) | Kötelező / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Sztring |Általában az attribútum neve. |
| **start** |Szükséges |egész szám |Az index a **forrás** karakterlánc, ahol a substring kell kezdődnie. A karakterlánc első karaktere az 1 indexe lesz, a második karakter 2 mutatója, és így tovább. |
| **hossza** |Szükséges |egész szám |A substring hosszát. Hossza kívül lejártát a **forrás** karakterlánc, a függvény karakterláncrészletet ad vissza **start** végét indextől **forrás** karakterlánc. |

- - -
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Függvény:**<br> NormalizeDiacritics(source)

**Leírás:**<br> Egy karakterlánc-argumentumot igényel. A karakterláncot ad vissza, de bármely diakritikus karakterekkel egyenértékű nem diakritikus karakterek helyett. A különböző felhasználói azonosítók például az egyszerű felhasználónevek, a SAM-fiók nevét és az e-mail címek használt értékek jellemzően konvertálni az első és utolsó nevét tartalmazó diakritikus karakterek (ékezetes).

**Paraméterek:**<br> 

| Name (Név) | Kötelező / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Sztring | Általában a Keresztnév vagy utolsó name attribútuma |

- - -
### <a name="not"></a>Nem
**Függvény:**<br> Not(Source)

**Leírás:**<br> Logikai értéke tükrözi a **forrás**. Ha **forrás** értéke "*igaz*", adja vissza "*hamis*". Ellenkező esetben adja vissza "*igaz*".

**Paraméterek:**<br> 

| Name (Név) | Kötelező / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Logikai típusú karakterlánc |Várt **forrás** értékek: "True" vagy "False". |

- - -
### <a name="replace"></a>Csere
**Függvény:**<br> Cserélje le a (forrás, oldValue, regexPattern, regexGroupName, helyettesítő értéke, replacementAttributeName, sablon)

**Leírás:**<br>
Lecseréli az értékeket karakterláncként. Attól függően, hogy a megadott paraméterek másképp működik:

* Ha **oldValue** és **helyettesítő értéke** itt találhatók:
  
  * A forrás oldValue összes előfordulását lecseréli helyettesítő értéke
* Ha **oldValue** és **sablon** itt találhatók:
  
  * Összes előfordulását lecseréli a **oldValue** a a **sablon** rendelkező a **forrás** érték
* Ha **regexPattern**, **regexGroupName**, **helyettesítő értéke** itt találhatók:
  
  * Lecseréli az összes értékeket oldValueRegexPattern helyettesítő értéke a forrás karakterláncot az egyező
* Ha **regexPattern**, **regexGroupName**, **replacementPropertyName** itt találhatók:
  
  * Ha **forrás** nincs értéke **forrás** adja vissza
  * Ha **forrás** értéke, használja a **regexPattern** és **regexGroupName** csereértékre kibontani a tulajdonság **replacementPropertyName** . Helyettesítő értéke az eredményt adja vissza a rendszer

**Paraméterek:**<br> 
| Name (Név) | Kötelező / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Sztring |Általában az attribútum neve a forrás-objektumból. |
| **oldValue** |Optional |Sztring |A cserélni kívánt érték **forrás** vagy **sablon**. |
| **regexPattern** |Optional |Sztring |Le kell cserélni az érték reguláris kifejezéssel mintát **forrás**. Vagy a replacementPropertyName használatakor helyettesítő tulajdonság értékének kinyerése mintát. |
| **regexGroupName** |Optional |Sztring |A csoport nevét **regexPattern**. Csak akkor, ha replacementPropertyName használata esetén azt ki a csoport értékét, helyettesítő helyettesítő tulajdonság értéke. |
| **replacementValue** |Optional |Sztring |Új értéket lecseréli a régi kiszolgálóéval. |
| **replacementAttributeName** |Optional |Sztring |Az attribútum értékét, kell használni, ha a forrás értéke nincs neve. |
| **Sablon** |Optional |Sztring |Ha **sablon** érték van megadva, fog keresni **oldValue** belül a sablont, és cserélje le az adatforrás-értéke. |

- - -
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Függvény:**<br> SingleAppRoleAssignment([appRoleAssignments])

**Leírás:**<br> Egy karakterlánc-argumentumot igényel. A karakterláncot ad vissza, de bármely diakritikus karakterek repalced egyenértékű nem diakritikus karakternél.

**Paraméterek:**<br> 

| Name (Név) | Kötelező / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |Szükséges |Sztring |**[appRoleAssignments]**  objektum. |

- - -
### <a name="stripspaces"></a>StripSpaces
**Függvény:**<br> StripSpaces(source)

**Leírás:**<br> Eltávolítja az összes szóközt ("") karaktert a forrás karakterláncot.

**Paraméterek:**<br> 

| Name (Név) | Kötelező / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Sztring |**forrás** érték frissítéséhez. |

- - -
### <a name="switch"></a>Kapcsoló
**Függvény:**<br> A kapcsoló (forrás, defaultValue, key1, érték1, key2, érték2,...)

**Leírás:**<br> Ha **forrás** értéke megegyezik egy **kulcs**, adja vissza **érték** az adott **kulcs**. Ha **forrás** érték nem felel meg a kulcsokat, értéket ad vissza **defaultValue**.  **Kulcs** és **érték** paraméterek mindig párban kell származniuk. A függvény mindig paraméterek páros számú vár.

**Paraméterek:**<br> 

| Name (Név) | Kötelező / ismétlődő | Típus | Megjegyzések |
| --- | --- | --- | --- |
| **source** |Szükséges |Sztring |**Forrás** érték frissítéséhez. |
| **defaultValue** |Optional |Sztring |Alapértelmezett érték használható, ha a forrás nem felel meg a kulcsokat. Üres karakterlánc lehet (""). |
| **key** |Szükséges |Sztring |**Kulcs** összehasonlítandó **forrás** az értéket. |
| **value** |Szükséges |Sztring |Csere értéke a **forrás** megadott kulccsal. |

## <a name="examples"></a>Példák
### <a name="strip-known-domain-name"></a>Sáv ismert tartománynév
Távolítsa el az beszerzése egy felhasználónevet a felhasználó e-mailben egy ismert tartománynevet kell. <br>
Például ha a tartomány pedig a "contoso.com", majd használhatja a következő kifejezést:

**Kifejezés:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**A minta bemeneti / kimeneti:** <br>

* **BEMENETI** (mail): "john.doe@contoso.com"
* **KIMENETI**: "john.doe"

### <a name="append-constant-suffix-to-user-name"></a>Állandó utótagok hozzáfűzése felhasználónév
Ha egy Salesforce védőfal használ, szükség lehet egy további utótagok hozzáfűzése a felhasználónevek őket szinkronizálása előtt.

**Kifejezés:** <br>
`Append([userPrincipalName], ".test"))`

**Minta bemeneti/kimeneti:** <br>

* **BEMENETI**: (userPrincipalName): "John.Doe@contoso.com"
* **KIMENETI**: "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Utónév és Vezetéknév hozzáfűzésével részei felhasználói alias létrehozása
A felhasználói alias létrehozására alapul véve a felhasználó utónevét az első 3 betűk és a felhasználó vezetéknevét első 5 betűket kell.

**Kifejezés:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Minta bemeneti/kimeneti:** <br>

* **BEMENETI** (givenName): "John"
* **BEMENETI** (Vezetéknév): "Doe"
* **KIMENETI**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Egy karakterlánc ékezetek eltávolítása
Ki kell cserélni a karakterek, ékezetes egyenértékű karakterek, ékezetes nem tartalmazó tartalmazó.

**Kifejezés:** <br>
NormalizeDiacritics([givenName])

**Minta bemeneti/kimeneti:** <br>

* **BEMENETI** (givenName): "Zoë"
* **KIMENETI**: "Zoe"

### <a name="output-date-as-a-string-in-a-certain-format"></a>Bizonyos formátumban karakterláncként kimeneti dátum
Szeretne küldeni a dátumok adott formátumot SaaS-alkalmazás. <br>
Például szeretné ServiceNow a dátumok formázásához.

**Kifejezés:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Minta bemeneti/kimeneti:**

* **BEMENETI** (extensionAttribute1): "20150123105347.1Z"
* **KIMENETI**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Cserélje le egy előre megadott beállítások alapján értékét
Adja meg a felhasználót az Azure AD-ben tárolt kódja alapján időzónájának kell. <br>
Ha az állapot kód nem egyezik az előre definiált beállításokat, használja a "Ausztrália/Sydney" alapértelmezett értékét.

**Kifejezés:** <br>

`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Minta bemeneti/kimeneti:**

* **BEMENETI** (állapot): "QLD"
* **KIMENETI**: "Ausztrália/Brisbane"

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)
* [Felhasználói létesítési vagy megszüntetési SaaS-alkalmazásokhoz való automatizálásához](active-directory-saas-app-provisioning.md)
* [A felhasználók átadása attribútum-leképezésekhez testreszabása](active-directory-saas-customizing-attribute-mappings.md)
* [Helyezése Hatókörszűrőkkel felhasználói történő üzembe helyezéséhez](active-directory-saas-scoping-filters.md)
* [SCIM használata a felhasználók és csoportok automatikus üzembe helyezésének engedélyezéséhez az Azure Active Directoryból az alkalmazásokba](manage-apps/use-scim-to-provision-users-and-groups.md)
* [Alkalmazás-kiépítési értesítések](active-directory-saas-account-provisioning-notifications.md)
* [SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](saas-apps/tutorial-list.md)

