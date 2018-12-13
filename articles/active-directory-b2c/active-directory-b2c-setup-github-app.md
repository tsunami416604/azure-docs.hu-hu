---
title: Regisztráljon, és jelentkezzen be a GitHub-fiók az Azure Active Directory B2C beállítása |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés az alkalmazásokban az Azure Active Directory B2C használatával GitHub-fiókkal rendelkező ügyfelek számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 426df541def0aa8d4d8b6a81a7364b32ee7f11dd
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074712"
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
4. Adja meg `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` a **engedélyezési visszahívási URL-Címének**. Cserélje le `your-tenant-name` az Azure AD B2C-bérlő nevével. Kisbetűk használhatók, ha akkor is, ha a bérlő Azure AD B2C-ben nagybetűk van definiálva, írja be a bérlő neve.
5. Kattintson a **alkalmazás regisztrálása**.
6. Másolja le az értékeket a **ügyfél-azonosító** és **titkos Ügyfélkód**. Mindkettő az identitásszolgáltató hozzáadása a bérlőhöz van szüksége.

## <a name="configure-a-github-account-as-an-identity-provider"></a>GitHub-fiók konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
5. Adjon meg egy **neve**. Adja meg például *Github*.
6. Válassza ki **identitásszolgáltató típusa**válassza **Github (előzetes verzió)**, és kattintson a **OK**.
7. Válassza ki **az identitásszolgáltató beállítása** , és adja meg az ügyfél-azonosítót, mint korábban rögzített a **ügyfél-azonosító** , és adja meg a titkos Ügyfélkulcsot, feljegyzett a **titkos Ügyfélkód**a korábban létrehozott Github-fiók alkalmazás.
8. Kattintson a **OK** majd **létrehozás** a Github-fiók konfigurációjának mentéséhez.