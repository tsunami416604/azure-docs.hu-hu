---
title: E-mail-ellenőrzés letiltása egyéni szabályzattal rendelkező ügyfél-regisztráció során
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan tilthatja le az e-mailek ellenőrzését a Azure Active Directory B2C az ügyfelek regisztrációja során.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 13a5fa6a030d876d92651ca587e37fdc6a3ec600
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79136142"
---
# <a name="disable-email-verification-during-customer-sign-up-using-a-custom-policy-in-azure-active-directory-b2c"></a>Az e-mailek ellenőrzésének letiltása az ügyfél-regisztráció során a Azure Active Directory B2C egyéni szabályzatának használatával

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre az [Ismerkedés az egyéni szabályzatokkal](custom-policy-get-started.md)című témakör lépéseit. A közösségi és helyi fiókokkal való bejelentkezéshez és a bejelentkezéshez egy működő egyéni szabályzatot kell használnia.

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Metaadatok hozzáadása az önérvényesített technikai profilhoz

A **LocalAccountSignUpWithLogonEmail** technikai profil egy [önérvényesített](self-asserted-technical-profile.md), amelyet a rendszer a regisztrálási folyamat során hív meg. Az e-mail-ellenőrzés letiltásához állítsa a `EnforceEmailVerification` metaadatokat hamis értékre. Bírálja felül a LocalAccountSignUpWithLogonEmail technikai profiljait. 

1. Nyissa meg a szabályzat Extensions (bővítmények) fájlját. Például <em> `SocialAndLocalAccounts/` </em>:.
1. Keresse meg `ClaimsProviders` az elemet. Ha az elem nem létezik, adja hozzá.
1. Adja hozzá a következő jogcím-szolgáltatót a `ClaimsProviders` elemhez:

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="test-the-custom-policy"></a>Egyéni szabályzat tesztelése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy az Azure AD-bérlőt tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki az Azure ad-bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Alkalmazásregisztrációk**.
4. Válassza az **identitási élmény keretrendszert**.
5. Válassza az **egyéni házirend feltöltése**lehetőséget, majd töltse fel a módosított két házirendet.
2. Válassza ki a feltöltött regisztrációs vagy bejelentkezési szabályzatot, majd kattintson a **Futtatás most** gombra.
3. Az érvényesítés nélkül egy e-mail-cím használatával regisztrálhat.


## <a name="next-steps"></a>További lépések

- További információ az [önérvényesített technikai profilról](self-asserted-technical-profile.md) a IEF-referenciában.
