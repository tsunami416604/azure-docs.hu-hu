---
title: Regisztráció és bejelentkezés beállítása WeChat-fiókkal
titleSuffix: Azure AD B2C
description: A Azure Active Directory B2C használatával WeChat-fiókkal rendelkező ügyfelek számára biztosítson regisztrációt és bejelentkezést az alkalmazásokban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c53210939358255b20d0e976df9c4bff88580a80
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184434"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>WeChat-fiókkal történő regisztráció és bejelentkezés beállítása Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-wechat-application"></a>WeChat-alkalmazás létrehozása

Ha a WeChat-fiókot Azure Active Directory B2C (Azure AD B2C) identitás-szolgáltatóként szeretné használni, létre kell hoznia egy alkalmazást a bérlőben, amely azt képviseli. Ha még nem rendelkezik WeChat-fiókkal, a [https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html)címen kaphat információt.

### <a name="register-a-wechat-application"></a>WeChat-alkalmazás regisztrálása

1. Jelentkezzen be [https://open.weixin.qq.com/ra](https://open.weixin.qq.com/) a WeChat hitelesítő adataival.
1. Válassza a**管理中心**(felügyeleti központ) lehetőséget.
1. Új alkalmazás regisztrálásához kövesse az alábbi lépéseket.
1. Adja meg `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` a**授权回调域**(visszahívási URL-cím). Ha például a bérlő neve contoso, a `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`URL-címet állítsa be.
1. Másolja az **alkalmazás azonosítóját** és az **alkalmazás kulcsát**. Ezekre szüksége lesz az identitás-szolgáltatónak a bérlőhöz való hozzáadásához.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>A WeChat konfigurálása identitás-szolgáltatóként a bérlőben

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza az **identitás-szolgáltatók**, majd a **WeChat (előzetes verzió)** lehetőséget.
1. Adjon meg egy **nevet**. Például: *WeChat*.
1. Az **ügyfél-azonosító**mezőben adja meg a korábban létrehozott WeChat-alkalmazás alkalmazás-azonosítóját.
1. Az **ügyfél titkos**kulcsa mezőben adja meg a rögzített alkalmazás kulcsát.
1. Kattintson a **Mentés** gombra.
