---
title: Nyelvi testreszabás az Azure Active Directory B2C-ben
description: További információ a nyelvi élmény testreszabásáról a felhasználói folyamatokban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5b3af812b2b78c276b5345b9b19226e6e1dba80b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185760"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Nyelvi testreszabás az Azure Active Directory B2C-ben

Az Azure Active Directory B2C (Azure AD B2C) nyelvi testreszabása lehetővé teszi, hogy a felhasználói folyamat az ügyfelek igényeinek megfelelően alkalmazkodjon a különböző nyelvekhez. A Microsoft [36 nyelv](#supported-languages)fordítását biztosítja, de saját fordításokat is biztosíthat bármilyen nyelvhez. Még akkor is testreszabhatja az oldalakon lévő szövegeket, ha a felhasználói élmény csak egy nyelvre van megadva.

## <a name="how-language-customization-works"></a>A nyelvi testreszabás működése

A nyelvi testreszabással kiválaszthatja, hogy a felhasználói folyamat mely nyelveken érhető el. A szolgáltatás engedélyezése után megadhatja a `ui_locales`lekérdezési karakterlánc paramétert az alkalmazásból. Amikor az Azure AD B2C hívása, az oldal lefordítása a területi beállítás, amely a megadott. Ez a konfigurációtípus teljes körű irányítást biztosít a felhasználói folyamat nyelvei felett, és figyelmen kívül hagyja az ügyfél böngészőjének nyelvi beállításait.

Előfordulhat, hogy nincs szükség arra, hogy az ügyfél milyen nyelveket lásson. Ha nem ad `ui_locales` meg paramétert, az ügyfél élményét a böngésző beállításai határozzák meg. Továbbra is szabályozhatja, hogy a felhasználói folyamat mely nyelvekre legyen lefordítva, ha támogatja azt adja hozzá. Ha egy ügyfél böngészője olyan nyelvet jelenít meg, amelyet nem szeretne támogatni, akkor a támogatott kulturális környezetben alapértelmezettként kiválasztott nyelv jelenik meg.

* **ui-locales megadott nyelv**: A nyelvi testreszabás engedélyezése után a felhasználói folyamat az itt megadott nyelvre lesz lefordítva.
* **Böngésző által kért nyelv:** Ha nem `ui_locales` adott meg paramétert, a rendszer lefordítja a felhasználói folyamatot a böngésző által kért nyelvre, ha a nyelv *támogatott.*
* **Házirend alapértelmezett nyelve:** Ha a böngésző nem ad meg nyelvet, vagy nem támogatott nyelvet ad meg, a rendszer lefordítja a felhasználói folyamat alapértelmezett nyelvét.

> [!NOTE]
> Ha egyéni felhasználói attribútumokat használ, saját fordításokat kell biztosítania. További információt a [Karakterláncok testreszabása című témakörben talál.](#customize-your-strings)

## <a name="support-requested-languages-for-ui_locales"></a>Támogatás kért nyelvek ui_locales

A nyelvi testreszabás általános elérhetősége előtt létrehozott házirendeknek először engedélyezniük kell ezt a szolgáltatást. Olyan házirendek és felhasználói folyamatok, amelyek alapértelmezés szerint a nyelvi testreszabás engedélyezése után jöttek létre.

Ha engedélyezi a nyelvi testreszabást egy felhasználói folyamaton, a `ui_locales` paraméter hozzáadásával szabályozhatja a felhasználói folyamat nyelvét.

1. Az Azure AD B2C-bérlőben válassza a **Felhasználói folyamatok**lehetőséget.
1. Kattintson arra a felhasználói folyamatra, amelyet engedélyezni szeretne a fordításokhoz.
1. **Nyelvek**kiválasztása .
1. Válassza **a Nyelvi testreszabás engedélyezése**lehetőséget.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>A felhasználói folyamat mely nyelveinek kiválasztása engedélyezve van

Engedélyezze, hogy a felhasználói folyamat nyelvkészletét lelehessen fordítani, `ui_locales` ha a böngésző a paraméter nélkül kéri.

1. Győződjön meg arról, hogy a felhasználói folyamat korábbi utasításokból engedélyezve van a nyelvi testreszabás.
1. A Felhasználói folyamat **Nyelvek lapján** válassza ki a támogatni kívánt nyelvet.
1. A Tulajdonságok ablaktáblán módosítsa az **Engedélyezve értéket** **Igen**értékre.
1. Válassza a **Mentés** gombot a tulajdonságok ablaktábla tetején.

>[!NOTE]
>Ha `ui_locales` nincs megadva paraméter, a rendszer csak akkor fordítja le az oldalt az ügyfél böngészőnyelvére, ha az engedélyezve van.
>

## <a name="customize-your-strings"></a>A karakterláncok testreszabása

A nyelvi testreszabás lehetővé teszi a felhasználói folyamat bármely karakterláncának testreszabását.

1. Győződjön meg arról, hogy a felhasználói folyamat nyelvi testreszabása engedélyezve van az előző utasításokból.
1. A Felhasználói folyamat **Nyelvek** lapján válassza ki a testre szabni kívánt nyelvet.
1. Az **Oldalszintű erőforrások fájljai csoportban**jelölje ki a szerkesztendő lapot.
1. Válassza **az Alapértelmezett letöltések (vagy** a **Letöltés felülbírálások** lehetőséget, ha korábban már szerkesztette ezt a nyelvet).

Ezek a lépések olyan JSON-fájlt adnak, amelynek segítségével elkezdheti a karakterláncok szerkesztését.

### <a name="change-any-string-on-the-page"></a>A lap bármely karakterláncának módosítása

1. Nyissa meg a JSON-szerkesztőben a korábbi utasításokból letöltött JSON-fájlt.
1. Keresse meg a módosítani kívánt elemet. Megtalálhatja `StringId` a keresett karakterláncot, vagy megkeresheti `Value` a módosítani kívánt attribútumot.
1. Frissítse `Value` az attribútumot a kívánt módon.
1. Minden módosítani kívánt karakterlánc esetében `Override` módosítsa `true`a .
1. Mentse a fájlt, és töltse fel a módosításokat. (A feltöltési vezérlőt ugyanazon a helyen találhatja meg, ahol a JSON-fájlt töltötte le.)

> [!IMPORTANT]
> Ha felül kell írnia egy karakterláncot, `Override` ügyeljen `true`arra, hogy az értéket a értékre állítsa. Ha az érték nem változik, a bejegyzés figyelmen kívül lesz hagyva.

### <a name="change-extension-attributes"></a>Kiterjesztésattribútumok módosítása

Ha módosítani szeretné egy egyéni felhasználói attribútum karakterláncát, vagy hozzá szeretne adni egyet a JSON-hoz, az a következő formátumban történik:

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

Cserélje `<ExtensionAttribute>` le az egyéni felhasználói attribútum nevére.

Cserélje `<ExtensionAttributeValue>` le a megjelenítendő új karakterláncra.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Értékek listájának biztosítása a Honosított gyűjtemények használatával

Ha meg szeretné adni a válaszok értékeinek készletlistáját, `LocalizedCollections` létre kell hoznia egy attribútumot. `LocalizedCollections`egy tömb `Name` és `Value` pár. A cikkek rendelése a megjelenített sorrend lesz. A `LocalizedCollections`hozzáadáshoz használja a következő formátumot:

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

* `ElementId`az a felhasználói attribútum, amelyre ez `LocalizedCollections` az attribútum választ ad.
* `Name`a felhasználó számára megjelenített érték.
* `Value`ez a beállítás kiválasztásakor a jogcímben visszaadott értéket adja vissza.

### <a name="upload-your-changes"></a>A módosítások feltöltése

1. Miután befejezte a JSON-fájl módosításait, lépjen vissza a B2C-bérlőhöz.
1. Válassza a **Felhasználói folyamatok lehetőséget,** és kattintson a fordításokhoz engedélyezni kívánt felhasználói folyamatra.
1. **Nyelvek**kiválasztása .
1. Válassza ki azt a nyelvet, amelyre le szeretné fordítani.
1. Válassza ki azt az oldalt, ahol fordításokat szeretne biztosítani.
1. Jelölje ki a mappa ikonját, és jelölje ki a feltöltendő JSON-fájlt.

A módosítások automatikusan mentésre kerülnek a felhasználói folyamatba.

## <a name="customize-the-page-ui-by-using-language-customization"></a>A lap felhasználói felületének testreszabása a nyelvi testreszabás sal

A HTML-tartalom honosításának két módja van. Ennek egyik módja a [nyelvi testreszabás bekapcsolása.](user-flow-language-customization.md) A funkció engedélyezése lehetővé teszi, hogy az Azure AD `ui-locales`B2C továbbítsa az OpenID Connect paramétert a végpontra. A tartalomkiszolgáló ezzel a paraméterrel nyelvspecifikus, személyre szabott HTML-lapokat biztosíthat.

Azt is megteheti, hogy a használt területi beállítás alapján különböző helyekről lekéri a tartalmat. A CORS-kompatibilis végponton beállíthat egy mappastruktúrát az adott nyelvek tartalmának tárolására. A megfelelőt fogja hívni, ha a `{Culture:RFC5646}`helyettesítő karakter értékét használja. Tegyük fel például, hogy ez az egyéni lap URI-ja:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

A lapot betöltheti a betöltésére. `fr` Amikor az oldal HTML- és CSS-tartalmat húz le, a következő ből származik:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Egyéni nyelvek hozzáadása

Olyan nyelveket is hozzáadhat, amelyekhez a Microsoft jelenleg nem biztosít fordítást. Meg kell adnia a felhasználói folyamat összes karakterláncának fordításait. A nyelvi és területi kódok az ISO 639-1 szabványban meghatározottakra korlátozódnak.

1. Az Azure AD B2C-bérlőben válassza a **Felhasználói folyamatok**lehetőséget.
2. Kattintson arra a felhasználói folyamatra, amelyhez egyéni nyelveket szeretne hozzáadni, majd kattintson a **Nyelvek gombra.**
3. A lap tetején válassza az **Egyéni nyelv hozzáadása** lehetőséget.
4. A megnyíló környezet ablaktáblában azonosítsa, hogy melyik nyelvhez biztosít fordítást egy érvényes területi kód megadásával.
5. Minden oldalhoz letöltheti az angol nyelvű felülírásokat, és dolgozhat a fordításokon.
6. Miután végzett a JSON-fájlokkal, feltöltheti őket minden oldalra.
7. Válassza **az Engedélyezés**lehetőséget, és a felhasználói folyamat most már megjelenítheti ezt a nyelvet a felhasználók számára.
8. Mentse a nyelvet.

>[!IMPORTANT]
>A mentés előtt engedélyeznie kell az egyéni nyelveket, vagy fel kell töltenie a felülírásokat.

## <a name="additional-information"></a>További információ

### <a name="page-ui-customization-labels-as-overrides"></a>A lap felhasználói felületének testreszabási címkéi felülbírálásként

Ha engedélyezi a nyelvi testreszabást, a lap felhasználói felületének testreszabását használó címkék korábbi szerkesztései megmaradnak egy angol (hu) JSON-fájlban. A címkék és más karakterláncok módosítása a nyelvi testreszabási nyelvi erőforrások feltöltésével folytatódhat.

### <a name="up-to-date-translations"></a>Naprakész fordítások

A Microsoft elkötelezett amellett, hogy a legkorszerűbb fordításokat biztosítsa az Ön számára. A Microsoft folyamatosan fejleszti a fordításokat, és megtartja azokat az Ön számára. A Microsoft azonosítja a hibákat és a globális terminológia változásait, és olyan frissítéseket készít, amelyek zökkenőmentesen működnek a felhasználói folyamatban.

### <a name="support-for-right-to-left-languages"></a>Jobbról balra író nyelvek támogatása

A Microsoft jelenleg nem támogatja a jobbról balra író nyelveket. Ezt egyéni területi beállításokkal és a CSS használatával teheti meg a karakterláncok megjelenítésének módosításához. Ha szüksége van erre a funkcióra, kérjük, szavazzon rá az [Azure Feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Közösségi identitásszolgáltató fordításai

A Microsoft `ui_locales` biztosítja az OIDC paramétert a közösségi bejelentkezések számára. De néhány közösségi identitásszolgáltatók, beleértve a Facebook és a Google, nem tartják tiszteletben őket.

### <a name="browser-behavior"></a>A böngésző viselkedése

A Chrome és a Firefox egyaránt kéri a beállított nyelvüket. Ha támogatott nyelvről van szó, akkor az alapértelmezett előtt jelenik meg. A Microsoft Edge jelenleg nem kér nyelvet, és egyenesen az alapértelmezett nyelvre megy.

## <a name="supported-languages"></a>Támogatott nyelvek

Az Azure AD B2C a következő nyelvek támogatását tartalmazza. A felhasználói folyamat nyelveit az Azure AD B2C biztosítja. A többtényezős hitelesítés (MFA) értesítési nyelveit az [Azure MFA](../active-directory/authentication/concept-mfa-howitworks.md)biztosítja.

| Nyelv              | Nyelvkód | Felhasználói folyamatok         | MFA-értesítések  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arab                | Ar            | ![nem](./media/user-flow-language-customization/no.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Bolgár             | Bg            | ![nem](./media/user-flow-language-customization/no.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Bangla                | Bn            | ![igen](./media/user-flow-language-customization/yes.png) | ![nem](./media/user-flow-language-customization/no.png) |
| Katalán               | Ca            | ![nem](./media/user-flow-language-customization/no.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Cseh                 | Cs            | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Dán                | Da            | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Német                | de de            | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Görög                 | el            | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Angol               | hu            | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Spanyol               | Igen            | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Észt              | et            | ![nem](./media/user-flow-language-customization/no.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Baszk                | Eu            | ![nem](./media/user-flow-language-customization/no.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Finn               | Fi            | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Francia                | Fr            | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Gallego              | Gl            | ![nem](./media/user-flow-language-customization/no.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| gudzsaráti              | Gu            | ![igen](./media/user-flow-language-customization/yes.png) | ![nem](./media/user-flow-language-customization/no.png) |
| Héber                | Ő            | ![nem](./media/user-flow-language-customization/no.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Hindi                 | szia            | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Horvát              | hr            | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Magyar             | hu            | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Indonéz            | id            | ![nem](./media/user-flow-language-customization/no.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Olasz               | ez            | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Japán              | ja (ja)            | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Kazak                | Kk            | ![nem](./media/user-flow-language-customization/no.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| kannada               | kn            | ![igen](./media/user-flow-language-customization/yes.png) | ![nem](./media/user-flow-language-customization/no.png) |
| Koreai                | Ko            | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Litván            | Hadnagy            | ![nem](./media/user-flow-language-customization/no.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Lett               | Lv            | ![nem](./media/user-flow-language-customization/no.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| malajálam             | Ml            | ![igen](./media/user-flow-language-customization/yes.png) | ![nem](./media/user-flow-language-customization/no.png) |
| marathi               | Mr            | ![igen](./media/user-flow-language-customization/yes.png) | ![nem](./media/user-flow-language-customization/no.png) |
| Maláj                 | Ms            | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Norvég Bokmal      | Nb            | ![igen](./media/user-flow-language-customization/yes.png) | ![nem](./media/user-flow-language-customization/no.png) |
| Holland                 | Nl            | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Norvég             | nem            | ![nem](./media/user-flow-language-customization/no.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| pandzsábi               | Pa            | ![igen](./media/user-flow-language-customization/yes.png) | ![nem](./media/user-flow-language-customization/no.png) |
| Lengyel                | Pl            | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Portugál - Brazília   | pt-br         | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Portugál - Portugália | pt-pt         | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Román              | Ro            | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Orosz               | Ru            | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Szlovák                | Sk            | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Szlovén             | Sl            | ![nem](./media/user-flow-language-customization/no.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Szerb - cirill    | sr-krio-cs    | ![nem](./media/user-flow-language-customization/no.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Szerb - Latin       | sr-latn-cs    | ![nem](./media/user-flow-language-customization/no.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Svéd               | Sv            | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| tamil                 | Ta            | ![igen](./media/user-flow-language-customization/yes.png) | ![nem](./media/user-flow-language-customization/no.png) |
| telugu                | te            | ![igen](./media/user-flow-language-customization/yes.png) | ![nem](./media/user-flow-language-customization/no.png) |
| Thai                  | Th            | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Török               | Tr            | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Ukrán             | uk            | ![nem](./media/user-flow-language-customization/no.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Vietnami            | Vi            | ![nem](./media/user-flow-language-customization/no.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Kínai - Egyszerűsített  | zh-hans között       | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
| Kínai - Hagyományos | zh-hant között       | ![igen](./media/user-flow-language-customization/yes.png) | ![igen](./media/user-flow-language-customization/yes.png) |
