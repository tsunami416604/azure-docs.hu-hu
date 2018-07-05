---
title: GitHub identitás szolgáltató konfigurálása az Azure Active Directory B2C |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés GitHub-fiókjukban az alkalmazások Azure Active Directory B2C által védett rendelkező ügyfelek számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/06/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 3754a169b301bac97f3e12d10b754222e3cf325d
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443341"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-github-accounts"></a>Az Azure Active Directory B2C: Adja meg előfizetési, és jelentkezzen be a GitHub-fiókkal rendelkező felhasználók számára

> [!NOTE]
> Ez a funkció előzetes verzióban érhető el.
> 

Ez a cikk bemutatja, hogyan ahhoz, hogy jelentkezzen be a GitHub-fiókkal rendelkező felhasználók számára.

## <a name="create-a-github-oauth-application"></a>Egy GitHub-OAuth-alkalmazás létrehozása

Identitás-szolgáltatóként az Azure AD B2C GitHub használatához kell egy GitHub-OAuth-alkalmazás létrehozása, és adja meg azt a megfelelő paraméterekkel.

1. Nyissa meg a [GitHub fejlesztői beállítások](https://github.com/settings/developers) után GitHub-ba történő bejelentkezéskor.
1. Kattintson a **új OAuth-alkalmazás**
1. Adjon meg egy **alkalmazásnév** és a **kezdőlap URL-címe**.
1. Az a **engedélyezési visszahívási URL-Címének**, adja meg `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Cserélje le **{tenant}** együtt az Azure AD B2C-bérlő neve (például: contosob2c.onmicrosoft.com).

    >[!NOTE]
    >"Tenant" értéke csak kisbetűket a a **bejelentkezési URL-**.

1. Kattintson a **alkalmazás regisztrálása**.
1. Mentse az értékeket a **ügyfél-azonosító** és **titkos Ügyfélkód**. Ez a szakasz a is kell.

## <a name="configure-github-as-an-identity-provider-in-your-azure-ad-b2c-tenant"></a>Konfigurálja a GitHub identitásszolgáltatójaként az Azure AD B2C-bérlőben

1. Az alábbi lépéseket követve [lépjen a B2C funkciók paneljére](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure Portalon.
1. Kattintson a B2C funkciók panelje **Identitásszolgáltatók**.
1. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
1. Adjon meg egy rövid **neve** az identitás szolgáltató a konfigurációhoz. Adja meg például a "GitHub".
1. Kattintson a **identitásszolgáltató típusa**válassza **GitHub**, és kattintson a **OK**.
1. Kattintson a **az identitásszolgáltató beállítása** , és adja meg az ügyfél-azonosító és titkos ügyfélkód a GitHub OAuth-alkalmazás, amely korábban vágólapra másolt.
1. Kattintson a **OK** majd **létrehozás** a GitHub-konfiguráció mentéséhez.

## <a name="next-steps"></a>További lépések

Hozzon létre vagy szerkessze a [beépített szabályzat](active-directory-b2c-reference-policies.md) , és adja hozzá a GitHub identitás-szolgáltatóként.