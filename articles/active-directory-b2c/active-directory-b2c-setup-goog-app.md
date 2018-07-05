---
title: A Google + konfigurálása az Azure Active Directory B2C |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés a Google + az Azure Active Directory B2C által védett alkalmazások fiókkal rendelkező felhasználók számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: fe5722e8d5a0a8a5bf172577777ccb899fb7b94d
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443426"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-google-accounts"></a>Az Azure Active Directory B2C: Regisztráció és bejelentkezés a Google +-fiókkal rendelkező felhasználók számára biztosít
## <a name="create-a-google-application"></a>A Google +-alkalmazás létrehozása
A Google +-t használja Identitásszolgáltatóként az Azure Active Directory (Azure AD) B2C, kell a Google +-alkalmazás létrehozása, és adja meg azt a megfelelő paraméterekkel. A Google +-fiók szükséges ehhez. Ha még nincs fiókja, beszerezheti a [ https://accounts.google.com/SignUp ](https://accounts.google.com/SignUp).

1. Nyissa meg a [Google fejlesztői konzolon](https://console.developers.google.com/) , és jelentkezzen be a Google + fiók hitelesítő adatait.
2. Kattintson a **Create project**, adjon meg egy **projektnév**, és kattintson a **létrehozása**.
   
    ![A Google + - első lépések](./media/active-directory-b2c-setup-goog-app/google-get-started.png)
   
    ![A Google + – új projekt](./media/active-directory-b2c-setup-goog-app/google-new-project.png)
3. Kattintson a **API Manager** majd **hitelesítő adatok** a bal oldali navigációs.
4. Kattintson a **OAuth-hozzájárulási képernyő** a felső fülön.
   
    ![A Google + - hitelesítő adatok](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)
5. Válasszon vagy adjon meg egy érvényes **E-mail-cím**, adjon meg egy **Terméknév**, és kattintson a **mentése**.
   
    ![A Google + - OAuth-hozzájárulási képernyő](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)
6. Kattintson a **új hitelesítő adatok** majd **OAuth-Ügyfélazonosító**.
   
    ![A Google + - OAuth-hozzájárulási képernyő](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)
7. A **alkalmazástípus**válassza **webes alkalmazás**.
   
    ![A Google + - OAuth-hozzájárulási képernyő](./media/active-directory-b2c-setup-goog-app/google-web-app.png)
8. Adjon meg egy **neve** adja meg az alkalmazás `https://login.microsoftonline.com` a a **JavaScript engedélyezett eredetek** mező, és `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` a a **jogosult átirányítási URI-k** a mező. Cserélje le **{tenant}** a bérlő nevét (például: contosob2c.onmicrosoft.com). A **{tenant}** értéke a kis-és nagybetűket. Kattintson a **Create** (Létrehozás) gombra.
   
    ![A Google + - ügyfél-azonosító létrehozása](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)
9. Másolja le az értékeket a **ügyfél-azonosító** és **titkos Ügyfélkód**. Mindkettő konfigurálásához a Google + identitás-szolgáltatóként a bérlőben kell. **Titkos Ügyfélkód** egy fontos biztonsági hitelesítő adat.
   
    ![A Google + - ügyfélkulcs](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## <a name="configure-google-as-an-identity-provider-in-your-tenant"></a>A Google + identitás-szolgáltatóként a bérlő konfigurálása
1. Az alábbi lépéseket követve [lépjen a B2C funkciók paneljére](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure Portalon.
2. Kattintson a B2C funkciók panelje **Identitásszolgáltatók**.
3. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
4. Adjon meg egy rövid **neve** az identitás szolgáltató a konfigurációhoz. Adja meg például "G +".
5. Kattintson a **identitásszolgáltató típusa**válassza **Google**, és kattintson a **OK**.
6. Kattintson a **az identitásszolgáltató beállítása** , és adja meg az ügyfél-azonosító és titkos ügyfélkód a Google +-alkalmazás, amelyet korábban hozott létre.
7. Kattintson a **OK** majd **létrehozás** a Google +-konfiguráció mentéséhez.

