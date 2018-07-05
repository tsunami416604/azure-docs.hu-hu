---
title: Facebook-konfiguráció az Azure Active Directory B2C |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés a az Azure Active Directory B2C által védett alkalmazások Facebook-fiókkal rendelkező felhasználók számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 8/7/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 316e44ea92a25ab804c8cc499f91c45e4a66ef02
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445500"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>Az Azure Active Directory B2C: Adja meg előfizetési, és jelentkezzen be a Facebook-fiókkal rendelkező felhasználók számára
## <a name="create-a-facebook-application"></a>Egy Facebook-alkalmazás létrehozása
Használja a Facebookot identitásszolgáltatójaként az Azure Active Directory (Azure AD) B2C-vel, meg kell hozzon létre egy Facebook-alkalmazást, és adja meg azt a megfelelő paraméterekkel. Ehhez egy Facebook-fiók szükséges. Ha még nincs fiókja, beszerezheti a [ https://www.facebook.com/ ](https://www.facebook.com/).

1. Nyissa meg a [Facebook-fejlesztőknek](https://developers.facebook.com/) webhelyet, és jelentkezzen be a Facebook-fiók hitelesítő adatait.
2. Ha még nem tette meg, hogy Facebook-fejlesztőként regisztrálnia kell. Ehhez kattintson **regisztrálása** (a az oldal jobb felső sarokban), fogadja el a Facebook-szabályzatokat, és végezze el a regisztrációs lépéseket.
3. Kattintson a **saját alkalmazások** majd **adjon hozzá egy új alkalmazást**. 
4. A képernyőn adjon meg egy **megjelenítendő név** és a egy érvényes **kapcsolattartó E-mail**.
5. Kattintson a **Alkalmazásazonosító létrehozása**. Ez előfordulhat, hogy fogadja el a Facebook-platform házirendek és a egy online biztonsági ellenőrzés.
6. A bal oldali oszlopban kattintson **beállítások** majd **alapszintű** Ha nincs bejelölve.
7. Válassza ki a **kategória**. 
8. Kattintson a **+ Hozzáadás Platform** válassza **webhely**.
   
    ![Facebook - beállítások](./media/active-directory-b2c-setup-fb-app/fb-settings.png)
   
    ![Facebook - Settings - webhely](./media/active-directory-b2c-setup-fb-app/fb-website.png)
9. Adja meg `https://login.microsoftonline.com/` a a **webhely URL-címe** mezőbe, majd kattintson **módosítások mentése** az oldal alján.
   
    ![Facebook - webhely URL-címe](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

10. Másolja az értéket a **Alkalmazásazonosító**. Kattintson a **megjelenítése** , és másolja az értéket a **titkos Alkalmazáskulcs**. Szüksége lesz mindkettő Facebook konfigurálása identitás-szolgáltatóként a bérlőben. **Alkalmazás titkos kulcsát** egy fontos biztonsági hitelesítő adat.
   
    ![Facebook - azonosító és titkos Alkalmazáskulcs](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)
11. Kattintson a **+ termék hozzáadása** a bal oldali navigációs, majd a **Set Up** gombot **Facebook-bejelentkezés**.
   
    ![Facebook - Facebook-bejelentkezés](./media/active-directory-b2c-setup-fb-app/fb-login.png)
12. Kattintson a **beállítások** a jobb oldali navigációs területen a **Facebook-bejelentkezés**

    ![Facebook - Facebook-bejelentkezés beállításai](./media/active-directory-b2c-setup-fb-app/fb-login-settings.png)
13. Adja meg `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` a a **érvényes OAuth átirányítási URI-k** mezőbe a **OAuth ügyfélbeállítások** szakaszban. Cserélje le **{tenant}** a bérlő nevét (például: contosob2c.onmicrosoft.com). Kattintson a **módosítások mentése** az oldal alján.
    
    ![Facebook - OAuth átirányítási URI-ja](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)
14. Ahhoz, hogy a Facebook-alkalmazás Azure AD B2C által használható, kell nyilvánosan elérhető legyen. Ezt megteheti kattintva is **alkalmazás áttekintése** a bal oldali navigációs és a kapcsoló a lap tetején lévő bekapcsolásával **Igen** kattintva **megerősítése**.
    
    ![Facebook - alkalmazás nyilvános](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>A bérlő Identitásszolgáltatóként Facebook konfigurálása
1. Az alábbi lépéseket követve [lépjen a B2C funkciók paneljére](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure Portalon.
2. Kattintson a B2C funkciók panelje **Identitásszolgáltatók**.
3. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
4. Adjon meg egy rövid **neve** az identitás szolgáltató a konfigurációhoz. Adja meg például a "Facebook".
5. Kattintson a **identitásszolgáltató típusa**válassza **Facebook**, és kattintson a **OK**.
6. Kattintson a **az identitásszolgáltató beállítása** és adja meg az alkalmazás azonosítója és a titkos Alkalmazáskulcs (a korábban létrehozott Facebook-alkalmazás) az a **ügyfél-azonosító** és **titkos Ügyfélkód** a mezők jelölik.
7. Kattintson a **OK**, és kattintson a **létrehozás** a Facebook-konfiguráció mentéséhez.

> [!NOTE]
> Hozzáadás egy **identitásszolgáltató** a bérlő nem módosítja a meglévő szabályzatokat. Ne felejtse el frissíteni a szabályzatok által az imént létrehozott identitásszolgáltató többek között.
>