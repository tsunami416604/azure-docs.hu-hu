---
title: Regisztráció és bejelentkezés a Microsoft-fiókkal az Azure Active Directory B2C beállítása |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés az alkalmazásokban az Azure Active Directory B2C használatával a Microsoft-fiókkal rendelkező ügyfelek számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 338c2a197cb50091c3b272e0ce590341ffda1d7f
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43341083"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés a Microsoft-fiókkal az Azure Active Directory B2C beállítása

## <a name="create-a-microsoft-account-application"></a>Hozzon létre egy Microsoft-fiók alkalmazás

Microsoft-fiókot használni Identitásszolgáltatóként az Azure Active Directory (Azure AD) B2C-ben, meg kell hozzon létre egy alkalmazást, amely azt jelöli, a bérlőben. Ha még nincs Microsoft-fiókkal, beszerezheti a [ https://www.live.com/ ](https://www.live.com/).

1. Jelentkezzen be a [Microsoft alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) Microsoft-fiókja hitelesítő adataival.
2. A jobb felső sarokban válassza **alkalmazás hozzáadása**.
3. Adjon meg egy **neve** az alkalmazásra, majd a **létrehozás**.
4. A regisztrációs oldalon másolja az értékét **alkalmazásazonosító**. Használhatja a Microsoft-fiók konfigurálása identitás-szolgáltatóként a bérlőben.
5. Válassza ki **Hozzáadás platform**, majd válassza ki, és **webes**.
6. Adja meg `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` a **átirányítási URL-ek**. Cserélje le **{tenant}** a bérlő nevét (például: contosob2c).
7. Válassza ki **új jelszót létrehozni** alatt **titkos Alkalmazáskulcsok**. Másolja a képernyőn megjelenő új jelszót. Szüksége lesz rá a Microsoft-fiók konfigurálása identitás-szolgáltatóként a bérlőben. Ez a jelszó, egy fontos biztonsági hitelesítő adat.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Identitás-szolgáltatóként a Microsoft-fiók konfigurálása

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Úgy győződhet meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárt használja, hogy átvált rá az Azure Portal jobb felső sarkában. Jelölje ki előfizetői adatait, majd válassza a **Könyvtárváltás** lehetőséget. 

    ![Átváltás az Azure AD B2C-bérlőre](./media/active-directory-b2c-setup-msa-app/switch-directories.png)

    Válassza ki a bérlőjét tartalmazó könyvtárt.

    ![Könyvtár kijelölése](./media/active-directory-b2c-setup-msa-app/select-directory.png)

3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
5. Adjon meg egy **neve**. Adja meg például *MSA*.
6. Válassza ki **identitásszolgáltató típusa**válassza **Microsoft Account**, és kattintson a **OK**.
7. Válassza ki **az identitásszolgáltató beállítása** , és írja be az alkalmazásazonosítót, mint korábban rögzített a **ügyfél-azonosító** írja be a jelszót, mint feljegyzett a **titkos Ügyfélkód**, a Microsoft-fiók alkalmazás, amelyet korábban hozott létre.
8. Kattintson a **OK** majd **létrehozás** a Microsoft-fiók konfigurációjának mentéséhez.

