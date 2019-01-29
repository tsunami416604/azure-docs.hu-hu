---
title: Nyelvi testreszabás az Azure Active Directory B2C |} A Microsoft Docs
description: További információ a nyelvi testreszabása.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 040f9b00b7f282789d8a63b9bbeb8ee7edfc779e
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55164147"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Az Azure Active Directory B2C a nyelvi testreszabás

Nyelvi testreszabás az Azure Active Directory B2C (Azure AD B2C-vel) lehetővé teszi, hogy a felhasználói folyamat különböző nyelvekhez az ügyfelek igényeinek megfelelően.  A Microsoft biztosít a fordításainak [36 nyelvet](#supported-languages), de is megadhatja a saját fordításokat bármely nyelven. Akkor is, ha a felhasználói élmény csak egyetlen nyelven áll rendelkezésre, testre szabható a lapokon szöveg.  

## <a name="how-language-customization-works"></a>Hogyan működik a nyelvi testreszabás
Milyen nyelveken érhető el a felhasználói folyamat kiválasztása a nyelvi testreszabás használatával. Miután a szolgáltatás engedélyezve van, megadhatja a lekérdezési karakterlánc paramétereként `ui_locales`, az alkalmazásból. Amikor kapcsolatba lép az Azure AD B2C-be, a lap a területi beállítás, amely jelezte, hogy le. Ezt a konfigurációtípust biztosít teljes körű, a nyelveket a felhasználói folyamat, és figyelmen kívül hagyja az ügyfél böngésző a nyelvi beállításokat. 

Milyen nyelveket, az ügyfél látja, hogy szintű nincs szüksége lehet. Ha nem ad meg egy `ui_locales` paramétert, a felhasználói élmény szabja meg a böngésző beállításait.  Továbbra is szabályozhatja, hogy a felhasználói folyamat fordítja le egy támogatott nyelvre úgy nyelveket. Ha a felhasználó böngészőben megjelenítése nyelv, amelyet szeretne támogatására van beállítva, akkor ehelyett alapértelmezés szerint a támogatott kulturális környezetek a kiválasztott nyelv jelenik meg.

- **felhasználói felület – területi beállításokhoz megadott nyelvi**: Miután engedélyezte a nyelvi testreszabás, a felhasználói folyamatot az itt megadott nyelv fordítja le.
- **Böngésző által kért nyelv**: Ha nincs `ui_locales` paraméter van megadva, a felhasználói folyamat fordítja le a böngésző által kért nyelv *, ha a nyelv támogatott*.
- **A házirend alapértelmezett nyelve**: Ha a böngésző nem adja meg a nyelvet, vagy adja meg, amely nem támogatott, a felhasználói folyamatot a felhasználó flow alapértelmezett nyelvét fordítja le.

>[!NOTE]
>Ha felhasználói egyéni attribútumok használata esetén meg kell adnia saját fordításait. További információkért lásd: [testre szabhatja a karakterláncok](#customize-your-strings).
>

## <a name="support-requested-languages-for-uilocales"></a>Az ui_locales paraméter a kért nyelv támogatása 
Ez a funkció először engedélyeznie kell a nyelvi testreszabás általános elérhetősége előtt létrehozott házirendek. Szabályzatok és a felhasználói folyamatok után létrehozott rendelkezik a nyelvi testreszabás alapértelmezés szerint engedélyezve van. 

Ha engedélyezi a felhasználói folyamat a nyelvi testreszabás, a nyelv, a felhasználói folyamat hozzáadásával szabályozhatja a `ui_locales` paraméter.
1. Válassza ki az Azure AD B2C-bérlőben **felhasználókövetési adatai**.
2. Kattintson a felhasználói folyamatot, amely a fordítások engedélyezni kívánja.
3. Válassza ki **nyelvek**.  
4. Válassza ki **nyelvi testreszabás engedélyezése**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Válassza ki a felhasználói folyamat nyelveket engedélyezve vannak 
Engedélyezi a nyelvek a felhasználói folyamat fordítandó nélkül a böngésző által kért készletét a `ui_locales` paraméter.
1. Győződjön meg arról, hogy a felhasználói folyamat rendelkezik-e a nyelvi testreszabás engedélyezve van az előző utasítások.
2. Az a **nyelvek** a felhasználói folyamat lapon, válassza ki a támogatni kívánt nyelv.
3. A Tulajdonságok panelen módosítsa **engedélyezve** való **Igen**.  
4. Válassza ki **mentése** tulajdonságpanel tetején.

>[!NOTE]
>Ha egy `ui_locales` paraméter nincs megadva, az oldalra fordítja le az ügyfél a böngésző nyelve csak akkor, ha engedélyezve van.
>

## <a name="customize-your-strings"></a>A karakterláncok testreszabása
Nyelvi testreszabás lehetővé teszi a felhasználói folyamat bármilyen karakterlánc testreszabása.
1. Győződjön meg arról, hogy rendelkezik-e a felhasználói folyamat engedélyezve van az előző utasítások a nyelvi testreszabás.
2. Az a **nyelvek** a felhasználói folyamat oldalára, válassza ki a nyelvet, amelyet testre szeretne.
3. A **lap szint erőforrásfájlok**, jelölje ki a szerkeszteni kívánt lapot.
4. Válassza ki **Alapértelmezések letöltése** (vagy **felülbírálások letöltése** Ha korábban szerkesztése ezen a nyelven).

Ezeket a lépéseket, amelyek segítségével szerkessze a karakterláncok egy JSON-fájlt ad.

### <a name="change-any-string-on-the-page"></a>A lapon bármilyen karakterlánc módosítása
1. Nyissa meg a JSON-fájl letölthető a JSON-szerkesztővel előző utasításait.
2. Keresse meg az elem, amelyet módosítani szeretne.  Annak `StringId` a karakterláncot keres, vagy keresse meg a `Value` attribútum, amelyet módosítani szeretne.
3. Frissítés a `Value` attribútummal, amelyeket meg szeretne.
4. Minden karakterlánc, amelyet módosítani szeretne, módosítsa `Override` való `true`.
5. Mentse a fájlt, és töltse fel a módosításokat. (Megtalálhatja a feltöltési vezérlő ugyanazon a helyen, ahonnan letöltötte a JSON-fájl.) 

>[!IMPORTANT]
>Ha bírálja felül egy karakterlánc van szüksége, ellenőrizze, hogy állítsa be a `Override` értéket a következőre `true`.  Ha az érték nem módosul, a rendszer figyelmen kívül hagyja a bejegyzést. 
>

### <a name="change-extension-attributes"></a>A bővítményattribútumok módosítása
Ha meg szeretné változtatni a karakterlánc egy egyéni felhasználói attribútum, vagy szeretne hozzáadni egy JSON-, a következő formátumban van:
```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Cserélje le `<ExtensionAttribute>` az egyéni felhasználói attribútum nevével.  

Cserélje le `<ExtensionAttributeValue>` megjelenő új karakterlánccal.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Adja meg az értékek listáját a LocalizedCollections
Set értékek listáját adja meg a válaszok szeretne, ha szeretne létrehozni egy `LocalizedCollections` attribútum.  `LocalizedCollections` tömbje `Name` és `Value` párokat. Az elemek sorrendjét lesz a sorrendben jelennek meg.  Hozzáadandó `LocalizedCollections`, használja a következő formátumot:

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType", 
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` a felhasználói attribútumot, hogy ez a `LocalizedCollections` attribútum esetén adott válasz.
* `Name` az érték a felhasználó számára.
* `Value` a mi adja vissza a jogcímet, ha ezt a beállítást.

### <a name="upload-your-changes"></a>Töltse fel a módosítások
1. Miután elvégezte a módosításokat a JSON-fájl, lépjen vissza a B2C-bérlőben.
2. Válassza ki **felhasználókövetési adatai** , és kattintson a felhasználói folyamatot, amely a fordítások engedélyezni kívánja.
3. Válassza ki **nyelvek**.
4. Válassza ki a kívánt fordítás.
5. Jelölje ki a lapot, ahol a fordítások szeretné.
6. Válassza ki a mappa ikont, és válassza ki a JSON-fájlt a feltöltéshez.
 
Menti a felhasználói folyamathoz automatikusan.

## <a name="customize-the-page-ui-by-using-language-customization"></a>A lap felhasználói felület testreszabása a nyelvi testreszabás

A HTML-tartalom honosítása két módon lehet. Egyik módja az, hogy kapcsolja be a [nyelvi testreszabás](active-directory-b2c-reference-language-customization.md). Ez a funkció lehetővé teszi, hogy az Azure AD B2C-vel továbbítja az Open ID Connect paraméter `ui-locales`, a végponthoz.  A webtartalom-kiszolgáló a paraméter használatával adja meg a testre szabott HTML-lapok nyelvspecifikus.

Azt is megteheti a használt területi beállítása alapján különböző helyekről származó tartalom kérheti le. A CORS-kompatibilis végponthoz az állíthat be egy mappaszerkezet gazdagép tartalom bizonyos nyelveken. Felhívjuk a megfelelőt, ha a helyettesítő karaktert tartalmazó értéket használja `{Culture:RFC5646}`.  Például tegyük fel, hogy ez az egyéni oldal URI-ja:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
Az oldal betöltése `fr`. Az oldal HTML és CSS tartalmat kér le, amikor azt a van lehetőség:
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Egyéni nyelv hozzáadása

A Microsoft jelenleg nem biztosít a fordítások nyelveket is hozzáadhat. Adja meg a felhasználói folyamat minden karakterlánc a fordításokat kell.  Nyelvi és területi beállítás kódok korlátozva, az ISO 639-1 szabvány lévőket. 

1. Válassza ki az Azure AD B2C-bérlőben **felhasználókövetési adatai**.
2. Kattintson a felhasználói folyamatot, ahol egyéni nyelv hozzáadása, és kattintson a kívánt **nyelvek**.
3. Válassza ki **egyéni nyelv hozzáadása** az oldal tetején.
4. A megnyíló helyi ablaktáblán azonosítsa a nyelvét, adja-e a fordítások adjon meg egy érvényes területi kódot.
5. Minden olyan lap töltse le a felülbírálások egy készletét az angol nyelvű tájékoztatáshoz, és a fordítások dolgozhat.
6. Miután végzett, az a JSON-fájlok, feltöltheti őket minden egyes laphoz.
7. Válassza ki **engedélyezése**, és a felhasználói folyamat most már jeleníti meg ezt a nyelvet, a felhasználók számára.
8. Mentse a nyelvet.

>[!IMPORTANT]
>Felülbírálások feltöltése a hozzá tartozó mentése előtt vagy a egyéni nyelveket kell.
>

## <a name="additional-information"></a>További információ

### <a name="page-ui-customization-labels-as-overrides"></a>Oldal-UI testreszabása címkékre, felülbírálások
Ha engedélyezi a nyelvi testreszabás, a címkék használatával az oldal-UI testreszabása a korábbi módosítások megmaradnak az egy JSON-fájlt az angol (en). Továbbra is módosíthatja a címkék és egyéb karakterláncokat a nyelvi testreszabás nyelvi erőforrás feltöltése.
### <a name="up-to-date-translations"></a>Naprakész fordítások
A Microsoft elkötelezett a szolgáltató a legfrissebb fordításokat használható. A Microsoft folyamatosan javítja a Fordítások és tartja őket a felel meg. A Microsoft lesz azonosítani a hibák és változások a globális terminológia és zökkenőmentesen győződjön meg a frissítéseket, amelyek működnek a felhasználói folyamat.
### <a name="support-for-right-to-left-languages"></a>Jobbról balra író nyelvek támogatása
A Microsoft jelenleg nem biztosít támogatást jobbról balra író nyelveket. Ez elvégezhető egyéni nyelv használatával, és CSS használatával megváltoztatni a karakterláncok jelennek meg.  Ha ez a funkció van szüksége, kérjük szavazzon rá a [Azure visszajelzési](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Közösségi szolgáltató fordítások
A Microsoft biztosít a `ui_locales` közösségi bejelentkezések OIDC paramétert. De néhány közösségi identitásszolgáltató, többek között a Facebookhoz és a Google, ne fogadja el őket. 
### <a name="browser-behavior"></a>Böngésző viselkedés
Chrome és a Firefox beállított nyelvet is kérhetnek. Ha egy támogatott nyelvre, mielőtt az alapértelmezett jelenik meg. A Microsoft Edge jelenleg nem kér egy nyelvet, és rögtön az alapértelmezett nyelv kerül.

### <a name="supported-languages"></a>Támogatott nyelvek

| Nyelv              | Nyelvkód |
|-----------------------|---------------|
| Bengáli                | BN            |
| cseh                 | cs            |
| dán                | da            |
| német                | de            |
| görög                 | el            |
| Angol               | hu            |
| spanyol               | es            |
| finn               | fi            |
| francia                | fr            |
| gudzsaráti              | Gu            |
| hindi                 | hi            |
| horvát              | óra            |
| magyar             | hu            |
| olasz               | it            |
| japán              | ja            |
| kannada               | kn            |
| koreai                | ko            |
| malajálam             | ml            |
| maráthi               | MR            |
| maláj                 | ms            |
| Norvég (nynorsk)      | nb            |
| holland                 | nl            |
| pandzsábi               | Pa            |
| lengyel                | pl            |
| Portugál – Brazília   | pt-br         |
| Portugál – Portugália | pt-pt         |
| román              | ro            |
| orosz               | ru            |
| szlovák                | sk            |
| svéd               | sv            |
| tamil                 | TA            |
| telugu                | Te            |
| thai                  | .            |
| török               | TR            |
| Egyszerűsített kínai –  | zh-hans       |
| Kínai – hagyományos | zh-hant       |
