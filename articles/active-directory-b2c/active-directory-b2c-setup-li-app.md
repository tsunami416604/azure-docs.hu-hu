---
title: Regisztráció és bejelentkezés a LinkedIn-fiókkal az Azure Active Directory B2C beállítása |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés az alkalmazásokban az Azure Active Directory B2C használatával LinkedIn-fiókkal rendelkező ügyfelek számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e1949c32833bb1d5e6603a6f5e36e22dc58e8cec
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43336928"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés a LinkedIn-fiókkal az Azure Active Directory B2C beállítása

## <a name="create-a-linkedin-application"></a>A LinkedIn-alkalmazás létrehozása

A LinkedIn-fiók használata Identitásszolgáltatóként az Azure Active Directory (Azure AD) B2C-ben, hozzon létre egy alkalmazást a bérlőben, hogy az azt jelölő kell. Ha a LinkedIn-fiók még nem rendelkezik, beszerezheti a [ https://www.linkedin.com/ ](https://www.linkedin.com/).

1. Jelentkezzen be a [LinkedIn fejlesztők webhely](https://www.developer.linkedin.com/) LinkedIn-fiókja hitelesítő adataival.
2. Válassza ki **saját alkalmazások**, és kattintson a **alkalmazás létrehozása**.
3. Adja meg **cég neve**, **alkalmazásnév**, **alkalmazásleírás**, **alkalmazás emblémája**, **alkalmazás használata** , **Webhely URL-címe**, **üzleti E-mail**, és **munkahelyi telefon**.
4. Fogadja el a **LinkedIn API használati** kattintson **küldés**.
5. Másolja le az értékeket a **ügyfél-azonosító** és **titkos Ügyfélkód**. Ezeket a megtalálhatja **hitelesítési kulcsok**. Szüksége lesz mindkettő LinkedIn konfigurálása identitás-szolgáltatóként a bérlőben. **Titkos Ügyfélkód** egy fontos biztonsági hitelesítő adat.
6. Adja meg `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` a **átirányítási URL-címek engedélyezett**. Cserélje le **{tenant}** a bérlő nevét (például: contosob2c). Válassza ki **Hozzáadás**, és kattintson a **frissítés**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>A LinkedIn-fiók konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Úgy győződhet meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárt használja, hogy átvált rá az Azure Portal jobb felső sarkában. Jelölje ki előfizetői adatait, majd válassza a **Könyvtárváltás** lehetőséget. 

    ![Átváltás az Azure AD B2C-bérlőre](./media/active-directory-b2c-setup-li-app/switch-directories.png)

    Válassza ki a bérlőjét tartalmazó könyvtárt.

    ![Könyvtár kijelölése](./media/active-directory-b2c-setup-li-app/select-directory.png)

3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
5. Adjon meg egy **neve**. Adja meg például *LinkedIn*.
6. Válassza ki **identitásszolgáltató típusa**válassza **LinkedIn**, és kattintson a **OK**.
7. Válassza ki **az identitásszolgáltató beállítása** , és adja meg az ügyfél-azonosítót, mint korábban rögzített a **ügyfél-azonosító** , és adja meg a titkos Ügyfélkulcsot, feljegyzett a **titkos Ügyfélkód**korábban létrehozott LinkedIn-fiók alkalmazás.
8. Kattintson a **OK** majd **létrehozás** a LinkedIn-fiók konfigurációjának mentéséhez.

