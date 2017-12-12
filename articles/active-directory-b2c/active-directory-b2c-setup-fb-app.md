---
title: "Az Azure Active Directory B2C: Facebook konfigurációs |} Microsoft Docs"
description: "Adja meg a regisztráció és bejelentkezés az Azure Active Directory B2C által védett alkalmazások Facebook-fiókkal rendelkező felhasználók számára."
services: active-directory-b2c
documentationcenter: 
author: sromeroz
manager: mtillman
editor: sromeroz
ms.assetid: b875f235-a1d2-4abb-b9f0-b89beac38a32
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/7/2017
ms.author: sromeroz
ms.openlocfilehash: 7f551a7ba9b7ddbb373e6b6418ab43fe7ce9be36
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>Az Azure Active Directory B2C: Adja meg regisztráció és bejelentkezés Facebook-fiókkal rendelkező felhasználók
## <a name="create-a-facebook-application"></a>Facebook-alkalmazás létrehozása
Az Azure Active Directory (Azure AD) B2C identitás-szolgáltatóként Facebook használatához szüksége Facebook-alkalmazás létrehozása, és adja meg azt a megfelelő paraméterekkel. Ehhez Facebook-fiók szükséges. Ha még nincs fiókja, beszerezheti a [https://www.facebook.com/](https://www.facebook.com/).

1. Lépjen a [Facebook-fejlesztőknek](https://developers.facebook.com/) webhelyet, és jelentkezzen be a Facebook-fiók hitelesítő adatait.
2. Ha még nem tette meg, Facebook-fejlesztőként regisztrálni szeretné. Ehhez kattintson **regisztrálása** (az oldal jobb felső sarkában), a Facebook tartozó házirendek fogadja el, és a regisztrációs lépéseket.
3. Kattintson a **saját alkalmazások** majd **fel egy új alkalmazást**. 
4. Ebben a formátumban adja meg a **megjelenített név** és egy érvényes **kapcsolattartó E-mail**.
5. Kattintson a **App ID Azonosítójának létrehozása**. Ez előfordulhat, hogy fogadja el a Facebook-platform házirendek és az online biztonsági ellenőrzés.
6. A bal oldali oszlopban kattintson **beállítások** majd **alapvető** Ha nincs bejelölve.
7. Válassza ki a **kategória**. 
8. Kattintson a **+ Hozzáadás Platform** válassza **webhely**.
   
    ![Facebook - beállítások](./media/active-directory-b2c-setup-fb-app/fb-settings.png)
   
    ![Facebook - beállítások - webhely](./media/active-directory-b2c-setup-fb-app/fb-website.png)
9. Adja meg `https://login.microsoftonline.com/` a a **webhely URL-címe** mezőben, majd kattintson **módosítások mentése** az oldal alján.
   
    ![Facebook - webhely URL-címe](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

10. Másolja a értékének **Alkalmazásazonosító**. Kattintson a **megjelenítése** , és másolja a értékének **alkalmazás titkos kulcs**. Mindkettő konfigurálásához a Facebook-bérlőben identitás-szolgáltatóként kell. **Alkalmazás titkos kulcs** van egy fontos biztonsági hitelesítő adatok.
   
    ![Facebook - Alkalmazásazonosító & alkalmazás titkos kulcs](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)
11. Kattintson a **+ Hozzáadás termék** a bal oldali navigációs, majd a **Set Up** gombra kattint, a **Facebook bejelentkezési**.
   
    ![Facebook - Facebook-bejelentkezés](./media/active-directory-b2c-setup-fb-app/fb-login.png)
12. Kattintson a **beállítások** a alatt az megfelelő nav **Facebook-bejelentkezés**

    ![Facebook - Facebook bejelentkezési beállítások](./media/active-directory-b2c-setup-fb-app/fb-login-settings.png)
13. Adja meg `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` a a **érvényes OAuth-alapú átirányítási URI-azonosítók** mező mellett a **OAuth ügyfélbeállítások** szakasz. Cserélje le **{tenant}** a bérlő nevű (például contosob2c.onmicrosoft.com). Kattintson a **módosítások mentése** az oldal alján.
    
    ![Facebook - OAuth átirányítási URI](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)
14. A Facebook-alkalmazás által az Azure AD B2C használhatóvá tételéhez kell nyilvánosan elérhető legyen. Ehhez kattintson **App felülvizsgálati** a bal oldali navigációs és a lap tetején kapcsoló bekapcsolásával **Igen** elemre kattintva **megerősítése**.
    
    ![Facebook - alkalmazást nyilvános](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>Konfigurálja a Facebook-bérlőben identitás-szolgáltatóként
1. Az alábbi lépéseket követve [lépjen a B2C funkciók panelje](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure portálon.
2. Kattintson a B2C funkciók panelje **identitás-szolgáltatóktól**.
3. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
4. Adjon meg egy rövid **neve** a az identitás-szolgáltató konfigurációját. Írja be például a "Facebook".
5. Kattintson a **identitás szolgáltatótípus**, jelölje be **Facebook**, és kattintson a **OK**.
6. Kattintson a **az identitásszolgáltató beállítása** , és írja be az alkalmazás Azonosítóját és alkalmazás titkos kulcs (a korábban létrehozott Facebook-alkalmazás) az a **ügyfél-azonosító** és **ügyfélkulcs** rendre mezők.
7. Kattintson a **OK**, és kattintson a **létrehozása** a Facebook-konfiguráció mentéséhez.

> [!NOTE]
> Hozzáadása egy **identitásszolgáltató** a bérlő nem módosítja a meglévő szabályzatokat. Ne felejtse el frissíteni a házirendeket az imént létrehozott identitásszolgáltató-ot.
>