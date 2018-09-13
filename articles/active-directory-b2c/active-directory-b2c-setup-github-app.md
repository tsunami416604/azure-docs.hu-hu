---
title: Regisztráljon, és jelentkezzen be a GitHub-fiók az Azure Active Directory B2C beállítása |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés az alkalmazásokban az Azure Active Directory B2C használatával GitHub-fiókkal rendelkező ügyfelek számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 92ae3e0697c2ecd345c8e75690d40a81ef473ba4
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713782"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Regisztráljon, és jelentkezzen be a GitHub-fiók az Azure Active Directory B2C beállítása

> [!NOTE]
> Ez a funkció előzetes verzióban érhető el.
> 

Identitás-szolgáltatóként az Azure Active Directory (Azure AD) B2C egy Github-fiók használatához hozzon létre egy alkalmazást, amely azt jelöli, a bérlőben kell. Ha még nem rendelkezik Github-fiók, beszerezheti a [ https://www.github.com/ ](https://www.github.com/).

## <a name="create-a-github-oauth-application"></a>Egy GitHub-OAuth-alkalmazás létrehozása

1. Jelentkezzen be a [GitHub fejlesztői](https://github.com/settings/developers) webhely a GitHub hitelesítő adataival.
2. Válassza ki **OAuth alkalmazások** majd **új OAuth-alkalmazás**.
3. Adjon meg egy **alkalmazásnév** és a **kezdőlap URL-címe**.
4. Adja meg `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` a **engedélyezési visszahívási URL-Címének**. Cserélje le `your-tenant-name` az Azure AD B2C-bérlő nevével.
5. Kattintson a **alkalmazás regisztrálása**.
6. Másolja le az értékeket a **ügyfél-azonosító** és **titkos Ügyfélkód**. Mindkettő az identitásszolgáltató hozzáadása a bérlőhöz van szüksége.

## <a name="configure-a-github-account-as-an-identity-provider"></a>GitHub-fiók konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.  

    ![Átváltás az Azure AD B2C-bérlőre](./media/active-directory-b2c-setup-github-app/switch-directories.png)

3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
5. Adjon meg egy **neve**. Adja meg például *Github*.
6. Válassza ki **identitásszolgáltató típusa**válassza **Github (előzetes verzió)**, és kattintson a **OK**.
7. Válassza ki **az identitásszolgáltató beállítása** , és adja meg az ügyfél-azonosítót, mint korábban rögzített a **ügyfél-azonosító** , és adja meg a titkos Ügyfélkulcsot, feljegyzett a **titkos Ügyfélkód**a korábban létrehozott Github-fiók alkalmazás.
8. Kattintson a **OK** majd **létrehozás** a Github-fiók konfigurációjának mentéséhez.