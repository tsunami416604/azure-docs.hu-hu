---
title: "Az Azure Active Directory B2C: Google + konfigurációs |} Microsoft Docs"
description: "Adja meg a regisztráció és bejelentkezés Google + az Azure Active Directory B2C által védett alkalmazások fiókkal rendelkező felhasználók számára."
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 4dcca66f-29e4-4b4d-8840-50baad736bd7
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: 6ab73e5c79742ab548733f5712dee1e28461db9f
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-google-accounts"></a>Az Azure Active Directory B2C: Regisztráció és bejelentkezés biztosítása Google + fiókkal rendelkező felhasználók
## <a name="create-a-google-application"></a>Google +-alkalmazás létrehozása
Használatához Google + az Azure Active Directory (Azure AD) B2C identitás-szolgáltatóként kell Google +-alkalmazás létrehozása, és adja meg azt a megfelelő paraméterekkel. Google +-fiók szükséges ehhez. Ha még nincs fiókja, beszerezheti a [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Lépjen a [Google fejlesztői konzol](https://console.developers.google.com/) , és jelentkezzen be Google + fiók hitelesítő adataival.
2. Kattintson a **Create project**, adjon meg egy **projekt neve**, és kattintson a **létrehozása**.
   
    ![Google + - első lépések](./media/active-directory-b2c-setup-goog-app/google-get-started.png)
   
    ![Google + – új projekt](./media/active-directory-b2c-setup-goog-app/google-new-project.png)
3. Kattintson a **API Manager** majd **hitelesítő adatok** a bal oldali navigációs.
4. Kattintson a **OAuth-hozzájárulás képernyő** fülre az oldal tetején.
   
    ![Google + - hitelesítő adatok](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)
5. Válassza ki vagy adjon meg érvényes **E-mail cím**, adjon meg egy **Terméknév**, és kattintson **mentése**.
   
    ![Google + - OAuth-hozzájárulás képernyő](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)
6. Kattintson a **új hitelesítő adatok** majd **OAuth-Ügyfélazonosító**.
   
    ![Google + - OAuth-hozzájárulás képernyő](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)
7. A **alkalmazástípus**, jelölje be **webes alkalmazás**.
   
    ![Google + - OAuth-hozzájárulás képernyő](./media/active-directory-b2c-setup-goog-app/google-web-app.png)
8. Adjon meg egy **neve** adja meg az alkalmazás `https://login.microsoftonline.com` a a **JavaScript engedélyezett eredeteket** mező, és `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` a a **jogosult átirányítási URI-azonosítók** a mező. Cserélje le **{tenant}** a bérlő nevű (például contosob2c.onmicrosoft.com). A **{tenant}** érték kis-és nagybetűket. Kattintson a **Create** (Létrehozás) gombra.
   
    ![Google + - ügyfél-azonosító létrehozása](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)
9. Értékeinek másolására **ügyfél-azonosító** és **ügyfélkulcs**. Mindkettő konfigurálásához Google +-bérlőben identitás-szolgáltatóként kell. **Titkos ügyfélkulcsot** van egy fontos biztonsági hitelesítő adatok.
   
    ![Google + - ügyfélkulcs](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## <a name="configure-google-as-an-identity-provider-in-your-tenant"></a>Google + identitás-szolgáltatóként az Ön bérelt szolgáltatásának konfigurálása
1. Az alábbi lépéseket követve [lépjen a B2C funkciók panelje](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure portálon.
2. Kattintson a B2C funkciók panelje **identitás-szolgáltatóktól**.
3. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
4. Adjon meg egy rövid **neve** a az identitás-szolgáltató konfigurációját. Adja meg például "G +".
5. Kattintson a **identitás szolgáltatótípus**, jelölje be **Google**, és kattintson a **OK**.
6. Kattintson a **az identitásszolgáltató beállítása** , és adja meg az ügyfél-azonosító és a Google +-alkalmazás, amely a korábban létrehozott titkos ügyfélkulcsot.
7. Kattintson a **OK** majd **létrehozása** a Google + konfiguráció mentéséhez.

