---
title: Regisztráljon, és jelentkezzen be a GitHub-fiók az Azure Active Directory B2C beállítása |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés az alkalmazásokban az Azure Active Directory B2C használatával GitHub-fiókkal rendelkező ügyfelek számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 11f3f190c0f55e45c549a8bd1de35f78eb7b752d
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337429"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Regisztráljon, és jelentkezzen be a GitHub-fiók az Azure Active Directory B2C beállítása

> [!NOTE]
> Ez a funkció előzetes verzióban érhető el.
> 

Identitás-szolgáltatóként az Azure Active Directory (Azure AD) B2C egy Github-fiók használatához hozzon létre egy alkalmazást, amely azt jelöli, a bérlőben kell. Ha még nem rendelkezik Github-fiók, beszerezheti a [ https://www.github.com/ ](https://www.github.com/).

## <a name="create-a-github-oauth-application"></a>Egy GitHub-OAuth-alkalmazás létrehozása

1. Jelentkezzen be a [GitHub fejlesztői](https://github.com/settings/developers) webhely a GitHub hitelesítő adataival.
2. Válassza ki **OAuth alkalmazások** majd **regisztrálhat új alkalmazásokat**.
3. Adjon meg egy **alkalmazásnév** és a **kezdőlap URL-címe**.
4. Adja meg `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` a **engedélyezési visszahívási URL-Címének**. Cserélje le **{tenant}** együtt az Azure AD B2C-bérlő neve (például: contosob2c).
5. Kattintson a **alkalmazás regisztrálása**.
6. Másolja le az értékeket a **ügyfél-azonosító** és **titkos Ügyfélkód**. Mindkettő az identitásszolgáltató hozzáadása a bérlőhöz van szüksége.

## <a name="configure-a-github-account-as-an-identity-provider"></a>GitHub-fiók konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Úgy győződhet meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárt használja, hogy átvált rá az Azure Portal jobb felső sarkában. Jelölje ki előfizetői adatait, majd válassza a **Könyvtárváltás** lehetőséget. 

    ![Átváltás az Azure AD B2C-bérlőre](./media/active-directory-b2c-setup-github-app/switch-directories.png)

    Válassza ki a bérlőjét tartalmazó könyvtárt.

    ![Könyvtár kijelölése](./media/active-directory-b2c-setup-github-app/select-directory.png)

3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
5. Adjon meg egy **neve**. Adja meg például *Github*.
6. Válassza ki **identitásszolgáltató típusa**válassza **Github (előzetes verzió)**, és kattintson a **OK**.
7. Válassza ki **az identitásszolgáltató beállítása** , és adja meg az ügyfél-azonosítót, mint korábban rögzített a **ügyfél-azonosító** , és adja meg a titkos Ügyfélkulcsot, feljegyzett a **titkos Ügyfélkód**a korábban létrehozott Github-fiók alkalmazás.
8. Kattintson a **OK** majd **létrehozás** a Github-fiók konfigurációjának mentéséhez.