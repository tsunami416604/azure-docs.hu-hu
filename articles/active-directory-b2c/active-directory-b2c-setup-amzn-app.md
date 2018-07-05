---
title: Amazon-konfiguráció az Azure Active Directory B2C |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés a az Azure Active Directory B2C által védett alkalmazások Amazon-fiókkal rendelkező felhasználók számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e3b3d66b913b595e68c03b68990d1a4806952579
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443702"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-amazon-accounts"></a>Az Azure Active Directory B2C: Adja meg előfizetési, és jelentkezzen be a Amazon-fiókkal rendelkező felhasználók számára
## <a name="create-an-amazon-application"></a>Amazon-alkalmazás létrehozása
Amazon használata Identitásszolgáltatóként az Azure Active Directory (Azure AD) B2C-ben, meg kell Amazon-alkalmazás létrehozása, és adja meg azt a megfelelő paraméterekkel. Ehhez egy Amazon-fiókra van szükség. Ha még nincs fiókja, beszerezheti a [ http://www.amazon.com/ ](http://www.amazon.com/).

1. Nyissa meg a [Amazon fejlesztői központ](https://login.amazon.com/) , és jelentkezzen be az Amazon-fiókja hitelesítő adataival.
2. Ha még nem tette meg, kattintson a **regisztráció**, hajtsa végre a fejlesztői regisztrációs lépéseket, és fogadja el a szabályzatot.
3. Kattintson a **új alkalmazás regisztrálása**.
   
    ![Az Amazon webhelyen található egy új alkalmazás regisztrálása](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)
4. Adja meg az alkalmazással kapcsolatos információk (**neve**, **leírás**, és **adatvédelmi nyilatkozat URL-címe**), és kattintson a **mentése**.
   
    ![Az Amazon, egy új alkalmazás regisztrálása az alkalmazással kapcsolatos adatok megadása](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)
5. Az a **Webbeállításainak** területén, az értékek másolásához **ügyfél-azonosító** és **titkos Ügyfélkód**. (Akkor rá kell kattintania a **Show Secret** gombra a megtekintéséhez.) Mindkettő Amazon konfigurálása identitás-szolgáltatóként a bérlő van szüksége. Kattintson a **szerkesztése** szakasz alján. **Titkos Ügyfélkód** egy fontos biztonsági hitelesítő adat.
   
    ![Ügyfél-azonosító és titkos Ügyfélkód megadása Amazon, az új alkalmazás](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)
6. Adja meg `https://login.microsoftonline.com` a a **JavaScript engedélyezett eredetek** mező és `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` a a **vissza URL-címek engedélyezett** mező. Cserélje le **{tenant}** a bérlő nevét (például contoso.onmicrosoft.com). Kattintson a **Save** (Mentés) gombra. A **{tenant}** értéke a kis-és nagybetűket.
   
    ![JavaScript-források és vissza URL-címek megadása az Amazon, az új alkalmazás](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## <a name="configure-amazon-as-an-identity-provider-in-your-tenant"></a>A bérlő Identitásszolgáltatóként Amazon konfigurálása
1. Az alábbi lépéseket követve [lépjen a B2C funkciók paneljére](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure Portalon.
2. Kattintson a B2C funkciók panelje **Identitásszolgáltatók**.
3. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
4. Adjon meg egy rövid **neve** az identitás szolgáltató a konfigurációhoz. Adja meg például a "Amzn".
5. Kattintson a **identitásszolgáltató típusa**válassza **Amazon**, és kattintson a **OK**.
6. Kattintson a **az identitásszolgáltató beállítása** és adja meg az ügyfél-azonosító és ügyfélkulcs az Amazon-alkalmazás, amelyet korábban hozott létre.
7. Kattintson a **OK** majd **létrehozás** az Amazon-konfiguráció mentéséhez.

