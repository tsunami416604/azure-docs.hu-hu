---
title: Regisztráció és bejelentkezés a Microsoft-fiókkal az Azure Active Directory B2C beállítása |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés az alkalmazásokban az Azure Active Directory B2C használatával a Microsoft-fiókkal rendelkező ügyfelek számára.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1f9deaf29982c4bd753336f51e10a06ced6586f1
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54842529"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés a Microsoft-fiókkal az Azure Active Directory B2C beállítása

## <a name="create-a-microsoft-account-application"></a>Hozzon létre egy Microsoft-fiók alkalmazás

Microsoft-fiókot használni Identitásszolgáltatóként az Azure Active Directory (Azure AD) B2C-ben, meg kell hozzon létre egy alkalmazást, amely azt jelöli, a bérlőben. Ha még nincs Microsoft-fiókkal, beszerezheti a [ https://www.live.com/ ](https://www.live.com/).

1. Jelentkezzen be a [Microsoft alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) Microsoft-fiókja hitelesítő adataival.
2. A jobb felső sarokban válassza **alkalmazás hozzáadása**.
3. Adjon meg egy **neve** az alkalmazáshoz. Ha például *MSAapp1*.
4. Válassza ki **új jelszó készítése** , és győződjön meg arról, hogy másolja az identitásszolgáltató konfigurálásakor használt jelszót. Emellett másolja a **alkalmazásazonosító**. 
5. Válassza ki **Hozzáadás platform**, majd válassza ki, és **webes**.
4. Adja meg `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` a **átirányítási URL-ek**. Cserélje le `your-tenant-name` a bérlő nevével.
5. Kattintson a **Mentés** gombra.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Identitás-szolgáltatóként a Microsoft-fiók konfigurálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
5. Adjon meg egy **neve**. Adja meg például *MSA*.
6. Válassza ki **identitásszolgáltató típusa**válassza **Microsoft Account**, és kattintson a **OK**.
7. Válassza ki **az identitásszolgáltató beállítása** , és írja be az alkalmazásazonosítót, mint korábban rögzített a **ügyfél-azonosító** írja be a jelszót, mint feljegyzett a **titkos Ügyfélkód**, a Microsoft-fiók alkalmazás, amelyet korábban hozott létre.
8. Kattintson a **OK** majd **létrehozás** a Microsoft-fiók konfigurációjának mentéséhez.

