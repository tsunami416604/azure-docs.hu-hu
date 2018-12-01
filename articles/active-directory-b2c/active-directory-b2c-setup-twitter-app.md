---
title: Regisztráljon, és jelentkezzen be a Twitter-fiók az Azure Active Directory B2C beállítása |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés az alkalmazásokban az Azure Active Directory B2C használatával Twitter-fiókkal rendelkező ügyfelek számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: f0f0b8e0cbb5fbab81a07a28a9d4a2c264be6545
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52719861"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Regisztráljon, és jelentkezzen be a Twitter-fiók az Azure Active Directory B2C beállítása

## <a name="create-an-application"></a>Alkalmazás létrehozása

Twitter használata Identitásszolgáltatóként az Azure AD B2C-ben, meg kell Twitter-alkalmazás létrehozására. Ha még a Twitter-fiók nem rendelkezik, beszerezheti a [ https://twitter.com/signup ](https://twitter.com/signup).

1. Jelentkezzen be a [Twitter-fejlesztők](https://developer.twitter.com/en/apps) webhely Twitter-fiókja hitelesítő adataival.
2. Válassza ki **hozzon létre egy alkalmazást**.
3. Adjon meg egy **alkalmazásnév** és a egy **alkalmazásleírás**.
4. A **webhely URL-címe**, adja meg `https://your-tenant.b2clogin.com`. Cserélje le `your-tenant` a bérlő nevével. Például: https://contosob2c.b2clogin.com.
5. Az a **visszahívási URL-Címének**, adja meg `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`. Cserélje le `your-tenant` a bérlő neve nevével és `your-user-flow-Id` a felhasználói folyamat azonosítóval. Például: `b2c_1A_signup_signin_twitter`. Kisbetűk használhatók, ha akkor is, ha a bérlő Azure AD B2C-ben nagybetűk van definiálva, írja be a bérlő nevét kell.
6. A lap alján, olvassa el és fogadja el a feltételeket, és válassza ki **létrehozás**.
7. A a **alkalmazás adatait** lapon jelölje be **szerkesztése > Részletek szerkesztése**, jelölje be a **engedélyezéséhez jelentkezzen be Twitter**, majd válassza ki **mentése**.
8. Válassza ki **kulcsok vagy tokenek** , és jegyezze fel a **fogyasztói API-kulcs** és a **Consumer API-k titkos kulcs** értékeket későbbi felhasználás céljából.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>A bérlő Identitásszolgáltatóként Twitter konfigurálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
5. Adjon meg egy **neve**. Adja meg például *Twitter*.
6. Válassza ki **identitásszolgáltató típusa**válassza **Twitter**, és kattintson a **OK**.
7. Válassza ki **az identitásszolgáltató beállítása** , és adja meg az API-kulcs a **ügyfél-azonosító** és API-t a titkos kulcsot a **titkos Ügyfélkód**.
8. Kattintson a **OK**, és kattintson a **létrehozás** kattintva mentse a Twitter-konfigurációt.