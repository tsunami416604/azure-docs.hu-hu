---
title: Microsoft-fiók konfigurációja az Azure Active Directory B2C |} Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés az Azure Active Directory B2C által védett alkalmazások a Microsoft-fiókkal rendelkező felhasználók számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: be8c691835d2e89d5d305e01f9717e55e3741baf
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712419"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-microsoft-accounts"></a>Az Azure Active Directory B2C: Regisztráció és bejelentkezés adhat Microsoft-fiókkal rendelkező felhasználók
## <a name="create-a-microsoft-account-application"></a>Microsoft-fiók alkalmazás létrehozása
Az Azure Active Directory (Azure AD) B2C identitás-szolgáltatóként a Microsoft-fiók használatához szüksége hozzon létre egy Microsoft-fiók alkalmazást, és adja meg azt a megfelelő paraméterekkel. Ehhez a Microsoft-fiók szükséges. Ha még nincs fiókja, beszerezheti a [ https://www.live.com/ ](https://www.live.com/).

1. Lépjen a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) , és jelentkezzen be Microsoft-fiók hitelesítő adataival.
2. Kattintson a **hozzáadhat egy alkalmazást**.
   
    ![Microsoft fiók – egy új alkalmazás felvétele](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)
3. Adjon meg egy **neve** az alkalmazás, és kattintson **alkalmazás létrehozása**.
   
    ![Microsoft-fiók - alkalmazás neve](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)
4. Másolja a értékének **alkalmazásazonosító**. Szüksége lesz rájuk Microsoft-fiókok konfigurálása a bérlő az identitás-szolgáltatóként.
   
    ![Microsoft-fiók – alkalmazásazonosító](./media/active-directory-b2c-setup-msa-app/msa-app-id.png)
5. Kattintson a **Hozzáadás platform** válassza **webes**.
   
    ![Microsoft fiók - platform hozzáadása](./media/active-directory-b2c-setup-msa-app/msa-add-platform.png)
   
    ![Microsoft-fiók - webalkalmazás](./media/active-directory-b2c-setup-msa-app/msa-web.png)
6. Adja meg `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` a a **átirányítási URI-azonosítók** mező. Cserélje le **{tenant}** a bérlő nevű (például contosob2c.onmicrosoft.com).
   
    ![Microsoft-fiók - átirányítási URL-cím](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)
7. Kattintson a **új jelszó létrehozása** alatt a **alkalmazás titkos kulcsok** szakasz. Másolja az új jelszót a képernyőn jelenik meg. Szüksége lesz rájuk Microsoft-fiókok konfigurálása a bérlő az identitás-szolgáltatóként. Ez a jelszó nem egy fontos biztonsági hitelesítő adatok.
   
    ![Microsoft fiók – új jelszó létrehozása](./media/active-directory-b2c-setup-msa-app/msa-generate-new-password.png)
   
    ![Microsoft-fiók – új jelszó](./media/active-directory-b2c-setup-msa-app/msa-new-password.png)
8. Jelölje be a jelölőnégyzetet, amely szerint **Live SDK támogatási** alatt a **speciális beállítások** szakasz. Kattintson a **Save** (Mentés) gombra.
   
    ![Microsoft-fiók - Live SDK-támogatás](./media/active-directory-b2c-setup-msa-app/msa-live-sdk-support.png)

## <a name="configure-microsoft-account-as-an-identity-provider-in-your-tenant"></a>A bérlő az identitás-szolgáltatóként Microsoft-fiók konfigurálása
1. Az alábbi lépéseket követve [lépjen a B2C funkciók panelje](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure portálon.
2. Kattintson a B2C funkciók panelje **identitás-szolgáltatóktól**.
3. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
4. Adjon meg egy rövid **neve** a az identitás-szolgáltató konfigurációját. Írja be például a "MSA".
5. Kattintson a **identitás szolgáltatótípus**, jelölje be **Microsoft-fiók**, és kattintson a **OK**.
6. Kattintson a **az identitásszolgáltató beállítása** , és írja be az alkalmazás-azonosító és jelszó korábban létrehozott Microsoft-fiók alkalmazás.
7. Kattintson a **OK** majd **létrehozása** menteni a Microsoft-fiók konfigurációja.

