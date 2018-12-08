---
title: Válassza ki a lapon szerződést az Azure Active Directory B2C |} A Microsoft Docs
description: Útmutató az Azure Active Directory B2C lap szerződés kiválasztása.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e2e6da5df434ffd217b0521d4a13cd8ec713d5a1
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53109740"
---
# <a name="select-a-page-contract-in-azure-active-directory-b2c-using-custom-policies"></a>Válassza ki a lap szerződés az Azure Active Directory B2C-vel egyéni szabályzatok használatával

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Kiválaszthatja egy oldal szerződés az Azure Active Directory (Azure AD) B2C konfigurálásával, az egy [egyéni szabályzatok](active-directory-b2c-overview-custom.md). Egy lap szerződés egy assocation elemeket, amelyeket az Azure AD B2C biztosít, és az Ön által megadott tartalom. Ha a használni kívánt [Javascript](javascript-samples.md), meg kell határoznia egy oldal szerződés verziót a tartalom-definíciók minden az egyéni házirendek.

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

Oldal szerződés kiválasztása módosítja a **DataUri** lévő értékeknek a [ContentDefinitions](contentdefinitions.md) azokban a házirendekben. A régi váltásával **DataUri** értékek új értékeivel, nem módosítható csomagot választjuk. Ez a csomag használatára előnye, hogy tudni fogja, nem módosíthatja és nem várt viselkedést okozhat az oldalon. 

Egy lap szerződés, használja a következő táblázatban található **DataUri** értékeket. 

| Régi DataUri érték | Új DataUri érték |
| ----------------- | ----------------- |
| urn: com:microsoft:aad:b2c:elements:idpselection:1.0.0 | urn: com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0 |
| urn: com:microsoft:aad:b2c:elements:unifiedssd:1.0.0 | urn: com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0 | 
| urn: com:microsoft:aad:b2c:elements:claimsconsent:1.0.0 | urn: com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0 |
| urn: com:microsoft:aad:b2c:elements:multifactor:1.0.0 | urn: com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0 |
| urn: com:microsoft:aad:b2c:elements:multifactor:1.1.0 | urn: com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0 |
| urn: com:microsoft:aad:b2c:elements:selfasserted:1.0.0 | urn: com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0 |
| urn: com:microsoft:aad:b2c:elements:selfasserted:1.1.0 | urn: com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0 | 
| urn: com:microsoft:aad:b2c:elements:unifiedssp:1.0.0 | urn: com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0 |
| urn: com:microsoft:aad:b2c:elements:unifiedssp:1.1.0 | urn: com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0 |
| urn: com:microsoft:aad:b2c:elements:globalexception:1.0.0 | urn: com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0 |
| urn: com:microsoft:aad:b2c:elements:globalexception:1.1.0 | urn: com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0 |

## <a name="next-steps"></a>További lépések

További információ az alkalmazások felhasználói felületének testreszabását [egyéni szabályzat használata az Azure Active Directory B2C az alkalmazás a felhasználói felület testreszabása](active-directory-b2c-ui-customization-custom.md).



