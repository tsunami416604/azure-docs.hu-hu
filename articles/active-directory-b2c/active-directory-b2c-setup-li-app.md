---
title: LinkedIn-konfiguráció az Azure Active Directory B2C |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés a az Azure Active Directory B2C által védett alkalmazások a LinkedIn-fiókkal rendelkező felhasználók számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7588711bd1c2a02e2e9a100d2ba182f43e7df488
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446089"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-linkedin-accounts"></a>Az Azure Active Directory B2C: Adja meg előfizetési, és jelentkezzen be a LinkedIn-fiókkal rendelkező felhasználók számára
## <a name="create-a-linkedin-application"></a>A LinkedIn-alkalmazás létrehozása
LinkedIn használata Identitásszolgáltatóként az Azure Active Directory (Azure AD) B2C-ben, meg kell a LinkedIn-alkalmazás létrehozása, és adja meg azt a megfelelő paraméterekkel. Ehhez a LinkedIn-fiók szükséges. Ha még nincs fiókja, beszerezheti a [ https://www.linkedin.com/ ](https://www.linkedin.com/).

1. Nyissa meg a [LinkedIn fejlesztők webhely](https://www.developer.linkedin.com/) , és jelentkezzen be a LinkedIn-fiók hitelesítő adatait.
2. Kattintson a **saját alkalmazások** a felső menüsávon, és kattintson a **alkalmazás létrehozása**.
   
    ![LinkedIn - új alkalmazás](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)
3. Az a **hozzon létre egy új alkalmazást** alkotnak, töltse ki a megfelelő információkat (**cégnév**, **neve**, **leírás**, **Alkalmazás embléma URL-Címének**, **alkalmazások által használható**, **webhely URL-címe**, **üzleti E-mail** és **Munkahelyitelefon**).
4. Fogadja el a **LinkedIn API használati** kattintson **küldés**.
   
    ![LinkedIn - alkalmazás regisztrálása](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)
5. Másolja le az értékeket a **ügyfél-azonosító** és **titkos Ügyfélkód**. (Alatt megtalálja **hitelesítési kulcsok**.) Szüksége lesz mindkettő LinkedIn konfigurálása identitás-szolgáltatóként a bérlőben.
   
   > [!NOTE]
   > **Titkos Ügyfélkód** egy fontos biztonsági hitelesítő adat.
   > 
   > 
6. Adja meg `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` a a **átirányítási URL-címek engedélyezett** mező (alatt **OAuth 2.0**). Cserélje le **{tenant}** a bérlő nevét (például contoso.onmicrosoft.com). Kattintson a **Hozzáadás**, és kattintson a **frissítés**. A **{tenant}** érték kisbetűssé kell lennie.
   
    ![LinkedIn - telepítő alkalmazás](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## <a name="configure-linkedin-as-an-identity-provider-in-your-tenant"></a>A bérlő Identitásszolgáltatóként, LinkedIn konfigurálása
1. Az alábbi lépéseket követve [lépjen a B2C funkciók paneljére](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure Portalon.
2. Kattintson a B2C funkciók panelje **Identitásszolgáltatók**.
3. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
4. Adjon meg egy rövid **neve** az identitás szolgáltató a konfigurációhoz. Adja meg például a "LI".
5. Kattintson a **identitásszolgáltató típusa**válassza **LinkedIn**, és kattintson a **OK**.
6. Kattintson a **az identitásszolgáltató beállítása** , és adja meg az ügyfél-azonosító és titkos ügyfélkód a LinkedIn-alkalmazás, amelyet korábban hozott létre.
7. Kattintson a **OK** majd **létrehozás** a LinkedIn-konfiguráció mentéséhez.

