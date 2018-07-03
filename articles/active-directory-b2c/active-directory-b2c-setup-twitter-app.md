---
title: Twitter-konfiguráció az Azure Active Directory B2C |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés az Azure Active Directory B2C által védett alkalmazások Twitter-fiókkal rendelkező ügyfelek számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 6/13/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: dad35f26496306558a6e0105db86321c497a8306
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37342933"
---
# <a name="provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts-using-azure-ad-b2c"></a>Regisztráció és bejelentkezés a fogyasztók számára biztosítanak a Twitter-fiókokkal, az Azure AD B2C használatával

## <a name="create-a-twitter-application"></a>Twitter-alkalmazás létrehozása

Twitter használata Identitásszolgáltatóként az Azure Active Directory (Azure AD) B2C-ben, meg kell Twitter-alkalmazás létrehozása, és adja meg azt a megfelelő paraméterekkel. Ehhez egy Twitter-fiók szükséges. Ha még nincs fiókja, beszerezheti a [ https://twitter.com/signup ](https://twitter.com/signup).

1. Nyissa meg a [Twitter-alkalmazások](https://apps.twitter.com/) , és jelentkezzen be a hitelesítő adataival.
2. Kattintson a **új alkalmazás létrehozása**.
3. A képernyőn adjon meg egy értéket a **neve**, **leírás**, és **webhely**.
4. Az a **visszahívási URL-Címének**, adja meg `https://login.microsoftonline.com/te/{tenant}/{policyId}/oauth1/authresp`. Cserélje le **{tenant}** a bérlő nevét (például: contosob2c.onmicrosoft.com) és {policyId} a házirend-azonosítójú (például b2c_1_policy).  A visszahívási URL-címet kell lennie az összes kisbetűt. Minden szabályzat, amely a Twitter-bejelentkezés használatára egy visszahívási URL-címet hozzá kell adnia. Győződjön meg arról, hogy használja `b2clogin.com` helyett ` login.microsoftonline.com` Ha az alkalmazás használ.
5. A jelölőnégyzet bejelölésével vállalja, hogy a **fejlesztői szerződés** kattintson **Twitter-alkalmazás létrehozása**.
6. Az alkalmazás létrehozása után válassza ki a listából, válassza ki a **beállítások** fülre, majd **beállításainak frissítése**.
7. Válassza ki a **kulcsok és hozzáférési tokenek** fülre.
8. Másolja az értéket a **fogyasztói kulcs** és **fogyasztói titkos kulcs**. Szüksége lesz mindkettő Twitter konfigurálása identitás-szolgáltatóként a bérlőben.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>A bérlő Identitásszolgáltatóként Twitter konfigurálása

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként. 
2. Váltson át az Azure AD B2C-bérlő, a portál jobb felső sarkában válassza ki az Azure AD B2C-címtárát.
3. Kattintson a **minden szolgáltatás**, majd válassza ki **Azure AD B2C-vel** elemet a szolgáltatáslistából alatt **biztonság + identitás**.
4. Kattintson a **Identitásszolgáltatók**.
5. Adjon meg egy rövid **neve** az identitás szolgáltató a konfigurációhoz. Adja meg például "Twitter".
6. Kattintson a **identitásszolgáltató típusa**válassza **Twitter (előzetes verzió)**, és kattintson a **OK**.
7. Kattintson a **az identitásszolgáltató beállítása** , és írja be a Twitter **fogyasztói kulcs** számára a **ügyfél-azonosító** és a Twitter **fogyasztói titkos kulcs** számára a **titkos Ügyfélkód**.
8. Kattintson a **OK**, és kattintson a **létrehozás** kattintva mentse a Twitter-konfigurációt.

## <a name="next-steps"></a>További lépések

Hozzon létre vagy szerkessze a [beépített szabályzat](active-directory-b2c-reference-policies.md) , és adja hozzá a Twitter identitás-szolgáltatóként.