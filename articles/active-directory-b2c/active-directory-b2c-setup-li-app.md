---
title: Regisztráció és bejelentkezés a LinkedIn-fiók – Azure Active Directory B2C-beállítása |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés az alkalmazásokban az Azure Active Directory B2C használatával LinkedIn-fiókkal rendelkező ügyfelek számára.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 6c340d6b410a289f8beca156f85472cb729b786a
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56427819"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés a LinkedIn-fiókkal az Azure Active Directory B2C beállítása

## <a name="create-a-linkedin-application"></a>A LinkedIn-alkalmazás létrehozása

Használatához a LinkedIn-fiók, mint egy [identitásszolgáltató](active-directory-b2c-reference-oauth-code.md) Azure Active Directory (Azure AD) B2C-vel, az alkalmazás létrehozása a bérlőben, hogy az azt jelölő szüksége. Ha a LinkedIn-fiók még nem rendelkezik, beszerezheti a [ https://www.linkedin.com/ ](https://www.linkedin.com/).

1. Jelentkezzen be a [LinkedIn fejlesztők webhely](https://www.developer.linkedin.com/) LinkedIn-fiókja hitelesítő adataival.
2. Válassza ki **saját alkalmazások**, és kattintson a **alkalmazás létrehozása**.
3. Adja meg **cég neve**, **alkalmazásnév**, **alkalmazásleírás**, **alkalmazás emblémája**, **alkalmazás használata** , **Webhely URL-címe**, **üzleti E-mail**, és **munkahelyi telefon**.
4. Fogadja el a **LinkedIn API használati** kattintson **küldés**.
5. Másolja le az értékeket a **ügyfél-azonosító** és **titkos Ügyfélkód**. Ezeket a megtalálhatja **hitelesítési kulcsok**. Szüksége lesz mindkettő LinkedIn konfigurálása identitás-szolgáltatóként a bérlőben. **Titkos Ügyfélkód** egy fontos biztonsági hitelesítő adat.
6. Adja meg `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` a **átirányítási URL-címek engedélyezett**. Cserélje le `your-tenant-name` a bérlő nevével. Kisbetűk használhatók, ha akkor is, ha a bérlő Azure AD B2C-ben nagybetűk van definiálva, írja be a bérlő nevét kell. Válassza ki **Hozzáadás**, és kattintson a **frissítés**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>A LinkedIn-fiók konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
5. Adjon meg egy **neve**. Adja meg például *LinkedIn*.
6. Válassza ki **identitásszolgáltató típusa**válassza **LinkedIn**, és kattintson a **OK**.
7. Válassza ki **az identitásszolgáltató beállítása** , és adja meg az ügyfél-azonosítót, mint korábban rögzített a **ügyfél-azonosító** , és adja meg a titkos Ügyfélkulcsot, feljegyzett a **titkos Ügyfélkód**korábban létrehozott LinkedIn-fiók alkalmazás.
8. Kattintson a **OK** majd **létrehozás** a LinkedIn-fiók konfigurációjának mentéséhez.

