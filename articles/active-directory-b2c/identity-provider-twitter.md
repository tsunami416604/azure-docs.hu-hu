---
title: Regisztráció és bejelentkezés beállítása Twitter-fiókkal
titleSuffix: Azure AD B2C
description: Az alkalmazások Twitter-fiókkal való regisztrációját és bejelentkezését Azure Active Directory B2C használatával biztosíthatja az ügyfeleknek.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd538529756270053351e4c2d85f5761b198eeff
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78187882"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Twitter-fiókkal való regisztráció és bejelentkezés beállítása Azure Active Directory B2C használatával

## <a name="create-an-application"></a>Alkalmazás létrehozása

Ha a Twittert identitás-szolgáltatóként szeretné használni Azure AD B2Cban, létre kell hoznia egy Twitter-alkalmazást. Ha még nem rendelkezik Twitter-fiókkal, regisztrálhat [https://twitter.com/signup](https://twitter.com/signup)címen.

1. Jelentkezzen be a [Twitter-fejlesztők](https://developer.twitter.com/en/apps) webhelyére a Twitter-fiókja hitelesítő adataival.
1. Válassza **az alkalmazás létrehozása**lehetőséget.
1. Adja meg az alkalmazás **nevét** és **leírását**.
1. A **webhely URL-címe**mezőbe írja be a `https://your-tenant.b2clogin.com`. Cserélje le a `your-tenant`t a bérlő nevére. Például: https://contosob2c.b2clogin.com.
1. A **visszahívási URL-cím**mezőbe írja be a `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`. Cserélje le a `your-tenant`t a bérlő nevére, és `your-user-flow-Id` a felhasználói folyamat azonosítójával. Például: `b2c_1A_signup_signin_twitter`. Az összes kisbetűs betűt kell használnia, ha a bérlő nevét és a felhasználói folyamat azonosítóját akkor is meg kell adnia, ha nagybetűvel vannak meghatározva a Azure AD B2Cban.
1. A lap alján olvassa el és fogadja el a feltételeket, majd válassza a **Létrehozás**lehetőséget.
1. Az **alkalmazás részletei** lapon válassza a **Szerkesztés > a részletek szerkesztése**lehetőséget, jelölje be a **bejelentkezés engedélyezése a Twitteren**jelölőnégyzetet, majd kattintson a **Mentés**gombra.
1. Válassza a **kulcsok és jogkivonatok** lehetőséget, és jegyezze fel a **fogyasztói API-kulcsot** és a **fogyasztói API titkos kulcsának** értékeit, amelyeket később szeretne használni.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>A Twitter konfigurálása identitás-szolgáltatóként a bérlőben

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza az **identitás-szolgáltatók**, majd a **Twitter**lehetőséget.
1. Adjon meg egy **nevet**. Például: *Twitter*.
1. Az **ügyfél-azonosító**mezőben adja meg a korábban létrehozott Twitter-alkalmazás fogyasztói API-kulcsát.
1. Az **ügyfél titkos**kulcsa mezőben adja meg a rögzített fogyasztói API titkos kulcsot.
1. Kattintson a **Mentés** gombra.
