---
title: Jogcímszolgáltatók – Azure Active Directory B2C | Microsoft dokumentumok
description: Adja meg az egyéni szabályzat ClaimsProvider elemét az Azure Active Directory B2C-ben.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/29/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dfb34085181e0b759d1d77485ff21b5bc59e0de3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189769"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A jogcímszolgáltató [technikai profilokat](technicalprofiles.md)tartalmaz. Minden jogcímszolgáltatónak rendelkeznie kell egy vagy több technikai profillal, amely meghatározza a jogcímszolgáltatóval való kommunikációhoz szükséges végpontokat és protokollokat. A jogcímszolgáltató több technikai profillal is rendelkezhet. Például több technikai profil is definiálható, mert a jogcímszolgáltató több protokollt támogat, különböző különböző képességekkel rendelkező végpontokat, vagy különböző jogcímeket ad ki különböző megbízhatósági szinteken. Előfordulhat, hogy egy felhasználói út során felszabadítja a bizalmas jogcímeket, de egy másikban nem.

```XML
<ClaimsProviders>
  <ClaimsProvider>
    <Domain>Domain name</Domain>
    <DisplayName>Display name</DisplayName>
    <TechnicalProfiles>
      </TechnicalProfile>
        ...
      </TechnicalProfile>
        ...
    </TechnicalProfiles>
  </ClaimsProvider>
  ...
</ClaimsProviders>
```

A **ClaimsProviders** elem a következő elemet tartalmazza:

| Elem | Események | Leírás |
| ------- | ----------- | ----------- |
| Jogcímszolgáltató | 1:n | Akkreditált jogcímszolgáltató, amely különböző felhasználói utazások során használható. |

## <a name="claimsprovider"></a>Jogcímszolgáltató

A **ClaimsProvider** elem a következő gyermekelemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ---------- | ----------- |
| Domain | 0:1 | A jogcímszolgáltató tartománynevét tartalmazó karakterlánc. Ha például a jogcímszolgáltatód tartalmazza a Facebook technikai profilját, a domain név Facebook.com. Ez a tartománynév a jogcímszolgáltatóban definiált összes technikai profilhoz használatos, kivéve, ha a technikai profil felülbírálja. A tartománynévre **domain_hint**is hivatkozhat. További információt az Azure Active Directory B2C használatával közvetlen bejelentkezés beállítása című közösségi szolgáltatóba való bejelentkezés **átirányítása** [című szakaszban talál.](direct-signin.md) |
| DisplayName | 1:1 | A jogcímszolgáltató nevét tartalmazó karakterlánc. |
| [TechnicalProfiles](technicalprofiles.md) | 0:1 | A jogcímszolgáltató által támogatott technikai profilok |

**A ClaimsProvider** rendszerezi, hogy a technikai profilok hogyan kapcsolódnak a jogcímszolgáltatóhoz. A következő példa az Azure Active Directory jogcímszolgáltatóját mutatja be az Azure Active Directory technikai profiljaival:

```XML
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AAD-Common">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      ...
    </TechnicalProfile>
      ...
    <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWritePhoneNumberUsingObjectId">
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

A következő példa a Facebook-jogcímszolgáltatót mutatja be a **Facebook-OAUTH** technikai profillal.

```XML
<ClaimsProvider>
  <Domain>facebook.com</Domain>
  <DisplayName>Facebook</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Facebook-OAUTH">
      <DisplayName>Facebook</DisplayName>
      <Protocol Name="OAuth2" />
        ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
