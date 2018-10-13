---
title: Regisztráció és bejelentkezés a Google-fiókkal az Azure Active Directory B2C beállítása |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés az alkalmazásokban az Azure Active Directory B2C használatával a Google-fiókkal rendelkező ügyfelek számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5f6ce3564cf4056dc9295e1885be425bbe3d7701
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2018
ms.locfileid: "49165300"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés a Google-fiókkal az Azure Active Directory B2C beállítása

## <a name="create-a-google-application"></a>Egy Google-alkalmazás létrehozása

Identitás-szolgáltatóként az Azure Active Directory (Azure AD) B2C egy Google-fiók használatához szüksége alkalmazás létrehozása a bérlőben, amely azt jelöli. Ha még nem rendelkezik egy Google-fiók beszerezheti a [ https://accounts.google.com/SignUp ](https://accounts.google.com/SignUp).

1. Jelentkezzen be a [Google fejlesztői konzolon](https://console.developers.google.com/) Google-fiókja hitelesítő adataival.
2. Válassza ki **Create project**, és kattintson a **létrehozás**. Ha projektek előtt létrehozott, válassza ki a projekt listát, és válassza **új projekt**.
3. Adjon meg egy **projektnév**, kattintson a **létrehozás**, majd győződjön meg arról, hogy az új projekt használ, és.
3. Válassza ki **hitelesítő adatok** a bal oldali menüben, és válassza ki a **hitelesítő adatok létrehozása** > **Oauth-Ügyfélazonosító**.
4. Válassza ki **konfigurálása beleegyezést kérő oldalon**.
5. Válasszon vagy adjon meg egy érvényes **E-mail-cím**, adja meg egy **a felhasználóknak megjelenített Terméknév**, hozzáadása `b2clogin.com` való **tartományok jogosult**, és kattintson a **mentése** .
6. A **alkalmazástípus**válassza **webes alkalmazás**.
7. Adjon meg egy **neve** adja meg az alkalmazás `https://your-tenant-name.b2clogin.com` a **JavaScript engedélyezett eredetek**, és `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` a **jogosult átirányítási URI-k**. Cserélje le `your-tenant-name` a bérlő nevével. Kisbetűk használhatók, ha akkor is, ha a bérlő Azure AD B2C-ben nagybetűk van definiálva, írja be a bérlő nevét kell.
8. Kattintson a **Create** (Létrehozás) gombra.
9. Másolja le az értékeket a **ügyfél-azonosító** és **titkos Ügyfélkód**. Szüksége lesz mindkettő Google konfigurálása identitás-szolgáltatóként a bérlőben. **Titkos Ügyfélkód** egy fontos biztonsági hitelesítő adat.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Google-fiók konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
5. Adjon meg egy **neve**. Adja meg például *Google*.
6. Válassza ki **identitásszolgáltató típusa**válassza **Google**, és kattintson a **OK**.
7. Válassza ki **az identitásszolgáltató beállítása** , és adja meg az ügyfél-Azonosítót, mint korábban rögzített a **ügyfél-azonosító** , és adja meg a titkos Ügyfélkulcsot, feljegyzett a **titkos Ügyfélkód**a Google-alkalmazás, amelyet korábban hozott létre.
8. Kattintson a **OK** majd **létrehozás** a Google-konfiguráció mentéséhez.

