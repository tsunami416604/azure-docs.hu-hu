---
title: Nyelvi testreszabás – az Azure AD B2C használatával |} Microsoft Docs
description: További tudnivalók a nyelvi testreszabása.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 02/26/2018
ms.author: davidmu
ms.openlocfilehash: 7c72c1d43d9a5fa541c72a8ba7a5ccedeafdaaff
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-using-language-customization"></a>Az Azure Active Directory B2C: Használatával nyelvi testreszabása

>[!NOTE]
>A funkció jelenleg nyilvános előzetes verziójához.
>

Nyelvi testreszabási lehetővé teszi, hogy a házirend különböző nyelveken az ügyfelek igényeinek megfelelően.  A Microsoft a fordítások 36 nyelvhez biztosít (lásd: [további információt](#additional-information)), de is megadhatja a saját fordítások egyik nyelven.  Akkor is, ha a felhasználói élmény csak egyetlen nyelvet előírt, testre szabhatja a lapokon szöveg.  

## <a name="how-does-language-customization-work"></a>Hogyan működik a nyelvi testreszabási?
Nyelvi testreszabási a felhasználó használatában is elérhető nyelveket kiválasztását teszi lehetővé.  Miután a szolgáltatás engedélyezve van, a lekérdezési karakterláncot, ui_locales, megadhatja az alkalmazásból.  Az Azure AD B2C hívásakor azt jelezte terület laphoz lefordítani.  Az ilyen típusú konfigurációs lehetővé teszi teljes felügyeletet gyakorolhat a nyelveket a a felhasználó használatában, és figyelmen kívül hagyja a nyelvi beállításokat, az ügyfél böngésző. Azt is megteheti nem feltétlenül kell, hogy milyen nyelveket, az ügyfél talál jogokat.  Ha nem ad meg egy ui_locales paraméter, a böngésző beállításait határozza meg a felhasználói élmény.  Továbbra is szabályozhatja a felhasználó használatában lefordítani van hozzáadva egy támogatott nyelv nyelveket.  Ha az ügyfél böngészője nem szeretne támogatni a nyelvi megjelenítése van beállítva, akkor inkább alapértelmezés szerint támogatott kulturális környezetek a kiválasztott nyelv jelenik meg.

1. **felhasználói felület – területi beállításokhoz megadott nyelvi** -nyelv testreszabási engedélyezését követően a felhasználó használatában az itt megadott lefordították
2. **Böngésző kért nyelv** -határozott meg nem ui-területi beállításokat, ha a böngésző lefordítja a kért nyelv, **Ha szerepelt támogatott nyelvek**
3. **Házirend alapértelmezett nyelv** – a böngésző nem adja meg a nyelvet, vagy adja meg egy nem támogatott, ha ez az eszköz alapértelmezett házirend-nyelve

>[!NOTE]
>Ha a felhasználói egyéni attribútumok használata esetén a saját fordítások szeretné. Lásd: "[testre szabhatja a karakterláncok](#customize-your-strings)" részleteiről.
>

## <a name="support-uilocales-requested-languages"></a>Támogatási ui_locales kért nyelvek 
A funkció engedélyezéséhez először nyelvi testreszabási általánosan rendelkezésre álló kiadása előtt létrehozott házirendek kell.  Házirendek létrehozása után nyelvi testreszabási alapértelmezés szerint engedélyezve lesz.  Azáltal, hogy a "Nyelvi beállítások" házirend alapján, most vezérelheti a felhasználók utazás nyelvét a ui_locales paraméter hozzáadásával.
1. [A lépések az Azure portálon a B2C funkciók lapra lép.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings)
2. Nyissa meg a házirendet, amely a fordítások engedélyezni szeretné.
3. Kattintson a **nyelvi testreszabási**.  
4. Kattintson a **nyelvi testreszabásának engedélyezése** felül.
5. Olvassa el a párbeszédpanelen, és kattintson az "Igen" gombra.

## <a name="select-which-languages-in-your-user-journey-are-enabled"></a>Válassza ki a felhasználói használatában a nyelveket engedélyezve vannak 
Egy meghatározott nyelv az elküldésre váró, amikor a ui_locales paraméter nincs megadva a felhasználói útra engedélyezése.
1. Győződjön meg arról, a házirend "Nyelvi testreszabási" az előző utasítások engedélyezve van.
2. Az a **házirend szerkesztése** lapon jelölje be **nyelvi testreszabási**.
3. Válasszon egy nyelvet, milyen támogatásához.
4. A Tulajdonságok panelen váltása **engedélyezve** igen.  
5. Kattintson a **mentése** a Tulajdonságok ablak tetején.

>[!NOTE]
>Ha egy ui_locales paraméter nincs megadva, majd az oldal van lefordítani az ügyfél a böngésző nyelvének csak akkor, ha engedélyezve van
>

## <a name="customize-your-strings"></a>A karakterlánc testreszabása
"Nyelvi testreszabási" lehetővé teszi a felhasználó használatában bármilyen karakterlánc testreszabását.
1. Győződjön meg arról, a házirendben engedélyezve van-e jelölve az előző utasítások "nyelvi testreszabási".
2. Az a **házirend szerkesztése** lapon jelölje be **nyelvi testreszabási**.
3. Válassza ki a testreszabni kívánt nyelvet.
4. Jelölje ki a szerkeszteni kívánt lapot.
5. Kattintson a **alapértelmezett letöltése** (vagy **töltse le a felülbírálások** Ha korábban módosította a nyelvi). 

Ezeket a lépéseket egy JSON-fájl, amely segítségével a karakterláncok szerkesztéshez biztosítják.

### <a name="changing-any-string-on-the-page"></a>Bármilyen karakterlánc az oldalon módosítása
1. Nyissa meg a JSON-fájlt egy JSON-szerkesztőben előző utasítások letölteni.
2. A módosítani kívánt elem található.  Megtalálhatja az `StringId` keres, vagy keresse meg a karakterlánc a `Value` módosítani kívánja.
3. Frissítés a `Value` attribútum kívánt jelennek meg.
4. Minden karakterlánc módosítani szeretné, ne felejtse el váltása `Override` való **igaz**.
5. Mentse a fájlt, és töltse fel a módosításokat (megtalálja a fájlfeltöltés-vezérlő, amelybe letöltötte a JSON-fájl ugyanazon a helyen). 

>[!IMPORTANT]
>Ha egy karakterlánc felülbírálása van szüksége, feltétlenül állítson be a `Override` egy érték `true`.  Ha az érték nem módosul, a rendszer figyelmen kívül hagyja a bejegyzést. 
>

### <a name="changing-extension-attributes"></a>A bővítményattribútumokat módosítása
Ha szeretné módosítani egy egyéni felhasználói attribútum karakterlánc, vagy vegyen fel egyet a JSON a szeretné hasznosítani, a következő formátumban van:
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

### <a name="using-localizedcollections"></a>LocalizedCollections használatával
Ha szeretne biztosítani meghatározott számítógéplistán szereplő értékek a válaszok, szeretné-e létrehozni egy `LocalizedCollections`.  A `LocalizedCollections` tömbje `Name` és `Value` párokat.  A `Name` jelenik meg, és a `Value` van, a jogcímek adott vissza.  Hozzáadása egy `LocalizedCollections`, a következő formátuma:

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

* `ElementId` a felhasználói attribútumot, amelyhez a rendszer `LocalizedCollections` válasz
* `Name` az érték jelenik meg a felhasználó számára
* `Value` az eredményének a jogcímek e beállítás kiválasztásakor

### <a name="upload-your-changes"></a>A módosítások feltöltéséhez
1. Miután befejezte a módosításokat a JSON-fájlba, lépjen vissza a B2C-bérlő.
2. Az a **házirend szerkesztése** lapon jelölje be **nyelvi testreszabási**.
3. Válassza ki a szeretne biztosítani a fordítás nyelvét.
4. Válassza a fordítások kívánt lapot.
5. Kattintson a mappa ikonra, és válassza ki a JSON-fájl feltöltése.
6. Ez megváltozott menti a házirend automatikusan.

## <a name="using-page-ui-customization-with-language-customization"></a>Lap felhasználói felületének testreszabása használata nyelvi testreszabása

Két módon lehet localize a HTML-tartalmakat.  Ha bekapcsolja a ["Nyelvi testreszabási"](active-directory-b2c-reference-language-customization.md).  Ez a funkció lehetővé teszi, hogy az Azure AD B2C az Open ID Connect paraméter továbbítására `ui-locales`, a végpontnak.  A tartalomkiszolgáló Ez a paraméter használatával adja meg a testreszabott HTML-lapok nyelvspecifikus.

Másik lehetőségként azt is lekérni a tartalmat a különböző helyeken alapuló használt területi beállításokat.  A CORS engedélyezése végpont állíthat be egy bizonyos nyelveken állomás tartalom mappastruktúrát és Felhívjuk a megfelelőt, írja be a helyettesítő értéket, ha `{Culture:RFC5646}`.  Ha például a saját egyéni lap URI-azonosítóként van:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
Képes betölteni a saját lapján `fr` és amikor azt van húzza html-és css, azt fogja lekérni a:
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="custom-locales"></a>Egyéni területi beállításokhoz

A Microsoft jelenleg nem fordítások a nyelveket is hozzáadhat.  Meg kell adnia a szabályzat minden karakterlánc fordítások.

1. Az a **házirend szerkesztése** lapon jelölje be **nyelvi testreszabási**.
2. Válassza ki **egyéni nyelv hozzáadása** az oldal tetején.
3. A környezet ablaktáblán nyelveket ad fordításainak írja be egy érvényes területibeállítás-kód azonosítása.
4. Az összes lapon felülbírálások készlete, angol nyelven töltse le, és a fordítások működik.
5. Ha elkészült, a JSON-fájlok, az összes lapon feltöltheti azokat.
6. Válassza ki **engedélyezése** és a csoportházirend is megjelennek-e a felhasználó nyelvét.
7. Ne felejtse el, akkor nyelvi menti azt engedélyezését követően.

## <a name="additional-information"></a>További információ

### <a name="page-ui-customization-labels-are-persisted-as-your-first-set-of-overrides-once-language-customization-is-enabled"></a>Lap felhasználói felületének testreszabása címkék a "Nyelv testreszabási" engedélyezése után az első sor felülbírálások megmaradnak
Ha engedélyezi a "Nyelv testreszabási", a korábbi módosítások címkék használatával lap felhasználói felületének testreszabása megmaradnak a JSON-fájl az angol (en).  Továbbra is a címkék és egyéb karakterláncok módosítása "Nyelvi testreszabás" nyelvi erőforrások feltöltésével.
### <a name="microsoft-is-committed-to-provide-the-most-up-to-date-translations-for-your-use"></a>A Microsoft elkötelezte magát a legfrissebb fordítások a használatára
Azt a rendszer folyamatosan fordítások javítására, és tartsa meg a megfelelőség.  Rendszer azonosítsa, hibákat és változások a globális terminológia és zökkenőmentesen végezze el a frissítéseket, amelyek működnek a felhasználó használatában.
### <a name="support-for-right-to-left-languages"></a>Jobbról balra író nyelvek támogatása
A Microsoft jelenleg nem támogatása jobbról balra író nyelvek, ha szüksége van a szolgáltatás adjon szavazzon ezt a funkciót [Azure visszajelzés](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Közösségi Identity provider fordítások
A közösségi bejelentkezések ui_locales OIDC paraméter nyújtunk, de azok nem fogadja el a néhány közösségi Identitásszolgáltatók, beleértve a Facebook-on és a Google. 
### <a name="browser-behavior"></a>Böngésző viselkedése
Chrome Firefox mindkét változáskérés és a beállított nyelven, és ha egy támogatott nyelvi, megjelenik az alapértelmezett előtt.  Peremhálózati jelenleg nem kér egy nyelvet, és rögtön az alapértelmezett nyelv kerül.

### <a name="what-languages-are-supported"></a>Milyen nyelveket támogatja?

| Nyelv              | Nyelvkód |
|-----------------------|---------------|
| bengáli                | BN            |
| cseh                 | cs            |
| dán                | da            |
| német                | de            |
| görög                 | el            |
| Angol               | hu            |
| spanyol               | es            |
| finn               | fi            |
| francia                | fr            |
| gudzsaráti              | gu            |
| hindi                 | szia            |
| horvát              | óra            |
| magyar             | hu            |
| olasz               | it            |
| japán              | ja            |
| kannada               | kn            |
| koreai                | ko            |
| malajálam             | ml            |
| maráthi               | mr            |
| maláj                 | ms            |
| Norvég Bokmal      | nb            |
| holland                 | nl            |
| pandzsábi               | pa            |
| lengyel                | pl            |
| Portugál - Brazília   | pt-br         |
| Portugál - Portugália | pt-pt         |
| román              | ro            |
| orosz               | ru            |
| szlovák                | SK            |
| svéd               | sv            |
| tamil                 | TA            |
| telugu                | Te            |
| thai                  | TH            |
| török               | TR            |
| Egyszerűsített kínai-  | zh-hans       |
| -Hagyományos kínai | zh-hant       |
