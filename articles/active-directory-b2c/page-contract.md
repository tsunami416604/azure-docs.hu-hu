---
title: Válassza ki a lapon szerződést – Azure Active Directory B2C |} A Microsoft Docs
description: Útmutató az Azure Active Directory B2C lap szerződés kiválasztása.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 4cd29df19179f07fd9b61a2f484b1d49cc05c4cf
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64570576"
---
# <a name="select-a-page-contract-in-azure-active-directory-b2c-using-custom-policies"></a>Válassza ki a lap szerződés az Azure Active Directory B2C-vel egyéni szabályzatok használatával

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Ügyféloldali JavaScript-kódot engedélyezheti az Azure Active Directory (Azure AD) B2C-szabályzatok, legyen szó a felhasználói folyamatok vagy egyéni szabályzatokkal. JavaScript engedélyezése az alkalmazások számára, hozzá kell adnia egy elem a [egyéni házirendet](active-directory-b2c-overview-custom.md), válassza ki a lap szerződést, és használja [b2clogin.com](b2clogin.md) a kérelmek a. Egy lap szerződés álló elemeket, amelyeket az Azure AD B2C biztosít, és az Ön által megadott tartalom. Ez a cikk leírja, hogyan lap szerződés kiválasztása az Azure AD B2C egy egyéni házirendek konfigurálásával.

> [!NOTE]
> Ha a felhasználói folyamatok JavaScript engedélyezni szeretné, tekintse meg [JavaScript és a lap szerződés az Azure Active Directory B2C verziók](user-flow-javascript-overview.md).

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



