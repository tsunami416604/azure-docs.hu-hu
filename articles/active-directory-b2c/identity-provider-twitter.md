---
title: Regisztráció és bejelentkezés beállítása Twitter-fiókkal
titleSuffix: Azure AD B2C
description: Az Azure Active Directory B2C használatával twitteres fiókkal rendelkező ügyfeleknek adhat meg regisztrációt és bejelentkezést az alkalmazásokban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 62a283efb93987d3c4a6564c9b25d2031c269559
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051471"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés beállítása Twitter-fiókkal az Azure Active Directory B2C használatával

## <a name="create-an-application"></a>Alkalmazás létrehozása

A Twitter identitásszolgáltatóként való használatához az Azure AD B2C-ben létre kell hoznia egy Twitter-alkalmazást. Ha még nem rendelkezik Twitter-fiókkal, regisztrálhat a következő helyen: [https://twitter.com/signup](https://twitter.com/signup).

1. Jelentkezzen be a [Twitter Developers](https://developer.twitter.com/en/apps) webhelyére a Twitter-fiókjának hitelesítő adataival.
1. Válassza **az Alkalmazás létrehozása**lehetőséget.
1. Adja meg **az alkalmazás nevét** és az alkalmazás **leírását.**
1. A **webhely URL-címében**adja meg a . `https://your-tenant.b2clogin.com` Cserélje `your-tenant` le a bérlő nevét. Például: `https://contosob2c.b2clogin.com`.
1. A **visszahívás url-címéhez**írja be a . `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp` Cserélje `your-tenant` le a bérlő nevét `your-user-flow-Id` és a felhasználói folyamat azonosítóját. Például: `b2c_1A_signup_signin_twitter`. A bérlő nevének és a felhasználói folyamat azonosítójának megadásakor minden kisbetűt használnia kell, még akkor is, ha azok nagybetűkkel vannak definiálva az Azure AD B2C-ben.
1. A lap alján olvassa el és fogadja el a feltételeket, majd válassza a **Létrehozás gombot.**
1. Az **Alkalmazás részletei** lapon válassza a **Szerkesztés > A részletek szerkesztése**jelölőnégyzetet, jelölje be a Bejelentkezés engedélyezése a **Twitterrel**jelölőnégyzetet, majd kattintson a **Mentés gombra.**
1. Válassza ki **a kulcsok at és jogkivonatokat,** és rögzítse a consumer **API-kulcs** és a **consumer API titkos kulcs** értékeit később használni.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>A Twitter konfigurálása identitásszolgáltatóként a bérlőben

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza **az Identitásszolgáltatók**lehetőséget, majd a **Twitter**lehetőséget.
1. Írjon be egy **nevet**. Például a *Twitter*.
1. Az **ügyfélazonosító**hoz adja meg a korábban létrehozott Twitter-alkalmazás fogyasztói API-kulcsát.
1. Az **ügyféltitkos kulcsot**adja meg a fogyasztói API titkos kulcs, amely rögzített.
1. Kattintson a **Mentés** gombra.
