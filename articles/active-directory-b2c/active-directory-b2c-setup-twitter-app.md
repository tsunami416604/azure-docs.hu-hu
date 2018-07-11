---
title: Regisztráljon, és jelentkezzen be a Twitter-fiók az Azure Active Directory B2C beállítása |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés az alkalmazásokban az Azure Active Directory B2C használatával Twitter-fiókkal rendelkező ügyfelek számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: bf5ae39d83fd021775fbd18cf23d2e6b9078e748
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37927896"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Regisztráljon, és jelentkezzen be a Twitter-fiók az Azure Active Directory B2C beállítása

## <a name="create-a-twitter-application"></a>Twitter-alkalmazás létrehozása

Twitter-fiók használata Identitásszolgáltatóként az Azure Active Directory (Azure AD) B2C-ben, hozzon létre egy alkalmazást a bérlőben, hogy az azt jelölő kell. Ha még a Twitter-fiók nem rendelkezik, beszerezheti a [ https://twitter.com/signup ](https://twitter.com/signup).

1. Jelentkezzen be a [Twitter-alkalmazások](https://apps.twitter.com/) a Twitter szolgáltatásbeli hitelesítő adatokkal.
2. Válassza ki **új alkalmazás létrehozása**.
3. Adja meg a **neve**, **leírás**, és **webhely**.
4. Adja meg `https://login.microsoftonline.com/te/{tenant}/{policyId}/oauth1/authresp` a **visszahívási URL-címek**. Cserélje le **{tenant}** a bérlő nevét (például: contosob2c.onmicrosoft.com) és **{policyId}** a házirend-azonosítójú (például b2c_1_policy). Minden szabályzat, amely a Twitter-fiók használata egy visszahívási URL-címet hozzá kell adnia. Győződjön meg arról, hogy használja `b2clogin.com` helyett ` login.microsoftonline.com` Ha az alkalmazás használ.
5. Fogadja el a **fejlesztői szerződés** válassza **Twitter-alkalmazás létrehozása**.
7. Válassza ki a **kulcsok és hozzáférési tokenek** fülre.
8. Másolja az értéket a **fogyasztói kulcs** és **fogyasztói titkos kulcs**. Twitter-fiók beállítása lehetőséget Identitásszolgáltatóként, a bérlőben mindkettő van szüksége.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>A bérlő Identitásszolgáltatóként Twitter konfigurálása

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Úgy győződhet meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárt használja, hogy átvált rá az Azure Portal jobb felső sarkában. Jelölje ki előfizetői adatait, majd válassza a **Könyvtárváltás** lehetőséget. 

    ![Átváltás az Azure AD B2C-bérlőre](./media/active-directory-b2c-setup-twitter-app/switch-directories.png)

    Válassza ki a bérlőjét tartalmazó könyvtárt.

    ![Könyvtár kijelölése](./media/active-directory-b2c-setup-twitter-app/select-directory.png)

3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
5. Adjon meg egy **neve**. Adja meg például *Twitter*.
6. Válassza ki **identitásszolgáltató típusa**válassza **Twitter**, és kattintson a **OK**.
7. Válassza ki **az identitásszolgáltató beállítása** , és adja meg a fogyasztói kulcs a **ügyfél-azonosító** és a **fogyasztói titkos kulcs** számára a **titkos Ügyfélkód**.
8. Kattintson a **OK**, és kattintson a **létrehozás** kattintva mentse a Twitter-konfigurációt.