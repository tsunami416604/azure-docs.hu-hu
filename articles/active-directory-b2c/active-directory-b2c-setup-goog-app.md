---
title: Regisztráció és bejelentkezés a Google-fiókkal az Azure Active Directory B2C beállítása |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés az alkalmazásokban az Azure Active Directory B2C használatával a Google-fiókkal rendelkező ügyfelek számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 477bd6047da639dcf21592a7ec0c1b80844e031e
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337734"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés a Google-fiókkal az Azure Active Directory B2C beállítása

## <a name="create-a-google-application"></a>Egy Google-alkalmazás létrehozása

Identitás-szolgáltatóként az Azure Active Directory (Azure AD) B2C egy Google-fiók használatához szüksége alkalmazás létrehozása a bérlőben, amely azt jelöli. Ha még nem rendelkezik egy Google-fiók beszerezheti a [ https://accounts.google.com/SignUp ](https://accounts.google.com/SignUp).

1. Jelentkezzen be a [Google fejlesztői konzolon](https://console.developers.google.com/) Google-fiókja hitelesítő adataival.
2. Válassza ki **Create project**, és kattintson a **létrehozás**. Ha projektek előtt létrehozott, válassza ki a projekt listát, és válassza **új projekt**.
3. Adjon meg egy **projektnév**, és kattintson a **létrehozás**.
3. Válassza ki **hitelesítő adatok** a bal oldali menüben, és válassza ki a **hitelesítő adatok létrehozása** > **Oauth-Ügyfélazonosító**.
4. Válassza ki **konfigurálása beleegyezést kérő oldalon**.
5. Válasszon vagy adjon meg egy érvényes **E-mail-cím**, adjon meg egy **a felhasználóknak megjelenített Terméknév**, és kattintson a **mentése**.
6. A **alkalmazástípus**válassza **webes alkalmazás**.
7. Adjon meg egy **neve** adja meg az alkalmazás `https://{tenant}.b2clogin.com` a **JavaScript engedélyezett eredetek**, és `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` a **jogosult átirányítási URI-k**. Cserélje le **{tenant}** a bérlő nevét (például: contosob2c).
8. Kattintson a **Create** (Létrehozás) gombra.
9. Másolja le az értékeket a **ügyfél-azonosító** és **titkos Ügyfélkód**. Szüksége lesz mindkettő Google konfigurálása identitás-szolgáltatóként a bérlőben. **Titkos Ügyfélkód** egy fontos biztonsági hitelesítő adat.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Google-fiók konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Úgy győződhet meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárt használja, hogy átvált rá az Azure Portal jobb felső sarkában. Jelölje ki előfizetői adatait, majd válassza a **Könyvtárváltás** lehetőséget. 

    ![Átváltás az Azure AD B2C-bérlőre](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    Válassza ki a bérlőjét tartalmazó könyvtárt.

    ![Könyvtár kijelölése](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
5. Adjon meg egy **neve**. Adja meg például *Google*.
6. Válassza ki **identitásszolgáltató típusa**válassza **Google**, és kattintson a **OK**.
7. Válassza ki **az identitásszolgáltató beállítása** , és adja meg az ügyfél-Azonosítót, mint korábban rögzített a **ügyfél-azonosító** , és adja meg a titkos Ügyfélkulcsot, feljegyzett a **titkos Ügyfélkód**a Google-alkalmazás, amelyet korábban hozott létre.
8. Kattintson a **OK** majd **létrehozás** a Google-konfiguráció mentéséhez.

