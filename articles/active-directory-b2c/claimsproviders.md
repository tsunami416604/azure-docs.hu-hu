---
title: ClaimsProviders – Azure Active Directory B2C | Microsoft Docs
description: A Azure Active Directory B2C egyéni házirendjének ClaimsProvider elemének megadásához.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/29/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0bda94344a22f39daf104e7b95e33723187c4aa1
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76983010"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A jogcím-szolgáltató [technikai profilokat](technicalprofiles.md)tartalmaz. Minden jogcím-szolgáltatónak rendelkeznie kell egy vagy több olyan technikai profillal, amely meghatározza a végpontokat és a jogcím-szolgáltatóval való kommunikációhoz szükséges protokollokat. A jogcím-szolgáltató több technikai profillal is rendelkezhet. Például több technikai profil is definiálható, mert a jogcím-szolgáltató több protokollt támogat, különböző képességekkel rendelkező végpontokat, illetve különböző jogcímeket különböző megbízhatósági szinteken szabadít fel. Lehetséges, hogy a bizalmas jogcímeket egy felhasználói úton kell kibocsátani, de nem egy másikban.

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
| ClaimsProvider | 1: n | Egy akkreditált jogcím-szolgáltató, amely különböző felhasználói útvonalakon is kihasználható. |

## <a name="claimsprovider"></a>ClaimsProvider

A **ClaimsProvider** elem a következő alárendelt elemeket tartalmazza:

| Elem | Események | Leírás |
| ------- | ---------- | ----------- |
| Domain | 0:1 | A jogcím-szolgáltató tartománynevét tartalmazó karakterlánc. Ha például a jogcímek szolgáltatója tartalmazza a Facebook technikai profilt, a tartománynév Facebook.com. Ezt a tartománynevet a rendszer a jogcím-szolgáltatóban definiált összes műszaki profilhoz használja, kivéve ha a technikai profil felülbírálja. A tartománynevet **domain_hint**is lehet hivatkozni. További információkért tekintse [meg a közvetlen bejelentkezés beállítása a Azure Active Directory B2C használatával](direct-signin.md)című témakör **átirányítása a közösségi szolgáltatónak** című szakaszát. |
| displayName | 1:1 | A jogcím-szolgáltató nevét tartalmazó karakterlánc. |
| [TechnicalProfiles](technicalprofiles.md) | 0:1 | A jogcím-szolgáltató által támogatott technikai profilok készlete |

A **ClaimsProvider** szervezi, hogy a technikai profilok hogyan kapcsolódnak a jogcím-szolgáltatóhoz. A következő példa a Azure Active Directory jogcím-szolgáltatót mutatja be a Azure Active Directory technikai profiljaival:

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

A következő példa a Facebook **-OAUTH** technikai profillal rendelkező Facebook-jogcímek szolgáltatóját mutatja be.

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
