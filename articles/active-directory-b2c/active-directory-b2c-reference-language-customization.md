---
title: "Az Azure Active Directory B2C: Használatával nyelvi testreszabási |} Microsoft Docs"
description: 
services: active-directory-b2c
documentationcenter: 
author: sama
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/25/2017
ms.author: sama
ms.openlocfilehash: 3c7c49ee5fbd98762da0eef6f02e7c2f036453cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-using-language-customization"></a>Az Azure Active Directory B2C: Használatával nyelvi testreszabása

>[!NOTE] 
>A funkció jelenleg nyilvános előzetes verziójához.  Javasoljuk, hogy ez a funkció használata esetén használjon egy tesztelési bérlőn.  Nem tervezzük az általánosan rendelkezésre álló kiadási a Preview legfrissebb módosításokat, de azt lefoglalni a jogot arra, hogy a változások a szolgáltatás javítására.  Miután lehetősége kipróbálni funkciót, adja meg az Önnek nyújtott élményt visszajelzést, és hogyan tökéletesíthetjük az jobb.  Visszajelzést küldhet az Azure portálon keresztül a jobb felső sarokban lévő arc arcfelismerési eszközzel.   Ha egy követelmény, hogy az éles üzem ezzel a szolgáltatással az előzetes fázisban, ossza meg velünk az esetek, és azt adja meg a megfelelő útmutatást és a támogatás.  Akkor is lépjen velünk kapcsolatba [ aadb2cpreview@microsoft.com ](mailto:aadb2cpreview@microsoft.com).

Nyelvi testreszabási lehetővé teszi, hogy a felhasználó használatában módosíthatja a felhasználói igényeknek nyelve.  Fordítások 36 nyelvhez nyújtunk (lásd: [további információt](#additional-information)).  Akkor is, ha a felhasználói élmény csak egyetlen nyelvet előírt, testre szabhatja az igényeinek oldalain szöveg.  

## <a name="how-does-language-customization-work"></a>Hogyan működik a nyelvi testreszabási?
Nyelvi testreszabási a felhasználó használatában is elérhető nyelveket kiválasztását teszi lehetővé.  Miután a szolgáltatás engedélyezve van, a lekérdezési karakterláncot, ui_locales, megadhatja az alkalmazásból.  Az Azure AD B2C hívásakor azt jelezte terület laphoz lefordítani.  Típusú konfiguráció használata lehetővé teszi teljes felügyeletet gyakorolhat a nyelveket a a felhasználó használatában, és figyelmen kívül hagyja a nyelvi beállításokat, az ügyfél böngésző. Azt is megteheti nem feltétlenül kell, hogy milyen nyelveket, az ügyfél talál jogokat.  Ha nem ad meg egy ui_locales paraméter, a böngésző beállításait határozza meg a felhasználói élmény.  Továbbra is szabályozhatja a felhasználó használatában lefordítani van hozzáadva egy támogatott nyelv nyelveket.  Ha az ügyfél böngészője nem szeretne támogatni a nyelvi megjelenítése van beállítva, akkor inkább alapértelmezés szerint támogatott kulturális környezetek a kiválasztott nyelv jelenik meg.

1. **felhasználói felület – területi beállításokhoz megadott nyelvi** -nyelv testreszabási engedélyezését követően a felhasználó használatában az itt megadott lefordították
2. **Böngésző kért nyelv** -határozott meg nem ui-területi beállításokat, ha a böngésző lefordítja a kért nyelv, **Ha szerepelt támogatott nyelvek**
3. **Házirend alapértelmezett nyelv** – a böngésző nem adja meg a nyelvet, vagy adja meg egy nem támogatott, ha ez az eszköz alapértelmezett házirend-nyelve

>[!NOTE]
>Ha a felhasználói egyéni attribútumok használata esetén a saját fordítások szeretné. Lásd: "[testre szabhatja a karakterláncok](#customize-your-strings)" részleteiről.
>

## <a name="support-uilocales-requested-languages"></a>Támogatási ui_locales kért nyelvek 
Azáltal, hogy a "Nyelvi beállítások" házirend alapján, most vezérelheti a felhasználók utazás nyelvét a ui_locales paraméter hozzáadásával.
1. [Kövesse az alábbi lépéseket az Azure portálon a B2C funkciók panelje megnyitására.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings)
2. Nyissa meg a házirendet, amely a fordítások engedélyezni szeretné.
3. Kattintson a **nyelvi testreszabási**.
4. A figyelmeztetés olvassa el körültekintően.  Miután engedélyezte őket, nem kapcsolható ki "Nyelvi testreszabási".
5. Kapcsolja be a szolgáltatást, és kattintson a **OK**.
6. Mentse a házirendet a bal felső sarkában a **házirend szerkesztése** panelen.

## <a name="select-which-languages-your-user-journey-supports"></a>Válassza ki a felhasználói használatában támogatja nyelveket 
Hozzon létre az elküldésre váró, amikor a ui_locales paraméter nincs megadva a felhasználói útra engedélyezett nyelvek listáját.
1. Győződjön meg arról, a házirend "Nyelvi testreszabási" az előző utasítások engedélyezve van.
2. Az a **házirend szerkesztése** panelen válassza **nyelvi testreszabási**.
3. A következő lépés a **támogatott nyelv** panelen.  Itt választhatja ki **nyelv hozzáadása**.
4. Válassza ki a támogatni kívánt nyelveket.  

>[!NOTE]
>Ha egy ui_locales paraméter nincs megadva, majd az oldal van lefordítani az ügyfél a böngésző nyelvének csak akkor, ha szerepel a listán
>

5. Kattintson a **Ok** alján
6. Zárja be a **nyelvi testreszabási** panel és **mentése** a házirendet.

## <a name="customize-your-strings"></a>A karakterlánc testreszabása
"Nyelvi testreszabási" lehetővé teszi a felhasználó használatában bármilyen karakterlánc testreszabását.
1. Győződjön meg arról, a házirendben engedélyezve van-e jelölve az előző utasítások "nyelvi testreszabási".
2. Az a **házirend szerkesztése** panelen válassza **nyelvi testreszabási**.
3. Válassza a bal oldali navigációs menü **Tartalomletöltéshez**.
4. Jelölje ki a szerkeszteni kívánt lapot.
5. A legördülő listában válassza ki a szerkeszteni kívánt nyelv.
6. Kattintson a **Letöltés** gombra. 

Ezeket a lépéseket egy JSON-fájl, amely segítségével a karakterláncok szerkesztéshez biztosítják.

### <a name="changing-any-string-on-the-page"></a>Bármilyen karakterlánc az oldalon módosítása
1. Nyissa meg a JSON-fájlt egy JSON-szerkesztőben előző utasítások letölteni.
2. A módosítani kívánt elem található.  Megtalálhatja az `StringId` keres, vagy keresse meg a karakterlánc a `Value` módosítani kívánja.
3. Frissítés a `Value` attribútum kívánt jelennek meg.
4. Mentse a fájlt, és töltse fel a módosításokat.

### <a name="changing-extension-attributes"></a>A bővítményattribútumokat módosítása
Ha szeretné módosítani egy egyéni felhasználói attribútum karakterlánc, vagy vegyen fel egyet a JSON a szeretné hasznosítani, a következő formátumban van:
```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": false,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```
>[!IMPORTANT]
>Ha egy karakterlánc felülbírálása van szüksége, feltétlenül állítson be a `Override` egy érték `true`.  Ha az érték nem módosul, a rendszer figyelmen kívül hagyja a bejegyzést. 
>

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
      "Override": false,
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
>[!IMPORTANT]
>Ha egy karakterlánc felülbírálása van szüksége, feltétlenül állítson be a `Override` egy érték `true`.  Ha az érték nem módosul, a rendszer figyelmen kívül hagyja a bejegyzést. 
>

* `ElementId`a felhasználói attribútumot, amelyhez a rendszer `LocalizedCollections` válasz
* `Name`az érték jelenik meg a felhasználó számára
* `Value`az eredményének a jogcímek e beállítás kiválasztásakor

### <a name="upload-your-changes"></a>A módosítások feltöltéséhez
1. Miután befejezte a módosításokat a JSON-fájlba, lépjen vissza a B2C-bérlő.
2. Az a **házirend szerkesztése** panelen válassza **nyelvi testreszabási**.
3. Válassza a bal oldali navigációs menü **tartalom feltöltése**.
4. Válassza ki a lapon, a módosítások a feltölteni kívánt.
5. Ha szeretne feltölteni egy nyelv, amely a JSON a korábban megadott, az előzőt törölni szeretne.  Kattintva törölheti a `...` adott nyelv, és válassza ki a jobb oldali **törlése**.
6. Kattintson a **Hozzáadás** bal felső.
7. Válassza ki a nyelvet a JSON-fájl.
8. A mappa gombra a jobb oldalon, majd keresse meg a JSON-fájl.
9. Kattintson a **feltöltése** gomb a panel alján.
10. Lépjen vissza a **házirend szerkesztése** panel megnyitásához, és kattintson **mentése**.

## <a name="additional-information"></a>További információ
### <a name="recommendations-for-language-customization"></a>A testreszabáshoz"nyelv" javaslatok
Azt javasoljuk, hogy csak a bejegyzéseket a nyelvi erőforrásokra explicit módon lecserélni kívánt karakterláncok üzembe.  A fájl a JSON-fordítások kívül lefordított méretkorlátot kényszerítése azt.  Ha a fájl túl nagy, akkor teljesítményére hatással van a felhasználó használatában.
### <a name="page-ui-customization-labels-are-removed-once-language-customization-is-enabled"></a>Lap felhasználói felületének testreszabása címkék "Nyelvi testreszabási" engedélyezése után törlődnek.
Ha engedélyezi a "Nyelv testreszabási", a korábbi módosítások címkék használatával lap felhasználói felületének testreszabása felhasználói egyéni attribútumok kivételével a rendszer törli.  Ez a változás történik a, ahol szerkesztheti a karakterláncok ütközések elkerülése érdekében.  Továbbra is a címkék és egyéb karakterláncok módosítása "Nyelvi testreszabás" nyelvi erőforrások feltöltésével.
### <a name="microsoft-is-committed-to-provide-the-most-up-to-date-translations-for-your-use"></a>A Microsoft elkötelezte magát a legfrissebb fordítások a használatára
Azt a rendszer folyamatosan fordítások javítására, és tartsa meg a megfelelőség.  Rendszer azonosítsa, hibákat és változások a globális terminológia és zökkenőmentesen végezze el a frissítéseket, amelyek működnek a felhasználó használatában.
### <a name="support-for-right-to-left-languages"></a>Jobbról balra író nyelvek támogatása
Nincs jobbról balra író nyelvek esetén nem támogatott, ha szüksége van a szolgáltatás adja ezt a funkciót szavazzon [Azure visszajelzés](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Közösségi Identity provider fordítások
A közösségi bejelentkezések ui_locales OIDC paraméter nyújtunk, de azok nem fogadja el a néhány közösségi Identitásszolgáltatók, beleértve a Facebook-on és a Google. 
### <a name="browser-behavior"></a>Böngésző viselkedése
Chrome Firefox mindkét változáskérés és a beállított nyelven, és ha egy támogatott nyelvi, megjelenik az alapértelmezett előtt.  Peremhálózati jelenleg nem kér egy nyelvet, és rögtön az alapértelmezett nyelv kerül.
### <a name="known-issues"></a>Ismert problémák
* A többtényezős hitelesítés lap egy profil szerkesztése házirendben nyelvi erőforrások feltöltése jelenleg nem érhető el.
* `LocalizedCollections`értékek nem létre, amikor arra szükség van a válasz típusa
### <a name="what-if-i-want-a-language-that-isnt-supported"></a>Mi történik, ha nem támogatott nyelven szeretnék?
Azt tervezi, hogy ez a szolgáltatás, amely lehetővé teszi egy JSON-erőforrás "egyéni nyelvek" felé feltöltése kiterjesztést adja meg.  A funkció lehetővé teszi, hogy adja meg a nevet és a nyelvi kódját, minden nyelvhez, és adhat meg *összes* a fordítások az adott nyelven.  Ha ez a funkció van szüksége, küldjön nekünk a forgatókönyvet [ aadb2cpreview@microsoft.com ](mailto:aadb2cpreview@microsoft.com).  

### <a name="what-languages-are-supported"></a>Milyen nyelveket támogatja?

| Nyelv              | Nyelvkód |
|-----------------------|---------------|
| Bangla                | BN            |
| cseh                 | cs            |
| dán                | da            |
| német                | de            |
| görög                 | el            |
| Angol               | en            |
| spanyol               | es            |
| finn               | fi            |
| francia                | fr            |
| gudzsaráti              | Gu            |
| hindi                 | szia            |
| horvát              | HR            |
| magyar             | hu            |
| olasz               | it            |
| japán              | ja            |
| kannada               | KN            |
| koreai                | ko            |
| malajálam             | gépi tanulás            |
| marathi               | MR            |
| maláj                 | MS            |
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
