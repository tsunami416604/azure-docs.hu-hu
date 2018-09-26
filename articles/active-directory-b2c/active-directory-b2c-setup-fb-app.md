---
title: Állítsa be regisztráció és bejelentkezés Facebook-fiókkal az Azure Active Directory B2C használatával |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés Facebook az alkalmazások az Azure Active Directory B2C fiókkal rendelkező ügyfelek számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: b1fbf77bfb771bd94ed9acdecb3c54e4c6894687
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182110"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Regisztráljon, és jelentkezzen be a Facebook-fiókkal az Azure Active Directory B2C beállítása

## <a name="create-a-facebook-application"></a>Egy Facebook-alkalmazás létrehozása

Identitás-szolgáltatóként az Azure Active Directory (Azure AD) B2C egy Facebook-fiók használatához szüksége alkalmazás létrehozása a bérlőben, amely azt jelöli. Ha még nem rendelkezik egy Facebook-fiókban, beszerezheti a [ https://www.facebook.com/ ](https://www.facebook.com/).

1. Jelentkezzen be a [Facebook-fejlesztőknek](https://developers.facebook.com/) Facebook-fiókja hitelesítő adataival.
2. Ha még nem tette meg, hogy Facebook-fejlesztőként regisztrálnia kell. Ehhez válassza ki a **regisztrálása** az oldal jobb felső sarkában, fogadja el a Facebook-szabályzatokat, és végezze el a regisztrációs lépéseket.
3. Válassza ki **saját alkalmazások** majd **adjon hozzá egy új alkalmazást**. 
4. Adjon meg egy **megjelenítendő név** és a egy érvényes **kapcsolattartó E-mail**.
5. Kattintson a **Alkalmazásazonosító létrehozása**. Ez előfordulhat, hogy fogadja el a Facebook-platform házirendek és a egy online biztonsági ellenőrzés.
6. Válassza ki **beállítások** > **alapszintű**.
7. Válasszon egy **kategória**, például `Business and Pages`. Ez az érték Facebook által igényelt, de nem használta az Azure AD B2C-t.
8. Válassza a lap alján **hozzáadása Platform**, majd válassza ki **webhely**.
9. A **webhely URL-címe**, adja meg `https://your-tenant-name.b2clogin.com/` cseréje `your-tenant-name` a bérlő nevével. Adjon meg egy URL-címet a **adatvédelmi szabályzat URL-címe**, például `http://www.contoso.com`. A szabályzat URL-címe egy oldalon kell karbantartani, adja meg az alkalmazás adatvédelmi nyilatkozatát.
10. Válassza ki **módosítások mentése**.
11. Az oldal tetején lévő másolás értékét **Alkalmazásazonosító**. 
12. Kattintson a **megjelenítése** , és másolja az értéket a **titkos Alkalmazáskulcs**. Használhatja mindkettő konfigurálása Facebook identitás-szolgáltatóként, a bérlőben. **Alkalmazás titkos kulcsát** egy fontos biztonsági hitelesítő adat.
13. Válassza ki **termékek**, majd válassza ki **beállítása** alatt **Facebook-bejelentkezés**.
14. Válassza ki **beállítások** alatt **Facebook-bejelentkezés**.
15. A **érvényes OAuth átirányítási URI-k**, adja meg `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Cserélje le `your-tenant-name` a bérlő nevével. Kattintson a **módosítások mentése** az oldal alján.
16. A Facebook-alkalmazás Azure AD B2C-vel elérhetővé tenni, jelölje be **alkalmazás áttekintése**állítsa be **saját alkalmazás győződjön meg arról, nyilvános?** való **Igen**, és kattintson a **megerősítése** .

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Facebook-fiókok konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása. 
3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
5. Adjon meg egy **neve**. Adja meg például *Facebook*.
6. Válassza ki **identitásszolgáltató típusa**válassza **Facebook**, és kattintson a **OK**.
7. Válassza ki **az identitásszolgáltató beállítása** , és adja meg az alkalmazás azonosítója, mint korábban rögzített a **ügyfél-azonosító** , és adja meg az alkalmazás titkos kulcsát, feljegyzett a **titkos Ügyfélkód** , a Facebook-alkalmazás, amelyet korábban hozott létre.
8. Kattintson a **OK** majd **létrehozás** a Facebook-konfiguráció mentéséhez.