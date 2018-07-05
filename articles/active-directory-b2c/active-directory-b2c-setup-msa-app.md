---
title: A Microsoft-fiók konfigurációját az Azure Active Directory B2C |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés a az Azure Active Directory B2C által védett alkalmazások a Microsoft-fiókkal rendelkező felhasználók számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c788b14a99125a208390cd4f8ead338efed06933
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444167"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-microsoft-accounts"></a>Az Azure Active Directory B2C: Adja meg előfizetési, és jelentkezzen be a Microsoft-fiókkal rendelkező felhasználók számára
## <a name="create-a-microsoft-account-application"></a>Hozzon létre egy Microsoft-fiók alkalmazás
Microsoft-fiók használata Identitásszolgáltatóként az Azure Active Directory (Azure AD) B2C-ben, meg kell hozzon létre egy Microsoft-fiók alkalmazást, és adja meg azt a megfelelő paraméterekkel. Ehhez egy Microsoft-fiók szükséges. Ha még nincs fiókja, beszerezheti a [ https://www.live.com/ ](https://www.live.com/).

1. Nyissa meg a [Microsoft alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) , és jelentkezzen be a Microsoft-fiók hitelesítő adatait.
2. Kattintson a **alkalmazás hozzáadása**.
   
    ![A Microsoft-fiók – új alkalmazás hozzáadása](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)
3. Adjon meg egy **neve** az alkalmazásra, majd a **-alkalmazás létrehozása**.
   
    ![A Microsoft-fiók – alkalmazás neve](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)
4. Másolja az értéket a **alkalmazásazonosító**. Szüksége lesz rá Microsoft-fiók beállítása lehetőséget Identitásszolgáltatóként, a bérlőben.
   
    ![A Microsoft-fiók – alkalmazás azonosítója](./media/active-directory-b2c-setup-msa-app/msa-app-id.png)
5. Kattintson a **Hozzáadás platform** válassza **webes**.
   
    ![A Microsoft-fiók - platform hozzáadása](./media/active-directory-b2c-setup-msa-app/msa-add-platform.png)
   
    ![A Microsoft-fiók – Web](./media/active-directory-b2c-setup-msa-app/msa-web.png)
6. Adja meg `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` a a **átirányítási URI-k** mező. Cserélje le **{tenant}** a bérlő nevét (például: contosob2c.onmicrosoft.com).
   
    ![A Microsoft-fiók – átirányítási URL-cím](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)
7. Kattintson a **új jelszó készítése** alatt a **titkos Alkalmazáskulcsok** szakaszban. Másolja a képernyőn megjelenő új jelszót. Szüksége lesz rá Microsoft-fiók beállítása lehetőséget Identitásszolgáltatóként, a bérlőben. Ez a jelszó, egy fontos biztonsági hitelesítő adat.
   
    ![A Microsoft-fiók – új jelszó létrehozása](./media/active-directory-b2c-setup-msa-app/msa-generate-new-password.png)
   
    ![A Microsoft-fiók – új jelszó](./media/active-directory-b2c-setup-msa-app/msa-new-password.png)
8. Jelölje be a jelölőnégyzetet, arról, hogy a **Live SDK támogatás** alatt a **speciális beállítások** szakaszban. Kattintson a **Save** (Mentés) gombra.
   
    ![Microsoft-fiók – a Live SDK-támogatás](./media/active-directory-b2c-setup-msa-app/msa-live-sdk-support.png)

## <a name="configure-microsoft-account-as-an-identity-provider-in-your-tenant"></a>A bérlő Identitásszolgáltatóként, Microsoft-fiók konfigurálása
1. Az alábbi lépéseket követve [lépjen a B2C funkciók paneljére](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure Portalon.
2. Kattintson a B2C funkciók panelje **Identitásszolgáltatók**.
3. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
4. Adjon meg egy rövid **neve** az identitás szolgáltató a konfigurációhoz. Adja meg például a "MSA".
5. Kattintson a **identitásszolgáltató típusa**válassza **Microsoft-fiók**, és kattintson a **OK**.
6. Kattintson a **az identitásszolgáltató beállítása** , és adja meg az alkalmazás azonosítója és a Microsoft-fiók alkalmazás, amely a korábban létrehozott jelszót.
7. Kattintson a **OK** majd **létrehozás** a Microsoft-fiók konfigurációjának mentéséhez.

