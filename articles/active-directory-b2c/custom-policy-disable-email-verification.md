---
title: Az e-mailek ellenőrzésének letiltása az egyéni házirenddel való ügyfél-regisztráció során
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan tilthatja le az e-mailek ellenőrzését az Azure Active Directory B2C-ben való ügyfél-regisztráció során.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136142"
---
# <a name="disable-email-verification-during-customer-sign-up-using-a-custom-policy-in-azure-active-directory-b2c"></a>Az e-mailek ellenőrzésének letiltása az ügyfélregisztráció során egyéni szabályzat használatával az Azure Active Directory B2C-ben

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

## <a name="prerequisites"></a>Előfeltételek

Hajtsa végre az Egyéni házirendek – Első lépések című [lépéseit.](custom-policy-get-started.md) Rendelkeznie kell egy működő egyéni szabályzat a regisztrációhoz és a bejelentkezéshez a közösségi és helyi fiókok.

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>A metaadatok hozzáadása az önérvényesítő technikai profilhoz

A **LocalAccountSignUpWithLogonEmail** technikai profil egy [önérvényesített](self-asserted-technical-profile.md), amely a regisztrációs folyamat során hívható. Az e-mail-ellenőrzés `EnforceEmailVerification` letiltásához állítsa a metaadatokat hamisértékre. Felülbírálja a LocalAccountSignUpWithLogonEmail technikai profilokat a bővítményfájlban. 

1. Nyissa meg a házirend bővítményfájlját. <em> `SocialAndLocalAccounts/` </em>Például.
1. Találd `ClaimsProviders` meg az elemet. Ha az elem nem létezik, adja hozzá.
1. Adja hozzá a következő `ClaimsProviders` jogcímszolgáltatót az elemhez:

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

## <a name="test-the-custom-policy"></a>Az egyéni házirend tesztelése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Győződjön meg arról, hogy az Azure AD-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja az Azure AD-bérlőt tartalmazó könyvtárat.
3. Válassza az **Összes szolgáltatás lehetőséget** az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Alkalmazásregisztrációk lehetőséget.**
4. Válassza **az Identitáskezelési keretrendszert**.
5. Válassza **az Egyéni házirend feltöltése**lehetőséget, majd töltse fel a két módosított házirendfájlt.
2. Jelölje ki a feltöltött regisztrációs vagy bejelentkezési szabályzatot, és kattintson a **Futtatás gombra.**
3. Az ellenőrzés nélkül e-mail címmel regisztrálhat.


## <a name="next-steps"></a>További lépések

- További információ az [önérvényesítő technikai profilról](self-asserted-technical-profile.md) az IEF-hivatkozásban.
