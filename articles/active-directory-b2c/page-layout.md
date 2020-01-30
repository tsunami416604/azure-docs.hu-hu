---
title: Lapelrendezés kiválasztása – Azure Active Directory B2C
description: További információ az oldalelrendezés kiválasztásáról a Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 452687f3886a85bea796e3899410667ee1d592fa
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840315"
---
# <a name="select-a-page-layout-in-azure-active-directory-b2c-using-custom-policies"></a>Lapelrendezés kiválasztása Azure Active Directory B2C egyéni házirendek használatával

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Engedélyezheti a JavaScript ügyféloldali kódot a Azure Active Directory B2C (Azure AD B2C) házirendekben, függetlenül attól, hogy felhasználói folyamatokat vagy egyéni házirendeket használ. Az alkalmazásokhoz való JavaScript engedélyezéséhez hozzá kell adnia egy elemet az [Egyéni szabályzathoz](custom-policy-overview.md), ki kell választania egy lapelrendezést, és a [b2clogin.com](b2clogin.md) kell használnia a kérésekben.

Az oldalelrendezés olyan elemek társítása, amelyeket Azure AD B2C biztosít, valamint az Ön által megadott tartalmat.

Ez a cikk azt ismerteti, hogyan lehet kijelölni egy lapelrendezést a Azure AD B2Cban úgy, hogy egy egyéni szabályzatban konfigurálja azt.

> [!NOTE]
> Ha engedélyezni szeretné a JavaScriptet a felhasználói folyamatokhoz, tekintse [meg a JavaScript-és lapelrendezés-verziókat a Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="replace-datauri-values"></a>DataUri értékek cseréje

Az egyéni szabályzatokban lehetnek olyan [ContentDefinitions](contentdefinitions.md) , amelyek meghatározzák a felhasználói úton használt HTML-sablonokat. A **ContentDefinition** olyan **DataUri** tartalmaz, amely a Azure ad B2C által biztosított oldal elemeire hivatkozik. A **tartalomdefinícióban** az Ön által megadott HTML-és CSS-tartalom relatív elérési útja.

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

Az oldalelrendezés kiválasztásához módosítsa a [ContentDefinitions](contentdefinitions.md) lévő **DataUri** értékeket a szabályzatokban. Ha a régi **DataUri** értékeket az új értékekre vált, kiválaszt egy megváltoztathatatlan csomagot. A csomag használatának előnye, hogy tudja, hogy nem változik, és váratlan viselkedést okoz a lapon.

Ha a régi **DataUri** értéket használó egyéni házirendekben szeretne megadni egy lapelrendezést, szúrjon be `contract` `elements` és az oldal típusa között (például `selfasserted`), és adja meg a verziószámot. Példa:

| Régi DataUri érték | Új DataUri érték |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0` |

## <a name="version-change-log"></a>Verziószám-változási napló

A lapelrendezés-csomagok rendszeres időközönként frissülnek, hogy tartalmazzanak javításokat és javításokat a lapok elemeiben. A következő változási napló meghatározza az egyes verziókban bevezetett módosításokat.

### <a name="200"></a>2.0.0

- Önérvényesített oldal (`selfasserted`)
  - A [megjelenítési vezérlők](display-controls.md) támogatása az egyéni házirendekben.

### <a name="120"></a>1.2.0

- Minden oldal
  - Kisegítő lehetőségek – javítások
  - Most hozzáadhatja a `data-preload="true"` attribútumot a HTML-címkékben a CSS és a JavaScript betöltési sorrendjének szabályozásához. A forgatókönyvek a következők:
    - Használja ezt a CSS-hivatkozást a CSS-fájlnak a HTML-sel való betöltéséhez, hogy ne legyen "villódzásmentes" a fájlok betöltése között
    - Ez az attribútum lehetővé teszi a parancsfájl-címkék beolvasásának és végrehajtásának sorrendjét a lap betöltése előtt.
  - Az e-mail-mező mostantól `type=email` és a mobil billentyűzetek biztosítják a megfelelő javaslatokat
  - A Chrome fordításának támogatása
- Egységes és önállóan érvényesített oldal
  - A Felhasználónév/e-mail és jelszó mezők mostantól a Form HTML-elemet használják.  Ez mostantól lehetővé teszi az Edge és az IE számára az adatok megfelelő mentését

### <a name="110"></a>1.1.0

- Kivétel lap (globalexception)
  - Kisegítő lehetőségek javítása
  - Az alapértelmezett üzenet el lett távolítva, ha nincs kapcsolat a szabályzatból
  - Alapértelmezett CSS eltávolítva
- MFA-oldal (többtényezős)
  - A "kód megerősítése" gomb el lett távolítva
  - A kód bemeneti mezője csak hat (6) karakter bevitelét teszi elérhetővé.
  - Az oldal automatikusan megkísérli ellenőrizni a 6 számjegyű kód beírásakor megadott kódot, anélkül, hogy a gombokra rá kellene kattintani
  - Ha a kód nem megfelelő, a beviteli mező automatikusan törlődik
  - Ha a három (3) helytelen kóddal próbálkozik, a B2C hibát küld vissza a függő entitásnak.
  - Kisegítő lehetőségek – javítások
  - Alapértelmezett CSS eltávolítva
- Önérvényesített oldal (selfasserted)
  - Törlési riasztás eltávolítva
  - A hibás elemek CSS-osztálya
  - A hiba logikájának megjelenítése/elrejtése Továbbfejlesztve
  - Alapértelmezett CSS eltávolítva
- Unified SSP (unifiedssp)
  - A bejelentkezett (KMSI) vezérlőelem hozzáadása

### <a name="100"></a>1.0.0

- Kezdeti kiadás

## <a name="next-steps"></a>Következő lépések

További információ arról, hogyan szabhatja testre az alkalmazásai felhasználói felületét az [alkalmazás felhasználói felületének testreszabásával az Azure Active Directory B2C egyéni házirendjének használatával](custom-policy-ui-customization.md).
