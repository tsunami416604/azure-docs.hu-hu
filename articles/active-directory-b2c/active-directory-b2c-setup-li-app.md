---
title: Az Azure Active Directory B2C LinkedIn konfigurációs |} Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés az Azure Active Directory B2C által védett alkalmazások LinkedIn fiókkal rendelkező felhasználók számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6db3832031a1bb960ee40c0e4fb8c3d0591a976c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711698"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-linkedin-accounts"></a>Az Azure Active Directory B2C: Regisztráció és bejelentkezés adhat LinkedIn-fiókkal rendelkező felhasználók
## <a name="create-a-linkedin-application"></a>LinkedIn-alkalmazás létrehozása
Az Azure Active Directory (Azure AD) B2C identitás-szolgáltatóként LinkedIn használatához szüksége LinkedIn-alkalmazás létrehozása, és adja meg azt a megfelelő paraméterekkel. Ehhez LinkedIn-fiók szükséges. Ha még nincs fiókja, beszerezheti a [ https://www.linkedin.com/ ](https://www.linkedin.com/).

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
6. Adja meg `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` a a **átirányítási URL-t engedélyezett** mező (alatt **OAuth 2.0**). Cserélje le **{tenant}** a bérlő nevű (például contoso.onmicrosoft.com). Kattintson a **Hozzáadás**, és kattintson a **frissítés**. A **{tenant}** érték kisbetűnek kell lennie.
   
    ![LinkedIn - telepítő alkalmazás](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## <a name="configure-linkedin-as-an-identity-provider-in-your-tenant"></a>A bérlő az identitás-szolgáltatóként LinkedIn konfigurálása
1. Az alábbi lépéseket követve [lépjen a B2C funkciók panelje](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure portálon.
2. Kattintson a B2C funkciók panelje **identitás-szolgáltatóktól**.
3. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
4. Adjon meg egy rövid **neve** a az identitás-szolgáltató konfigurációját. Írja be például a "LI".
5. Kattintson a **identitás szolgáltatótípus**, jelölje be **LinkedIn**, és kattintson a **OK**.
6. Kattintson a **az identitásszolgáltató beállítása** , és adja meg az ügyfél-azonosító és a korábban létrehozott LinkedIn alkalmazás titkos ügyfélkulcsot.
7. Kattintson a **OK** majd **létrehozása** a LinkedIn-konfiguráció mentéséhez.

