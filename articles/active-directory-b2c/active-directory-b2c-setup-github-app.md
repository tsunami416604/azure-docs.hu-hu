---
title: "GitHub-identitás szolgáltató konfigurálása - az Azure AD B2C |} Microsoft Docs"
description: "Adja meg a regisztráció és bejelentkezés az Azure Active Directory B2C által védett alkalmazások GitHub fiókkal rendelkező felhasználók számára."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 5518e135-36b3-4f86-9a01-be25f021ed22
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: parja
ms.openlocfilehash: e827bea257309f6d6dda1af4e68ccda5a26d30ef
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-github-accounts"></a>Az Azure Active Directory B2C: Regisztráció és bejelentkezés biztosítása a GitHub-fiókkal rendelkező felhasználók

> [!NOTE]
> A funkció jelenleg előzetes verzió.
> 

Ez a cikk bemutatja, hogyan engedélyezése bejelentkezés GitHub-fiók a felhasználók számára.

## <a name="create-a-github-oauth-application"></a>GitHub OAuth-alkalmazás létrehozása

GitHub használatára az Azure AD B2C identitás-szolgáltatóként, meg kell GitHub OAuth-alkalmazás létrehozása, és adja meg azt a megfelelő paraméterekkel.

1. Lépjen a [GitHub fejlesztői beállítások](https://github.com/settings/developers) Miután bejelentkezett a Githubon.
1. Kattintson a **új OAuth-alkalmazás**
1. Adjon meg egy **alkalmazásnév** és a **kezdőlap URL-cím**.
1. Az a **engedélyezési visszahívási URL-címet**, adja meg `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Cserélje le **{tenant}** az Azure AD B2C-bérlő nevű (például contosob2c.onmicrosoft.com).

    >[!NOTE]
    >Az értéknek "bérlő" kell lennie a kisbetűket a **bejelentkezési URL-cím**.

1. Kattintson a **alkalmazás regisztrálása**.
1. Mentés értékének **ügyfél-azonosító** és **Ügyfélkulcs**. Szüksége lesz a következő szakaszban is.

## <a name="configure-github-as-an-identity-provider-in-your-azure-ad-b2c-tenant"></a>GitHub konfigurálása az Azure AD B2C-bérlőben identitás-szolgáltatóként

1. Az alábbi lépéseket követve [lépjen a B2C funkciók panelje](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure portálon.
1. Kattintson a B2C funkciók panelje **identitás-szolgáltatóktól**.
1. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
1. Adjon meg egy rövid **neve** a az identitás-szolgáltató konfigurációját. Írja be például a "GitHub".
1. Kattintson a **identitás szolgáltatótípus**, jelölje be **GitHub**, és kattintson a **OK**.
1. Kattintson a **az identitásszolgáltató beállítása** , és adja meg az ügyfél-azonosító és a korábban kimásolt GitHub OAuth alkalmazás titkos ügyfélkulcsot.
1. Kattintson a **OK** majd **létrehozása** a GitHub-konfiguráció mentéséhez.

## <a name="next-steps"></a>További lépések

Hozzon létre vagy szerkessze a [beépített házirend](active-directory-b2c-reference-policies.md) , és adja hozzá a Githubon identitás-szolgáltatóként.