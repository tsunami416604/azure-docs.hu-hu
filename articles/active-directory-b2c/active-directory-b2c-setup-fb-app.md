---
title: Állítsa be regisztráció és bejelentkezés Facebook-fiókkal az Azure Active Directory B2C használatával |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés Facebook az alkalmazások az Azure Active Directory B2C fiókkal rendelkező ügyfelek számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 985285b463d66770f97a431705d5b9198b632592
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344606"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Regisztráljon, és jelentkezzen be a Facebook-fiókkal az Azure Active Directory B2C beállítása

## <a name="create-a-facebook-application"></a>Egy Facebook-alkalmazás létrehozása

Identitás-szolgáltatóként az Azure Active Directory (Azure AD) B2C egy Facebook-fiók használatához szüksége alkalmazás létrehozása a bérlőben, amely azt jelöli. Ha még nem rendelkezik egy Facebook-fiókban, beszerezheti a [ https://www.facebook.com/ ](https://www.facebook.com/).

1. Jelentkezzen be a [Facebook-fejlesztőknek](https://developers.facebook.com/) Facebook-fiókja hitelesítő adataival.
2. Ha még nem tette meg, hogy Facebook-fejlesztőként regisztrálnia kell. Ehhez válassza ki a **regisztrálása** az oldal jobb felső sarkában, fogadja el a Facebook-szabályzatokat, és végezze el a regisztrációs lépéseket.
3. Válassza ki **saját alkalmazások** majd **új alkalmazás hozzáadása**. 
4. Adjon meg egy **megjelenítendő név** és a egy érvényes **kapcsolattartó E-mail**.
5. Kattintson a **Alkalmazásazonosító létrehozása**. Ez előfordulhat, hogy fogadja el a Facebook-platform házirendek és a egy online biztonsági ellenőrzés.
6. Válassza ki **beállítások** > **alapszintű**.
7. Válassza a lap alján **hozzáadása Platform**, majd válassza ki **webhely**.
8. Adja meg `https://{tenantname}.b2clogin.com/` a **webhely URL-címe**. Adjon meg egy URL-címet a **adatvédelmi szabályzat URL-címe**, például `http://www.contoso.com`.
9. Válassza ki **módosítások mentése**.
11. Az oldal tetején lévő másolás értékét **Alkalmazásazonosító**. 
12. Kattintson a **megjelenítése** , és másolja az értéket a **titkos Alkalmazáskulcs**. Használhatja mindkettő konfigurálása Facebook identitás-szolgáltatóként, a bérlőben. **Alkalmazás titkos kulcsát** egy fontos biztonsági hitelesítő adat.
13. Válassza ki **termékek**, majd válassza ki **beállítása** alatt **Facebook-bejelentkezés**.
14. Válassza ki **beállítások** alatt **Facebook-bejelentkezés**.
15. Adja meg `https://{tenantname}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` a **érvényes OAuth átirányítási URI-k** . Cserélje le **{tenant}** a bérlő nevét (például: contosob2c). Kattintson a **módosítások mentése** az oldal alján.
16. A Facebook-alkalmazás elérhető az Azure AD B2C-vel, válasszon ki **alkalmazás áttekintése**állítsa be **saját alkalmazás győződjön meg arról, nyilvános?** való **Igen**, válasszon egy kategóriát, például `Business and Pages`majd **megerősítése**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Facebook-fiókok konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Úgy győződhet meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárt használja, hogy átvált rá az Azure Portal jobb felső sarkában. Jelölje ki előfizetői adatait, majd válassza a **Könyvtárváltás** lehetőséget. 

    ![Átváltás az Azure AD B2C-bérlőre](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    Válassza ki a bérlőjét tartalmazó könyvtárt.

    ![Könyvtár kijelölése](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
5. Adjon meg egy **neve**. Adja meg például *Facebook*.
6. Válassza ki **identitásszolgáltató típusa**válassza **Facebook**, és kattintson a **OK**.
7. Válassza ki **az identitásszolgáltató beállítása** , és adja meg az alkalmazás azonosítója, mint korábban rögzített a **ügyfél-azonosító** , és adja meg az alkalmazás titkos kulcsát, feljegyzett a **titkos Ügyfélkód** , a Facebook-alkalmazás, amelyet korábban hozott létre).
8. Kattintson a **OK** majd **létrehozás** a Facebook-konfiguráció mentéséhez.