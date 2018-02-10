---
title: "Az Azure Active Directory B2C: Twitter konfigurációs |} Microsoft Docs"
description: "Adja meg a regisztráció és bejelentkezés az Azure Active Directory B2C által védett alkalmazások Twitter fiókkal rendelkező felhasználók számára."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: 579a6841-9329-45b8-a351-da4315a6634e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/06/2017
ms.author: parakhj
ms.openlocfilehash: 4dbc70d866a728a63102bd169a124bd26990aeb2
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts"></a>Az Azure Active Directory B2C: Regisztráció és bejelentkezés adhat Twitter-fiókkal rendelkező felhasználók

## <a name="create-a-twitter-application"></a>Twitter-alkalmazás létrehozása
Az Azure Active Directory (Azure AD) B2C identitás-szolgáltatóként Twitter használatához szüksége Twitter-alkalmazás létrehozása, és adja meg azt a megfelelő paraméterekkel. Ehhez Twitter developer-fiók szükséges. Ha még nincs fiókja, beszerezheti a [https://dev.twitter.com/](https://dev.twitter.com/).

1. Lépjen a [fejlesztői webhely Twitter](https://dev.twitter.com/) és jelentkezzen be a hitelesítő adatait.
2. Kattintson a **alkalmazásaimat** alatt **eszközök & támogatási** , majd **új alkalmazás létrehozása**. 
3. Ebben a formátumban adja meg az értéket a **neve**, **leírás**, és **webhely**.
4. Az a **visszahívási URL-cím**, adja meg `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Győződjön meg arról, hogy **{tenant}** a bérlő nevű (például contosob2c.onmicrosoft.com).
5. A jelölőnégyzet bejelölésével vállalja, hogy a **fejlesztői megállapodás** kattintson **az Twitter-alkalmazás létrehozása**.
6. Az alkalmazás létrehozása után kattintson **kulcsok és a hozzáférési jogkivonatok**.
7. Másolja a értékének **kulcsa** és **felhasználói titok**. Mindkettő konfigurálásához Twitter-bérlőben identitás-szolgáltatóként kell.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Twitter-bérlőben identitás-szolgáltatóként konfigurálása
1. Az alábbi lépéseket követve [lépjen a B2C funkciók panelje](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure portálon.
2. Kattintson a B2C funkciók panelje **identitás-szolgáltatóktól**.
3. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
4. Adjon meg egy rövid **neve** a az identitás-szolgáltató konfigurációját. Adja meg például "Twitter".
5. Kattintson a **identitás szolgáltatótípus**, jelölje be **Twitter**, és kattintson a **OK**.
6. Kattintson a **az identitásszolgáltató beállítása** , és írja be a Twitteren **kulcsa** a a **ügyfél-azonosító** és a Twitter **felhasználói titok** a a **ügyfélkulcs**.
7. Kattintson a **OK**, és kattintson a **létrehozása** a Twitter-konfiguráció mentéséhez.

## <a name="next-steps"></a>További lépések

Hozzon létre vagy szerkessze a [beépített házirend](active-directory-b2c-reference-policies.md) , és adja hozzá a Twitter identitás-szolgáltatóként.