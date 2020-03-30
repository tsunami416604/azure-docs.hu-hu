---
title: Regisztráció és bejelentkezés beállítása WeChat-fiókkal
titleSuffix: Azure AD B2C
description: Az Azure Active Directory B2C használatával WeChat-fiókkal rendelkező ügyfelek nek is biztosíthatja a regisztrációt és a bejelentkezést az alkalmazásokban.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184434"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés beállítása WeChat-fiókkal az Azure Active Directory B2C használatával

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-wechat-application"></a>WeChat alkalmazás létrehozása

A WeChat-fiók identitásszolgáltatóként való használatához az Azure Active Directory B2C (Azure AD B2C) szolgáltatásban létre kell hoznia egy alkalmazást a bérlőben, amely azt képviseli. Ha még nem rendelkezik WeChat-fiókkal, a [https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html)( a ) oldalon kaphat információkat.

### <a name="register-a-wechat-application"></a>WeChat alkalmazás regisztrálása

1. Jelentkezzen be [https://open.weixin.qq.com/](https://open.weixin.qq.com/) WeChat-hitelesítő adataival.
1. Válassza a **(felügyeleti** központ) lehetőséget.
1. Új alkalmazás regisztrálásához kövesse a lépéseket.
1. Írja `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` be **a** következő ta- Ha például a bérlő neve contoso, állítsa `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`be az URL-címet.
1. Másolja az **APP-azonosítót** és az **APP-kulcsot.** Ezekre szüksége lesz az identitásszolgáltató hozzáadása a bérlőhöz.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>A WeChat konfigurálása identitásszolgáltatóként a bérlőben

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza **az Identitásszolgáltatók**lehetőséget, majd a **WeChat (Előzetes verzió)** lehetőséget.
1. Írjon be egy **nevet**. Például: *WeChat*.
1. Az **ügyfélazonosítóhoz**adja meg a korábban létrehozott WeChat alkalmazás APP-azonosítóját.
1. Az **ügyfél titkos kulcsa,** adja meg az APP kulcs, amely rögzített.
1. Kattintson a **Mentés** gombra.
