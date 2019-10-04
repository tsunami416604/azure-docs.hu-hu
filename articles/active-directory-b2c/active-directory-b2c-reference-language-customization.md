---
title: Nyelvi Testreszabás a Azure Active Directory B2Cban
description: Ismerje meg a felhasználói folyamatok nyelvi élményének testreszabását.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bced7a4b994172a1a2076149d6f25adb39c99b54
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69015566"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Nyelvi Testreszabás a Azure Active Directory B2Cban

A Azure Active Directory B2C (Azure AD B2C) nyelvi testreszabása lehetővé teszi, hogy a felhasználói folyamat különböző nyelveket biztosítson az ügyfelek igényeinek megfelelően. A Microsoft a [36 nyelvekhez](#supported-languages)biztosít fordításokat, de bármilyen nyelven saját fordításokat is megadhat. Ha a felhasználói élmény csak egyetlen nyelven érhető el, testreszabhatja a lapokon található bármely szöveget.

## <a name="how-language-customization-works"></a>A nyelvi Testreszabás működése

A nyelvi testreszabással kiválaszthatja, hogy a felhasználói folyamat mely nyelveken érhető el a alkalmazásban. A szolgáltatás engedélyezése után megadhatja az alkalmazásból a lekérdezési karakterlánc `ui_locales`paraméterét. Amikor behívja a Azure AD B2Ct, a rendszer lefordítja a lapot a jelzett területi beállításra. Ez a típusú konfiguráció lehetővé teszi a felhasználói folyamat nyelveinek teljes körű ellenőrzését, és figyelmen kívül hagyja az ügyfél böngészőjének nyelvi beállításait.

Előfordulhat, hogy nincs szüksége arra, hogy milyen nyelveken látja az ügyfelet. Ha nem ad `ui_locales` meg paramétert, az ügyfél felhasználói élményét a böngésző beállításai határozzák meg. Továbbra is szabályozhatja, hogy a felhasználói folyamat mely nyelveket fordítsa le, ha azt támogatott nyelvként adja hozzá. Ha egy ügyfél böngészője olyan nyelvet mutat be, amelyet nem szeretne támogatni, akkor a támogatott kultúrákban alapértelmezettként kiválasztott nyelv jelenik meg.

* **felhasználói felület – a megadott nyelv területi beállítása**: A nyelvi Testreszabás engedélyezése után a felhasználói folyamat az itt megadott nyelvre lesz lefordítva.
* **Böngésző által kért nyelv**: Ha nem `ui_locales` adta meg a paramétert, a rendszer lefordítja a felhasználói folyamatot a böngésző által kért nyelvre, *Ha a nyelv támogatott*.
* **Házirend alapértelmezett nyelve**: Ha a böngésző nem ad meg nyelvet, vagy egy nem támogatott értéket ad meg, a felhasználói folyamat fordítása a felhasználói folyamat alapértelmezett nyelvére történik.

> [!NOTE]
> Ha egyéni felhasználói attribútumokat használ, saját fordításokat kell megadnia. További információ: [a karakterláncok testreszabása](#customize-your-strings).

## <a name="support-requested-languages-for-ui_locales"></a>A ui_locales által kért nyelvek támogatása

A nyelvi Testreszabás általánosan elérhetővé tétele előtt létrehozott szabályzatoknak először engedélyeznie kell ezt a funkciót. Az azt követően létrehozott házirendek és felhasználói folyamatok, hogy a nyelvi Testreszabás alapértelmezés szerint engedélyezve van.

Ha engedélyezi a nyelvi testreszabást egy felhasználói folyamaton, a `ui_locales` paraméter hozzáadásával szabályozhatja a felhasználói folyamat nyelvét.

1. A Azure AD B2C-bérlőben válassza a **felhasználói folyamatok**lehetőséget.
1. Kattintson arra a felhasználói folyamatra, amelyet engedélyezni szeretne a fordítások számára.
1. Válassza a **nyelvek**lehetőséget.
1. Válassza a **nyelvi Testreszabás engedélyezése**lehetőséget.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Válassza ki, hogy a felhasználói folyamat mely nyelveken legyen engedélyezve

Lehetővé teheti a felhasználói folyamat fordítását, ha a böngésző a `ui_locales` paraméter nélkül kéri le.

1. Győződjön meg arról, hogy a felhasználói folyamat rendelkezik a korábbi utasításokban engedélyezett nyelvi testreszabással.
1. A felhasználói folyamat **nyelvek** lapján válassza ki a támogatni kívánt nyelvet.
1. A Tulajdonságok ablaktáblán az **enabled** ( **Igen**) értékre vált.
1. Válassza a **Mentés** lehetőséget a Tulajdonságok ablaktábla tetején.

>[!NOTE]
>Ha a `ui_locales` paraméter nincs megadva, a lap csak akkor lesz lefordítva az ügyfél böngésző nyelvére, ha engedélyezve van.
>

## <a name="customize-your-strings"></a>A karakterláncok testreszabása

A nyelvi Testreszabás lehetővé teszi a felhasználói folyamat bármely karakterláncának testreszabását.

1. Győződjön meg arról, hogy a felhasználói folyamat rendelkezik az előző utasításokban engedélyezett nyelvi testreszabással.
1. A felhasználói folyamat **nyelvek** lapján válassza ki a testreszabni kívánt nyelvet.
1. Az **oldal szintű erőforrások fájljai**területen válassza ki a szerkeszteni kívánt oldalt.
1. Válassza az Alapértelmezések **letöltése** (vagy a **felülbírálások letöltése** lehetőséget, ha korábban már szerkesztette ezt a nyelvet).

Ezek a lépések egy JSON-fájlt biztosítanak a karakterláncok szerkesztésének megkezdéséhez.

### <a name="change-any-string-on-the-page"></a>Az oldalon lévő bármelyik sztring módosítása

1. Nyissa meg a JSON-szerkesztő korábbi utasításait letöltő JSON-fájlt.
1. Keresse meg a módosítani kívánt elemet. Keresse `StringId` meg a keresett karakterláncot, vagy keresse meg a `Value` módosítani kívánt attribútumot.
1. Frissítse az `Value` attribútumot, hogy mit szeretne megjeleníteni.
1. Minden módosítani kívánt sztring esetében váltson `Override` a `true`következőre:.
1. Mentse a fájlt, és töltse fel a módosításokat. (A feltöltési vezérlőt a JSON-fájl letöltésével megegyező helyen találja.)

> [!IMPORTANT]
> Ha felül kell bírálnia egy karakterláncot, ügyeljen arra, hogy `Override` a `true`értékre állítsa be a értéket. Ha az érték nem változik, a rendszer figyelmen kívül hagyja a bejegyzést.

### <a name="change-extension-attributes"></a>Bővítmény attribútumainak módosítása

Ha módosítani szeretné egy egyéni felhasználói attribútumhoz tartozó karakterláncot, vagy az egyiket szeretné hozzáadni a JSON-hoz, a következő formátumban kell megadnia:

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

A `<ExtensionAttribute>` helyére írja be az egyéni felhasználói attribútum nevét.

Cserélje `<ExtensionAttributeValue>` le a szöveget a megjelenítendő új sztringre.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Adja meg az értékek listáját a LocalizedCollections használatával

Ha meg szeretné adni a válaszok értékeinek listáját, létre kell hoznia egy `LocalizedCollections` attribútumot. `LocalizedCollections`a `Name` és`Value` a párok tömbje. Az elemek sorrendje a megjelenő sorrendben jelenik meg. A hozzáadáshoz `LocalizedCollections`használja a következő formátumot:

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

* `ElementId`a felhasználó attribútuma, amelyhez `LocalizedCollections` ez az attribútum válaszol.
* `Name`a felhasználó számára megjelenített érték.
* `Value`Ha ez a beállítás be van jelölve, a rendszer a jogcímben visszaadott értéket adja vissza.

### <a name="upload-your-changes"></a>A módosítások feltöltése

1. A JSON-fájl módosításainak befejezése után lépjen vissza a B2C-bérlőre.
1. Válassza a **felhasználói folyamatok** lehetőséget, majd kattintson arra a felhasználói folyamatra, amelyet engedélyezni szeretne a fordítások számára.
1. Válassza a **nyelvek**lehetőséget.
1. Válassza ki a nyelvet, amelyet le szeretne fordítani.
1. Válassza ki azt a lapot, amelyen a fordításokat meg szeretné adni.
1. Válassza ki a mappa ikont, és válassza ki a feltölteni kívánt JSON-fájlt.

A módosításokat a rendszer automatikusan menti a felhasználói folyamatba.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Az oldal felhasználói felületének testreszabása a nyelvi Testreszabás használatával

A HTML-tartalmak honosítása kétféleképpen lehetséges. Az egyik módszer a [nyelvi Testreszabás](active-directory-b2c-reference-language-customization.md)bekapcsolása. A funkció engedélyezése lehetővé teszi, hogy Azure ad B2C továbbítsa az OpenID `ui-locales`Connect paramétert a végpontnak. A tartalom-kiszolgáló ezzel a paraméterrel testreszabott, nyelvspecifikus HTML-lapokat adhat meg.

Azt is megteheti, hogy a használt területi beállítás alapján más helyekről is lehívhatja a tartalmat. A CORS-kompatibilis végponton beállíthatja, hogy a mappa szerkezete bizonyos nyelvekhez biztosítson tartalmat. Ha a helyettesítő karaktert `{Culture:RFC5646}`használja, hívja meg a megfelelőt. Tegyük fel például, hogy ez az egyéni oldal URI-ja:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

A lapot betöltheti a `fr`alkalmazásban. Amikor az oldal lekéri a HTML-és CSS-tartalmakat, a következőről indul:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Egyéni nyelvek hozzáadása

Hozzáadhat olyan nyelveket is, amelyeket a Microsoft jelenleg nem biztosít fordításokat a szolgáltatáshoz. A felhasználói folyamat összes karakterláncához meg kell adnia a fordításokat. A nyelvi és területi beállítási kódok az ISO 639-1 szabványban foglalt értékekre korlátozódnak.

1. A Azure AD B2C-bérlőben válassza a **felhasználói folyamatok**lehetőséget.
2. Kattintson arra a felhasználói folyamatra, amelyhez egyéni nyelveket kíván hozzáadni, majd kattintson a **nyelvek**elemre.
3. Válassza az **Egyéni nyelv hozzáadása** lehetőséget az oldal tetején.
4. A megnyíló környezet ablaktáblán azonosítsa, hogy egy érvényes területi beállítás megadásával melyik nyelvet használja a fordítások számára.
5. Minden oldalon letöltheti az angol nyelvű felülbírálások készletét, és dolgozhat a fordításokon is.
6. Miután elkészült a JSON-fájlokkal, feltöltheti őket az egyes lapokra.
7. Válassza az **Engedélyezés**lehetőséget, és a felhasználói folyamat mostantól megjelenítheti a felhasználók számára a nyelvet.
8. Mentse a nyelvet.

>[!IMPORTANT]
>A Mentés előtt engedélyeznie kell az egyéni nyelveket, vagy fel kell töltenie a felülbírálásokat.

## <a name="additional-information"></a>További információ

### <a name="page-ui-customization-labels-as-overrides"></a>Oldal felhasználói felületének testreszabása feliratok felülbírálásként

Ha engedélyezi a nyelvi testreszabást, az oldal felhasználói felületének testreszabását használó címkék korábbi szerkesztési adatai megmaradnak az angol (en) JSON-fájlban. A nyelvi testreszabási nyelvi erőforrások feltöltésével továbbra is módosíthatja a címkéket és más karakterláncokat.

### <a name="up-to-date-translations"></a>Naprakész fordítások

A Microsoft elkötelezte magát arra, hogy a legnaprakészebb fordításokat biztosítson a használathoz. A Microsoft folyamatosan fejleszti a fordításokat, és megfelel az Ön számára. A Microsoft azonosítja a hibákat és a globális terminológiában bekövetkező változásokat, és olyan frissítéseket tesz elérhetővé, amelyek zökkenőmentesen fognak működni a felhasználói folyamat során.

### <a name="support-for-right-to-left-languages"></a>Jobbról balra író nyelvek támogatása

A Microsoft jelenleg nem nyújt támogatást a jobbról balra író nyelvekhez. Ezt az egyéni területi beállítások és a CSS használatával végezheti el a karakterláncok megjelenítésének megváltoztatásához. Ha erre a szolgáltatásra van szüksége, szavazzon rá az [Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag)-visszajelzésre.

### <a name="social-identity-provider-translations"></a>Közösségi identitás-szolgáltatói fordítások

A Microsoft biztosítja `ui_locales` a OIDC paramétert a közösségi bejelentkezésekhez. De egyes közösségi identitás-szolgáltatók, például a Facebook és a Google is, ne tartsák tiszteletben őket.

### <a name="browser-behavior"></a>Böngésző viselkedése

A Chrome és a Firefox is a beállított nyelvre irányuló kérelem. Ha ez egy támogatott nyelv, az alapértelmezés szerint megjelenik. A Microsoft Edge jelenleg nem kér nyelvet, és egyenesen az alapértelmezett nyelvre lép.

## <a name="supported-languages"></a>Támogatott nyelvek

A Azure AD B2C a következő nyelvek támogatását tartalmazza. A felhasználói folyamat nyelveit Azure AD B2C biztosítjuk. A multi-Factor Authentication (MFA) értesítési nyelveit az [Azure MFA](../active-directory/authentication/concept-mfa-howitworks.md)nyújtja.

| Nyelv              | Nyelvkód | Felhasználói folyamatok         | MFA-értesítések  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| arab                | AR            | x                | :heavy_check_mark: |
| bolgár             | BG            | x                | :heavy_check_mark: |
| Bengáli                | Mrd USD            | :heavy_check_mark: | x                |
| katalán               | CA            | x                | :heavy_check_mark: |
| cseh                 | cs            | :heavy_check_mark: | :heavy_check_mark: |
| dán                | da            | :heavy_check_mark: | :heavy_check_mark: |
| német                | de            | :heavy_check_mark: | :heavy_check_mark: |
| görög                 | el            | :heavy_check_mark: | :heavy_check_mark: |
| Angol               | hu-HU            | :heavy_check_mark: | :heavy_check_mark: |
| spanyol               | es            | :heavy_check_mark: | :heavy_check_mark: |
| észt              | et            | x                | :heavy_check_mark: |
| baszk                | EU            | x                | :heavy_check_mark: |
| finn               | fi            | :heavy_check_mark: | :heavy_check_mark: |
| francia                | fr            | :heavy_check_mark: | :heavy_check_mark: |
| galíciai              | GL            | x                | :heavy_check_mark: |
| gudzsaráti              | Gu            | :heavy_check_mark: | x                |
| héber                | ő            | x                | :heavy_check_mark: |
| hindi                 | szia            | :heavy_check_mark: | :heavy_check_mark: |
| horvát              | óra            | :heavy_check_mark: | :heavy_check_mark: |
| magyar             | hu            | :heavy_check_mark: | :heavy_check_mark: |
| indonéz            | id            | x                | :heavy_check_mark: |
| olasz               | it            | :heavy_check_mark: | :heavy_check_mark: |
| japán              | ja            | :heavy_check_mark: | :heavy_check_mark: |
| kazak                | KK            | x                | :heavy_check_mark: |
| kannada               | KN            | :heavy_check_mark: | x                |
| koreai                | ko            | :heavy_check_mark: | :heavy_check_mark: |
| litván            | lt            | x                | :heavy_check_mark: |
| lett               | lv            | x                | :heavy_check_mark: |
| malajálam             | ml            | :heavy_check_mark: | x                |
| maráthi               | Mr            | :heavy_check_mark: | x                |
| maláj                 | ms            | :heavy_check_mark: | :heavy_check_mark: |
| Norvég bokmal      | nb            | :heavy_check_mark: | x                |
| holland                 | nl            | :heavy_check_mark: | :heavy_check_mark: |
| norvég             | nem            | x                | :heavy_check_mark: |
| pandzsábi               | PA            | :heavy_check_mark: | x                |
| lengyel                | pl            | :heavy_check_mark: | :heavy_check_mark: |
| Portugál – Brazília   | pt-br         | :heavy_check_mark: | :heavy_check_mark: |
| Portugál – Portugália | pt-pt         | :heavy_check_mark: | :heavy_check_mark: |
| román              | ro            | :heavy_check_mark: | :heavy_check_mark: |
| orosz               | ru            | :heavy_check_mark: | :heavy_check_mark: |
| szlovák                | sk            | :heavy_check_mark: | :heavy_check_mark: |
| szlovén             | SL            | x                | :heavy_check_mark: |
| Szerb – cirill betűs    | SR-cryl-cs    | x                | :heavy_check_mark: |
| Szerb – latin betűs       | SR-Latn-cs    | x                | :heavy_check_mark: |
| svéd               | sv            | :heavy_check_mark: | :heavy_check_mark: |
| tamil                 | ta            | :heavy_check_mark: | x                |
| telugu                | ttelepítése            | :heavy_check_mark: | x                |
| thai                  | .            | :heavy_check_mark: | :heavy_check_mark: |
| török               | TR            | :heavy_check_mark: | :heavy_check_mark: |
| ukrán             | uk            | x                | :heavy_check_mark: |
| vietnami            | VI            | x                | :heavy_check_mark: |
| Egyszerűsített kínai  | zh-Hans       | :heavy_check_mark: | :heavy_check_mark: |
| Kínai – hagyományos | zh-Hant       | :heavy_check_mark: | :heavy_check_mark: |
