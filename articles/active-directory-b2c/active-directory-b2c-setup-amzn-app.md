---
title: "Az Azure Active Directory B2C: Amazon konfigurációs |} Microsoft Docs"
description: "Adja meg a regisztráció és bejelentkezés az Azure Active Directory B2C által védett alkalmazások Amazon-fiókkal rendelkező felhasználók számára."
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 77c099bb-a005-4d75-87f9-f61e3de48725
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: dcc97e1b7f6287bd7692c52bf068950065a26572
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-amazon-accounts"></a>Az Azure Active Directory B2C: Regisztráció és bejelentkezés adhat Amazon-fiókkal rendelkező felhasználók
## <a name="create-an-amazon-application"></a>Az Amazon-alkalmazás létrehozása
Az Azure Active Directory (Azure AD) B2C identitás-szolgáltatóként Amazon használatához szüksége Amazon alkalmazás létrehozása, és adja meg azt a megfelelő paraméterekkel. Ehhez az Amazon-fiók szükséges. Ha még nincs fiókja, beszerezheti a [http://www.amazon.com/](http://www.amazon.com/).

1. Lépjen a [Amazon fejlesztői központ](https://login.amazon.com/) és jelentkezzen be az Amazon fiók hitelesítő adatait.
2. Ha még nem tette meg, kattintson a **regisztráció**, hajtsa végre a fejlesztői regisztrációs lépéseket, és fogadja el a szabályzatot.
3. Kattintson a **új alkalmazás regisztrálása**.
   
    ![Egy új alkalmazást az Amazon webhelyen regisztrálása](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)
4. Adja meg az alkalmazással kapcsolatos információk (**neve**, **leírás**, és **adatvédelmi nyilatkozat URL-címe**), és kattintson a **mentése**.
   
    ![Amazon egy új alkalmazás regisztrálásához alkalmazással kapcsolatos adatok megadása](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)
5. Az a **Webbeállításainak** területen értékeinek másolására **ügyfél-azonosító** és **Ügyfélkulcs**. (Kattintson a **megjelenítése titkos** gombra kattintva megjelenik ez.) Mindkettő konfigurálásához Amazon-bérlőben identitás-szolgáltatóként van szüksége. Kattintson a **szerkesztése** szakasz alján. **Titkos Ügyfélkulcsot** van egy fontos biztonsági hitelesítő adatok.
   
    ![Olyan ügyfél-Azonosítót és titkos Ügyfélkulcs az Amazon: új alkalmazás](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)
6. Adja meg `https://login.microsoftonline.com` a a **engedélyezett JavaScript források** mező és `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` a a **visszatérési URL-címek engedélyezett** mező. Cserélje le **{tenant}** a bérlő nevű (például contoso.onmicrosoft.com). Kattintson a **Save** (Mentés) gombra. A **{tenant}** érték kis-és nagybetűket.
   
    ![JavaScript források és visszatérési URL-címek megadása az új alkalmazást az Amazon:](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## <a name="configure-amazon-as-an-identity-provider-in-your-tenant"></a>A bérlő az identitás-szolgáltatóként Amazon konfigurálása
1. Az alábbi lépéseket követve [lépjen a B2C funkciók panelje](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure portálon.
2. Kattintson a B2C funkciók panelje **identitás-szolgáltatóktól**.
3. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
4. Adjon meg egy rövid **neve** a az identitás-szolgáltató konfigurációját. Írja be például a "Amzn".
5. Kattintson a **identitás szolgáltatótípus**, jelölje be **Amazon**, és kattintson a **OK**.
6. Kattintson a **az identitásszolgáltató beállítása** , és adja meg az ügyfél-azonosító és a titkos ügyfélkulcs az Amazon-alkalmazás, amelyet korábban hozott létre.
7. Kattintson a **OK** majd **létrehozása** az Amazon-konfiguráció mentéséhez.

