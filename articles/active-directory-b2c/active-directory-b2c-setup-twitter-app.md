---
title: Regisztráljon, és jelentkezzen be a Twitter-fiók az Azure Active Directory B2C beállítása |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés az alkalmazásokban az Azure Active Directory B2C használatával Twitter-fiókkal rendelkező ügyfelek számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5732293510a75a3c40df5cf3d31978c5ce599791
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44720157"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Regisztráljon, és jelentkezzen be a Twitter-fiók az Azure Active Directory B2C beállítása

## <a name="create-a-twitter-application"></a>Twitter-alkalmazás létrehozása

Twitter-fiók használata Identitásszolgáltatóként az Azure Active Directory (Azure AD) B2C-ben, hozzon létre egy alkalmazást a bérlőben, hogy az azt jelölő kell. Ha még a Twitter-fiók nem rendelkezik, beszerezheti a [ https://twitter.com/signup ](https://twitter.com/signup).

1. Jelentkezzen be a [Twitter-alkalmazások](https://apps.twitter.com/) a Twitter szolgáltatásbeli hitelesítő adatokkal.
2. Válassza ki **hozzon létre egy alkalmazást**.
3. Adja meg a **alkalmazásnév**, **alkalmazásleírás**, és **webhely URL-címe**.
4. Adja meg `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy-name/oauth1/authresp` a **visszahívási URL-címek**. Cserélje le `your-tenant-name` a bérlő nevével és `your-policy-name` a szabályzat nevét. Például: `b2c_1_signupsignin`. Kisbetűk használhatók megadásakor a bérlő nevét és a szabályzat neve akkor is, ha azokat az Azure AD B2C-ben nagybetűk definiálva van szüksége.
5. Fogadja el a **fejlesztői szerződés** válassza **létrehozás**.
7. Válassza ki a **kulcsok és hozzáférési tokenek** fülre.
8. Másolja le az értékeket a **API-kulcs** és **API titkos kulcs**. Twitter-fiók beállítása lehetőséget Identitásszolgáltatóként, a bérlőben mindkettő van szüksége.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>A bérlő Identitásszolgáltatóként Twitter konfigurálása

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.  

    ![Átváltás az Azure AD B2C-bérlőre](./media/active-directory-b2c-setup-twitter-app/switch-directories.png)

3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
5. Adjon meg egy **neve**. Adja meg például *Twitter*.
6. Válassza ki **identitásszolgáltató típusa**válassza **Twitter**, és kattintson a **OK**.
7. Válassza ki **az identitásszolgáltató beállítása** , és adja meg az API-kulcs a **ügyfél-azonosító** és API-t a titkos kulcsot a **titkos Ügyfélkód**.
8. Kattintson a **OK**, és kattintson a **létrehozás** kattintva mentse a Twitter-konfigurációt.