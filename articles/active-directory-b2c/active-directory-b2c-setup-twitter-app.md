---
title: Az Azure AD B2C Twitter konfigurációja |} Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés az Azure Active Directory B2C által védett alkalmazások Twitter fiókkal rendelkező felhasználók számára.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 4/17/2018
ms.author: davidmu
ms.openlocfilehash: 40e4c5549414765dabc6f37c5ffb5aea519ae673
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts-using-azure-ad-b2c"></a>Twitter-fiókok Azure AD B2C segítségével fogyasztók regisztráció és bejelentkezés biztosítása

## <a name="create-a-twitter-application"></a>Twitter-alkalmazás létrehozása
Az Azure Active Directory (Azure AD) B2C identitás-szolgáltatóként Twitter használatához szüksége Twitter-alkalmazás létrehozása, és adja meg azt a megfelelő paraméterekkel. Ehhez Twitter-fiók szükséges. Ha még nincs fiókja, beszerezheti a [ https://twitter.com/signup ](https://twitter.com/signup).

1. Lépjen a [Twitter alkalmazások](https://apps.twitter.com/) és jelentkezzen be a hitelesítő adatait.
2. Kattintson a **új alkalmazás létrehozása**. 
3. Ebben a formátumban adja meg az értéket a **neve**, **leírás**, és **webhely**.
4. Az a **visszahívási URL-cím**, adja meg `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Győződjön meg arról, hogy **{tenant}** a bérlő nevű (például contosob2c.onmicrosoft.com).
5. A jelölőnégyzet bejelölésével vállalja, hogy a **fejlesztői megállapodás** kattintson **az Twitter-alkalmazás létrehozása**.
6. Az alkalmazás létrehozása után kattintson **kulcsok és a hozzáférési jogkivonatok**.
7. Másolja a értékének **kulcsa** és **felhasználói titok**. Mindkettő konfigurálásához Twitter-bérlőben identitás-szolgáltatóként kell.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Twitter-bérlőben identitás-szolgáltatóként konfigurálása
1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként. 
2. Váltson át az Azure AD B2C-bérlő, válassza ki az Azure AD B2C-címtárat a portál jobb felső sarkában.
3. Kattintson a **minden szolgáltatás**, majd válassza ki **az Azure AD B2C** alatti szolgáltatások listából **biztonság + identitás szakaszában**.
4. Kattintson a **identitás-szolgáltatóktól**.
4. Adjon meg egy rövid **neve** a az identitás-szolgáltató konfigurációját. Adja meg például "Twitter".
5. Kattintson a **identitás szolgáltatótípus**, jelölje be **Twitter (előzetes verzió)**, és kattintson a **OK**.
6. Kattintson a **az identitásszolgáltató beállítása** , és írja be a Twitteren **kulcsa** a a **ügyfél-azonosító** és a Twitter **felhasználói titok** a a **ügyfélkulcs**.
7. Kattintson a **OK**, és kattintson a **létrehozása** a Twitter-konfiguráció mentéséhez.

## <a name="next-steps"></a>További lépések

Hozzon létre vagy szerkessze a [beépített házirend](active-directory-b2c-reference-policies.md) , és adja hozzá a Twitter identitás-szolgáltatóként.