---
title: Az Azure AD B2C nyelvi testreszabási |} Microsoft Docs
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
ms.openlocfilehash: 097033b78e3e4f640e7bf4008fd970c53315d5d7
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Az Azure Active Directory B2C nyelvi testreszabása

Az Azure Active Directory B2C nyelvi testreszabási (az Azure AD B2C) lehetővé teszi, hogy a házirend különböző nyelveken az ügyfelek igényeinek megfelelően.  A Microsoft biztosít a fordításainak [36 nyelvet](#supported-languages), de is megadhatja a saját fordítások egyik nyelven. Akkor is, ha a felhasználói élmény előírt csak egyetlen nyelvet, testre szabhatja a lapokon szöveg.  

## <a name="how-language-customization-works"></a>Nyelvi testreszabási működése
Nyelvi testreszabási segítségével válassza ki a felhasználói használatában is elérhető nyelveket. A szolgáltatás engedélyezése után megadhatja a lekérdezési karakterlánc paraméter `ui_locales`, az alkalmazásból. Amikor az Azure AD B2C hívására, a lap le a nyelv, amely jelezte. Az ilyen típusú konfigurációs lehetővé teszi teljes felügyeletet gyakorolhat a nyelveket a a felhasználó használatában, és figyelmen kívül hagyja a nyelvi beállításokat, az ügyfél böngésző. 

Előfordulhat, hogy nem kell, hogy milyen nyelveket, az ügyfél számára megjelenített jogokat. Ha nem ad meg egy `ui_locales` paraméter, a felhasználói élmény határozza meg a böngésző beállításait.  Továbbra is szabályozhatja a felhasználó használatában lefordítani van hozzáadva egy támogatott nyelv nyelveket. Ha a megjelenítése egy nyelv, amely nem szeretne támogatni az ügyfél böngészője van beállítva, akkor inkább alapértelmezés szerint támogatott kulturális környezetek a kiválasztott nyelven jelenik meg.

- **felhasználói felület – területi beállításokhoz megadott nyelvi**: nyelvi testreszabásának engedélyezése után a felhasználó használatában itt megadott nyelvre van lefordítva.
- **Böngésző-kért nyelv**: Ha nincs `ui_locales` paraméter van megadva, a felhasználó használatában van lefordítani a böngésző által kért nyelv *Ha a nyelv támogatott*.
- **Házirend alapértelmezett nyelv**: a böngésző nem adja meg a nyelvet, vagy adja meg egy nem támogatott, ha a felhasználó út lefordították házirend alapértelmezett nyelvre.

>[!NOTE]
>Felhasználói egyéni attribútumok használata, meg kell adnia a saját fordítások. További információkért lásd: [testre szabhatja a karakterláncok](#customize-your-strings).
>

## <a name="support-requested-languages-for-uilocales"></a>A ui_locales kért nyelv támogatása 
Ez a szolgáltatás először engedélyeznie kell az általános rendelkezésre állását nyelvi testreszabása előtt létrehozott házirendek. Után létrehozott házirendek nyelvi testreszabási alapértelmezés szerint engedélyezve van. 

Ha engedélyezi a nyelvi beállítások alapján egy házirendet, szabályozhatja a felhasználó út nyelvi hozzáadása a `ui_locales` paraméter.
1. [Nyissa meg az Azure portál B2C funkciók lapjára](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings).
2. Tallózással keresse meg a házirendet, amely a fordítások engedélyezni szeretné.
3. Válassza ki **nyelvi testreszabási**.  
4. Válassza ki **nyelvi testreszabásának engedélyezése**.
5. Olvassa el a párbeszédpanelt, és válassza ki **Igen**.

## <a name="select-which-languages-in-your-user-journey-are-enabled"></a>Válassza ki a felhasználói használatában a nyelveket engedélyezve vannak 
Engedélyezze a felhasználó használatában fordítandó nélkül a böngésző által kért nyelv készlete a `ui_locales` paraméter.
1. Ügyeljen arra, hogy a házirend engedélyezve van az előző utasítások nyelvi testreszabása.
2. Az a **házirend szerkesztése** lapon jelölje be **nyelvi testreszabási**.
3. Válasszon egy nyelvet, amelyeket támogatni kíván.
4. A Tulajdonságok panelen módosíthatja **engedélyezve** való **Igen**.  
5. Válassza ki **mentése** a Tulajdonságok ablak tetején.

>[!NOTE]
>Ha egy `ui_locales` paraméter nincs megadva, a lap az ügyfél böngésző nyelvének lefordították, csak akkor, ha engedélyezve van.
>

## <a name="customize-your-strings"></a>A karakterlánc testreszabása
Nyelvi testreszabási lehetővé teszi a felhasználó használatában bármilyen karakterlánc testreszabása.
1. Ügyeljen arra, hogy a házirend-e jelölve az előző utasítások nyelvi testreszabása.
2. Az a **házirend szerkesztése** lapon jelölje be **nyelvi testreszabási**.
3. Válassza ki a nyelvet, amely a testreszabni kívánt.
4. Válassza ki a szerkeszteni kívánt oldal.
5. Válassza ki **alapértelmezett letöltése** (vagy **töltse le a felülbírálások** Ha korábban módosította a nyelvi). 

Ezeket a lépéseket egy JSON-fájl, amely segítségével a karakterláncok szerkesztéshez biztosítják.

### <a name="change-any-string-on-the-page"></a>Bármilyen karakterlánc az oldalon módosítása
1. Nyissa meg a JSON-fájlt egy JSON-szerkesztőben előző utasítások letölteni.
2. A módosítani kívánt elem található.  Található `StringId` a karakterláncot keres, vagy keresse meg a `Value` attribútumot, amelyet módosítani szeretne.
3. Frissítés a `Value` attribútum kívánt jelennek meg.
4. Minden karakterlánc, amelyet módosítani szeretne, módosítsa `Override` való `true`.
5. Mentse a fájlt, és töltse fel a módosításokat. (Megtalálhatja a fájlfeltöltés-vezérlő ugyanazon a helyen, amelybe letöltötte a JSON-fájl.) 

>[!IMPORTANT]
>Ha egy karakterlánc felülbírálása van szüksége, feltétlenül állítson be a `Override` egy érték `true`.  Ha az érték nem módosul, a rendszer figyelmen kívül hagyja a bejegyzést. 
>

### <a name="change-extension-attributes"></a>A bővítményattribútumokat módosítása
Ha szeretné módosítani egy egyéni felhasználói attribútum karakterlánc, vagy vegyen fel egyet a JSON a kívánt, a következő formátumban van:
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

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>LocalizedCollections használatával biztosítja az értékek listája
Ha szeretne biztosítani meghatározott számítógéplistán szereplő értékek a válaszok, szeretné-e létrehozni egy `LocalizedCollections` attribútum.  `LocalizedCollections` a tömb `Name` és `Value` párokat. Az elemek sorrendje lesz a sorrendben jelennek meg.  Hozzáadandó `LocalizedCollections`, használja a következő formátumot:

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

* `ElementId` a felhasználói attribútumot, amelyhez a rendszer `LocalizedCollections` attribútum választ.
* `Name` az érték a felhasználó számára látható.
* `Value` az eredményének a jogcímek e beállítás kiválasztásakor.

### <a name="upload-your-changes"></a>A módosítások feltöltéséhez
1. Miután elvégezte a módosításokat a JSON-fájlba, lépjen vissza, a B2C-bérlő.
2. Az a **házirend szerkesztése** lapon jelölje be **nyelvi testreszabási**.
3. Válassza ki a használni kívánt lefordítani.
4. Válassza a lapot, ahová fordítások.
5. Válassza ki a mappára, és válassza ki a JSON-fájl feltöltése.
 
Menti a módosításokat a házirend automatikusan.

## <a name="customize-the-page-ui-by-using-language-customization"></a>A lap felhasználói felületének testreszabása nyelvi testreszabás használatával

Két módon lehet localize a HTML-tartalmakat. Egyirányú kapcsolva a [nyelvi testreszabási](active-directory-b2c-reference-language-customization.md). Ez a funkció lehetővé teszi, hogy az Azure AD B2C az Open ID Connect paraméter továbbítására `ui-locales`, a végpontnak.  A tartalomkiszolgáló Ez a paraméter használatával adja meg a testreszabott HTML-lapok nyelvspecifikus.

Azt is megteheti hogy is lekérni a tartalmat használt terület alapján különböző helyein. A CORS-kompatibilis végpont állíthat be egy mappastruktúrát állomás tartalom bizonyos nyelveken. A megfelelőt értesítjük telefonon, ha a helyettesítő karakteres érték használata `{Culture:RFC5646}`.  Tegyük fel például, ezt követően az egyéni oldalát URI:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
A lap betöltése `fr`. Az oldal HTML- és CSS tartalmat kéri le, amikor a rendszer húzza a:
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Egyéni nyelvek hozzáadása

A Microsoft jelenleg nem fordítások a nyelveket is hozzáadhat. Adja meg a szabályzat minden karakterlánc fordítások lesz szüksége.  Nyelvi és területi kódok korlátozódnak 639-1 ISO szabvány. 

1. Az a **házirend szerkesztése** lapon jelölje be **nyelvi testreszabási**.
2. Válassza ki **egyéni nyelv hozzáadása** az oldal tetején.
3. A környezet ablaktáblán nyelveket meg van adva fordításainak írja be egy érvényes területibeállítás-kód azonosítása.
4. Az összes lapon felülbírálások készlete, angol nyelven töltse le, és a fordítások működik.
5. Miután végzett a JSON-fájlokat, az összes lapon feltöltheti azokat.
6. Válassza ki **engedélyezése**, és a csoportházirend is megjelennek a felhasználók adott nyelven.
7. Mentse a nyelvet.

>[!IMPORTANT]
>Az egyéni nyelvek engedélyezéséhez, vagy töltse fel a felülbírálások az mentése előtt kell.
>

## <a name="additional-information"></a>További információ

### <a name="page-ui-customization-labels-as-overrides"></a>Lap felhasználói felületének testreszabása címkék felülbírálások szerint
Nyelvi testreszabási engedélyezésekor címkék használatával lap felhasználói felületének testreszabása a korábbi módosítások megmaradnak a JSON-fájl az angol (en). Továbbra is módosíthatja a címkék és egyéb karakterláncok nyelvi erőforrások nyelvi testreszabás feltöltésével.
### <a name="up-to-date-translations"></a>Naprakész fordítások
A Microsoft elkötelezett a szolgáltató a legújabb fordítások a használatra. A Microsoft folyamatosan növeli a fordítások, és szinkronizálja azok megfelelőségi meg. Microsoft azonosítsa, hibákat és változások a globális terminológia, és a frissítések, amelyek működnek a zökkenőmentesen teszi a felhasználó használatában.
### <a name="support-for-right-to-left-languages"></a>Jobbról balra író nyelvek támogatása
A Microsoft jelenleg nem biztosít támogatást jobbról balra író nyelvek esetén. Ez a használatával egyéni területi beállításokat, és CSS segítségével módosíthatja, miként jelenik meg a karakterlánc érhető el.  Ha ez a funkció van szüksége, adjon szavazhatnak az [Azure visszajelzés](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Közösségi identity provider fordítások
A Microsoft biztosít a `ui_locales` közösségi bejelentkezések OIDC paraméter. Azonban néhány közösségi Identitásszolgáltatók, beleértve a Facebook-on és a Google, nem veszi őket. 
### <a name="browser-behavior"></a>Böngésző viselkedése
Chrome és Firefox mindkét kérhetnek a nyelv beállítása. Ha egy támogatott nyelvi, mielőtt az alapértelmezett jelenik meg. Peremhálózati jelenleg nem kér egy nyelvet, és rögtön az alapértelmezett nyelv kerül.

### <a name="supported-languages"></a>Támogatott nyelvek

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
| gudzsaráti              | Gu            |
| hindi                 | szia            |
| horvát              | óra            |
| magyar             | hu            |
| olasz               | it            |
| japán              | ja            |
| kannada               | kn            |
| koreai                | ko            |
| malajálam             | ml            |
| maráthi               | MR            |
| maláj                 | ms            |
| Norvég Bokmal      | nb            |
| holland                 | nl            |
| pandzsábi               | szolgáltatói            |
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
