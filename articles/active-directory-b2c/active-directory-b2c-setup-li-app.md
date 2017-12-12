---
title: "Az Azure Active Directory B2C: LinkedIn konfigurációs |} Microsoft Docs"
description: "Regisztráció és bejelentkezés adhat az Azure Active Directory B2C által védett alkalmazások LinkedIn fiókkal rendelkező felhasználók"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mtillman
editor: bryanla
ms.assetid: fa51a16b-9ce9-4e27-9eff-0869b4c4f0ef
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 860aa90c391604924850a00cf2137d59fa4a1b53
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-linkedin-accounts"></a>Az Azure Active Directory B2C: Regisztráció és bejelentkezés adhat LinkedIn-fiókkal rendelkező felhasználók
## <a name="create-a-linkedin-application"></a>LinkedIn-alkalmazás létrehozása
Az Azure Active Directory (Azure AD) B2C identitás-szolgáltatóként LinkedIn használatához szüksége LinkedIn-alkalmazás létrehozása, és adja meg azt a megfelelő paraméterekkel. Ehhez LinkedIn-fiók szükséges. Ha még nincs fiókja, beszerezheti a [https://www.linkedin.com/](https://www.linkedin.com/).

1. Lépjen a [LinkedIn fejlesztők webhely](https://www.developer.linkedin.com/) és jelentkezzen be a LinkedIn-fiók hitelesítő adataival.
2. Kattintson a **saját alkalmazások** a felső menüsávban majd **alkalmazás létrehozása**.
   
    ![LinkedIn - új alkalmazás](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)
3. Az a **hozzon létre egy új alkalmazást** alkotnak, töltse ki a megfelelő információkat (**vállalatnév**, **neve**, **leírás**, **embléma URL-címe**, **alkalmazás használata**, **webhely URL-címe**, **üzleti E-mail** és **munkahelyi telefon**).
4. Vállalja, hogy a **LinkedIn API használati** kattintson **Submit**.
   
    ![LinkedIn - alkalmazás regisztrálása](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)
5. Értékeinek másolására **ügyfél-azonosító** és **Ügyfélkulcs**. (Megtalálja azokat a **hitelesítési kulcsokat**.) Mindkettő konfigurálásához LinkedIn-bérlőben identitás-szolgáltatóként kell.
   
   > [!NOTE]
   > **Titkos Ügyfélkulcsot** van egy fontos biztonsági hitelesítő adatok.
   > 
   > 
6. Adja meg `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` a a **átirányítási URL-t engedélyezett** mező (alatt **OAuth 2.0**). Cserélje le **{tenant}** a bérlő nevű (például contoso.onmicrosoft.com). Kattintson a **Hozzáadás**, és kattintson a **frissítés**. A **{tenant}** érték kis-és nagybetűket.
   
    ![LinkedIn - telepítő alkalmazás](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## <a name="configure-linkedin-as-an-identity-provider-in-your-tenant"></a>A bérlő az identitás-szolgáltatóként LinkedIn konfigurálása
1. Az alábbi lépéseket követve [lépjen a B2C funkciók panelje](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure portálon.
2. Kattintson a B2C funkciók panelje **identitás-szolgáltatóktól**.
3. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
4. Adjon meg egy rövid **neve** a az identitás-szolgáltató konfigurációját. Írja be például a "LI".
5. Kattintson a **identitás szolgáltatótípus**, jelölje be **LinkedIn**, és kattintson a **OK**.
6. Kattintson a **az identitásszolgáltató beállítása** , és adja meg az ügyfél-azonosító és a korábban létrehozott LinkedIn alkalmazás titkos ügyfélkulcsot.
7. Kattintson a **OK** majd **létrehozása** a LinkedIn-konfiguráció mentéséhez.

