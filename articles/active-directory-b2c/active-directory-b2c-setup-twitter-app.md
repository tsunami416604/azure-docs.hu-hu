---
title: Twitter-fiókkal való regisztráció és bejelentkezés beállítása Azure Active Directory B2C használatával
description: Az alkalmazások Twitter-fiókkal való regisztrációját és bejelentkezését Azure Active Directory B2C használatával biztosíthatja az ügyfeleknek.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ff795bbbd3cf136735499b571367cf5b8a6ec170
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622152"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Twitter-fiókkal való regisztráció és bejelentkezés beállítása Azure Active Directory B2C használatával

## <a name="create-an-application"></a>Alkalmazás létrehozása

Ha a Twittert identitás-szolgáltatóként szeretné használni Azure AD B2Cban, létre kell hoznia egy Twitter-alkalmazást. Ha még nem rendelkezik Twitter-fiókkal, regisztrálhat a következő címen: [https://twitter.com/signup](https://twitter.com/signup).

1. Jelentkezzen be a [Twitter-fejlesztők](https://developer.twitter.com/en/apps) webhelyére a Twitter-fiókja hitelesítő adataival.
1. Válassza **az alkalmazás létrehozása**lehetőséget.
1. Adja meg az alkalmazás **nevét** és **leírását**.
1. A **webhely URL**-címe `https://your-tenant.b2clogin.com`mezőbe írja be a értéket. Cserélje `your-tenant` le a helyére a bérlő nevét. Például: https://contosob2c.b2clogin.com.
1. A **visszahívás URL**-címéhez `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`írja be a következőt:. Cserélje `your-tenant` le a nevet a bérlő `your-user-flow-Id` nevére, a felhasználói folyamat azonosítójával együtt. Például: `b2c_1A_signup_signin_twitter`. A bérlő nevének megadásakor az összes kisbetűt kell használnia, még akkor is, ha a bérlőt nagybetűvel definiálták Azure AD B2C.
1. A lap alján olvassa el és fogadja el a feltételeket, majd válassza a **Létrehozás**lehetőséget.
1. Az **alkalmazás részletei** lapon válassza a **Szerkesztés > a részletek szerkesztése**lehetőséget, jelölje be a **bejelentkezés engedélyezése**a Twitteren jelölőnégyzetet, majd kattintson a **Mentés**gombra.
1. Válassza a **kulcsok és** jogkivonatok lehetőséget, és jegyezze fel a **fogyasztói API-kulcsot** és a **fogyasztói API titkos kulcsának** értékeit, amelyeket később szeretne használni.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>A Twitter konfigurálása identitás-szolgáltatóként a bérlőben

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza az **identitás-szolgáltatók**, majd a **Twitter**lehetőséget.
1. Adjon meg egy **nevet**. Például: *Twitter*.
1. Az **ügyfél-azonosító**mezőben adja meg a korábban létrehozott Twitter-alkalmazás fogyasztói API-kulcsát.
1. Az **ügyfél titkos**kulcsa mezőben adja meg a rögzített fogyasztói API titkos kulcsot.
1. Kattintson a **Mentés** gombra.
