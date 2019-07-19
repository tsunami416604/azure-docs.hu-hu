---
title: Lapelrendezés kiválasztása – Azure Active Directory B2C
description: További információ az oldalelrendezés kiválasztásáról a Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 011fb262ff91c56269c5b7dc9adf4aaeab9acbd5
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229049"
---
# <a name="select-a-page-layout-in-azure-active-directory-b2c-using-custom-policies"></a>Lapelrendezés kiválasztása Azure Active Directory B2C egyéni házirendek használatával

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Engedélyezheti a JavaScript ügyféloldali kódot a Azure Active Directory (Azure AD) B2C-szabályzatokban, függetlenül attól, hogy felhasználói folyamatokat vagy egyéni házirendeket használ. Az alkalmazásokhoz való JavaScript engedélyezéséhez hozzá kell adnia egy elemet az [Egyéni szabályzathoz](active-directory-b2c-overview-custom.md), ki kell választania egy lapelrendezést, és a [b2clogin.com](b2clogin.md) kell használnia a kérésekben.

Az oldalelrendezés olyan elemek társítása, amelyeket Azure AD B2C biztosít, valamint az Ön által megadott tartalmat.

Ez a cikk azt ismerteti, hogyan lehet kijelölni egy lapelrendezést a Azure AD B2Cban úgy, hogy egy egyéni szabályzatban konfigurálja azt.

> [!NOTE]
> Ha engedélyezni szeretné a JavaScriptet a felhasználói folyamatokhoz, tekintse [meg a JavaScript-és lapelrendezés-verziókat a Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="replace-datauri-values"></a>DataUri értékek lecserélése

A egyéni szabályzatait, szükség lehet [ContentDefinitions](contentdefinitions.md) , hogy a használt a felhasználói interakciósorozatban szereplő HTML-sablonok meghatározása. A **ContentDefinition** tartalmaz egy **DataUri** hivatkozik, amely az Azure AD B2C által biztosított elemei. A **LoadUri** a HTML és CSS tartalmat, Ön által megadott relatív elérési útját.

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

Az oldalelrendezés kiválasztásához módosítsa a [ContentDefinitions](contentdefinitions.md) lévő **DataUri** értékeket a szabályzatokban. A régi váltásával **DataUri** értékek új értékeivel, nem módosítható csomagot választjuk. Ez a csomag használatára előnye, hogy tudni fogja, nem módosíthatja és nem várt viselkedést okozhat az oldalon.

Az **DataUri** beállításához használja a következő táblázatot a kívánt értékek megkereséséhez.

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

## <a name="next-steps"></a>További lépések

További információ az alkalmazások felhasználói felületének testreszabását [egyéni szabályzat használata az Azure Active Directory B2C az alkalmazás a felhasználói felület testreszabása](active-directory-b2c-ui-customization-custom.md).
