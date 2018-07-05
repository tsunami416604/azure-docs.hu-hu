---
title: Nyelvi testreszabás az Azure Active Directory B2C |} A Microsoft Docs
description: További információ a nyelvi testreszabása.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: affd52352dcc745557dd66c61ccfa1e7a99dcdb7
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442321"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Az Azure Active Directory B2C a nyelvi testreszabás

Nyelvi testreszabás az Azure Active Directory B2C (Azure AD B2C-vel) lehetővé teszi, hogy a szabályzat különböző nyelvekhez az ügyfelek igényeinek megfelelően.  A Microsoft biztosít a fordításainak [36 nyelvet](#supported-languages), de is megadhatja a saját fordításokat bármely nyelven. Akkor is, ha a felhasználói élmény csak egyetlen nyelven áll rendelkezésre, testre szabható a lapokon szöveg.  

## <a name="how-language-customization-works"></a>Hogyan működik a nyelvi testreszabás
Milyen nyelveken érhető el a felhasználói interakciósorozat válassza ki a nyelvi testreszabás használatával. Miután a szolgáltatás engedélyezve van, megadhatja a lekérdezési karakterlánc paramétereként `ui_locales`, az alkalmazásból. Amikor kapcsolatba lép az Azure AD B2C-be, a lap a területi beállítás, amely jelezte, hogy le. Ezt a konfigurációtípust figyelmen kívül hagyja a nyelvi beállításokat, az ügyfél böngésző és a felhasználói interakciósorozatban szereplő nyelvek teljes körű kínálja. 

Milyen nyelveket, az ügyfél látja, hogy szintű nincs szüksége lehet. Ha nem ad meg egy `ui_locales` paramétert, a felhasználói élmény szabja meg a böngésző beállításait.  Továbbra is szabályozhatja, hogy a felhasználói interakciósorozat fordítja le egy támogatott nyelvre úgy nyelveket. Ha a felhasználó böngészőben megjelenítése nyelv, amelyet szeretne támogatására van beállítva, akkor ehelyett alapértelmezés szerint a támogatott kulturális környezetek a kiválasztott nyelv jelenik meg.

- **felhasználói felület – területi beállításokhoz megadott nyelvi**: Miután engedélyezte a nyelvi testreszabás, a felhasználói út az itt megadott nyelvre lefordították.
- **Böngésző által kért nyelv**: Ha nincs `ui_locales` paraméter van megadva, a felhasználói interakciósorozat fordítja le a böngésző által kért nyelv *, ha a nyelv támogatott*.
- **A házirend alapértelmezett nyelve**: Ha a böngésző nem adja meg a nyelvet, vagy adja meg, amely nem támogatott, a felhasználói út házirend alapértelmezett nyelvét fordítja le.

>[!NOTE]
>Ha felhasználói egyéni attribútumok használata esetén meg kell adnia saját fordításait. További információkért lásd: [testre szabhatja a karakterláncok](#customize-your-strings).
>

## <a name="support-requested-languages-for-uilocales"></a>Az ui_locales paraméter a kért nyelv támogatása 
Ez a funkció először engedélyeznie kell a nyelvi testreszabás általános elérhetősége előtt létrehozott házirendek. Után létrehozott házirendek a nyelvi testreszabás alapértelmezés szerint engedélyezve van. 

Ha engedélyezi a szabályzat nyelvi testreszabás, a nyelv, a felhasználói interakciósorozatában szereplő hozzáadásával szabályozhatja a `ui_locales` paraméter.
1. [Nyissa meg az Azure portal B2C funkciók lapjának](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings).
2. Keresse meg a szabályzatot, amely a fordítások engedélyezni kívánja.
3. Válassza ki **nyelvi testreszabás**.  
4. Válassza ki **nyelvi testreszabás engedélyezése**.
5. Olvassa el az adatokat a párbeszédpanelen, és válassza ki **Igen**.

## <a name="select-which-languages-in-your-user-journey-are-enabled"></a>Válassza ki a felhasználói interakciósorozatban szereplő nyelveket engedélyezve vannak 
Engedélyezi a nyelvek a felhasználói út tartalomdefinícióinak fordítandó nélkül a böngésző által kért készletét a `ui_locales` paraméter.
1. Győződjön meg arról, hogy a szabályzat nyelvi testreszabás engedélyezve van az előző utasítások.
2. Az a **házirend szerkesztése** lapon jelölje be **nyelvi testreszabás**.
3. Válassza ki a támogatni kívánt nyelv.
4. A Tulajdonságok panelen módosítsa **engedélyezve** való **Igen**.  
5. Válassza ki **mentése** tulajdonságpanel tetején.

>[!NOTE]
>Ha egy `ui_locales` paraméter nincs megadva, az oldalra fordítja le az ügyfél a böngésző nyelve csak akkor, ha engedélyezve van.
>

## <a name="customize-your-strings"></a>A karakterláncok testreszabása
Nyelvi testreszabás lehetővé teszi a felhasználói interakciósorozatban szereplő bármely karakterlánc testreszabása.
1. Győződjön meg arról, hogy a szabályzat nyelvi testreszabás engedélyezve lett az előző utasítások rendelkezik-e.
2. Az a **házirend szerkesztése** lapon jelölje be **nyelvi testreszabás**.
3. Válassza ki a nyelvet, amelyet testre szeretne.
4. Válassza ki a szerkeszteni kívánt lapra.
5. Válassza ki **Alapértelmezések letöltése** (vagy **felülbírálások letöltése** Ha korábban szerkesztése ezen a nyelven). 

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
2. Az a **házirend szerkesztése** lapon jelölje be **nyelvi testreszabás**.
3. Válassza ki a kívánt fordítás.
4. Jelölje ki a lapot, ahol a fordítások szeretné.
5. Válassza ki a mappa ikont, és válassza ki a JSON-fájlt a feltöltéshez.
 
Menti a módosításokat a szabályzatot automatikusan.

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

A Microsoft jelenleg nem biztosít a fordítások nyelveket is hozzáadhat. Adja meg a szabályzat minden karakterlánc a fordításokat kell.  Nyelvi és területi beállítás kódok korlátozva, az ISO 639-1 szabvány lévőket. 

1. Az a **házirend szerkesztése** lapon jelölje be **nyelvi testreszabás**.
2. Válassza ki **egyéni nyelv hozzáadása** az oldal tetején.
3. A megnyíló helyi ablaktáblán azonosítsa a nyelvét, adja-e a fordítások adjon meg egy érvényes területi kódot.
4. Minden olyan lap töltse le a felülbírálások egy készletét az angol nyelvű tájékoztatáshoz, és a fordítások dolgozhat.
5. Miután végzett, az a JSON-fájlok, feltöltheti őket minden egyes laphoz.
6. Válassza ki **engedélyezése**, és a szabályzat mostantól jeleníti meg ezt a nyelvet, a felhasználók számára.
7. Mentse a nyelvet.

>[!IMPORTANT]
>Felülbírálások feltöltése a hozzá tartozó mentése előtt vagy a egyéni nyelveket kell.
>

## <a name="additional-information"></a>További információ

### <a name="page-ui-customization-labels-as-overrides"></a>Oldal-UI testreszabása címkékre, felülbírálások
Ha engedélyezi a nyelvi testreszabás, a címkék használatával az oldal-UI testreszabása a korábbi módosítások megmaradnak az egy JSON-fájlt az angol (en). Továbbra is módosíthatja a címkék és egyéb karakterláncokat a nyelvi testreszabás nyelvi erőforrás feltöltése.
### <a name="up-to-date-translations"></a>Naprakész fordítások
A Microsoft elkötelezett a szolgáltató a legfrissebb fordításokat használható. A Microsoft folyamatosan javítja a Fordítások és tartja őket a felel meg. A Microsoft fogja azonosíthatja a hibák és változások a globális terminológia és zökkenőmentesen győződjön meg a frissítéseket, amelyek működnek a felhasználói interakciósorozat.
### <a name="support-for-right-to-left-languages"></a>Jobbról balra író nyelvek támogatása
A Microsoft jelenleg nem biztosít támogatást jobbról balra író nyelveket. Ez elvégezhető egyéni nyelv használatával, és CSS használatával megváltoztatni a karakterláncok jelennek meg.  Ha ez a funkció van szüksége, kérjük szavazzon rá a [Azure visszajelzési](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Közösségi szolgáltató fordítások
A Microsoft biztosít a `ui_locales` közösségi bejelentkezések OIDC paramétert. De néhány közösségi identitásszolgáltató, többek között a Facebookhoz és a Google, ne fogadja el őket. 
### <a name="browser-behavior"></a>Böngésző viselkedés
Chrome és a Firefox beállított nyelvet is kérhetnek. Ha egy támogatott nyelvre, mielőtt az alapértelmezett jelenik meg. Edge jelenleg nem kér egy nyelvet, és rögtön az alapértelmezett nyelv kerül.

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
| hindi                 | szia            |
| horvát              | óra            |
| magyar             | hu            |
| olasz               | it            |
| japán              | ja            |
| kannada               | KN            |
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
| szlovák                | SK            |
| svéd               | sv            |
| tamil                 | TA            |
| telugu                | Te            |
| thai                  | .            |
| török               | TR            |
| Egyszerűsített kínai –  | zh-hans       |
| Kínai – hagyományos | zh-hant       |
