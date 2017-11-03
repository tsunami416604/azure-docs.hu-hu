---
title: "Az Azure Active Directory B2C: WeChat konfigurációs |} Microsoft Docs"
description: "Adja meg a regisztráció és bejelentkezés az Azure Active Directory B2C által védett alkalmazások WeChat fiókkal rendelkező felhasználók számára."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: d2424c66-ba68-4d82-847e-d137683527b0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2017
ms.author: parakhj
ms.openlocfilehash: a54aec23d951610118246e9f70cdd27752ef39a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-wechat-accounts"></a>Az Azure Active Directory B2C: Regisztráció és bejelentkezés adhat WeChat fiókkal rendelkező felhasználók

> [!NOTE]
> A funkció jelenleg előzetes verzió.
> 

## <a name="create-a-wechat-application"></a>WeChat-alkalmazás létrehozása

Az Azure Active Directory (Azure AD) B2C identitás-szolgáltatóként WeChat használatához szüksége WeChat-alkalmazás létrehozása, és adja meg azt a megfelelő paraméterekkel. Ehhez WeChat fiók szükséges. Ha még nincs fiókja, egyet a mobilalkalmazások egyikével regisztrál vagy a Gyorsműveletek szám használatával kaphat. Ezt követően hozzon létre a fiókot, a WeChat fejlesztőprogrambeli regisztrálva. További információt talál [Itt](http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Egy WeChat alkalmazás regisztrálása

1. Ugrás a [https://open.weixin.qq.com/](https://open.weixin.qq.com/) , és jelentkezzen be.
2. Kattintson a**管理中心**(felügyeleti központ).
3. Hajtsa végre a szükséges lépéseket egy új alkalmazás regisztrálásához.
4. A**授权回调域**(visszahívási URL-cím), adja meg `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Például ha a `tenant_name` van contoso.onmicrosoft.com, állítsa be az URL-címet kell `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
5. Keresse meg és másolja a **Alkalmazásazonosító** és **ALKALMAZÁSKULCS**. Ezekre később kell.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>A bérlő az identitás-szolgáltatóként WeChat konfigurálása
1. Az alábbi lépéseket követve [lépjen a B2C funkciók panelje](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure portálon.
2. Kattintson a B2C funkciók panelje **identitás-szolgáltatóktól**.
3. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
4. Adjon meg egy rövid **neve** a az identitás-szolgáltató konfigurációját. Írja be például a "WeChat".
5. Kattintson a **identitás szolgáltatótípus**, jelölje be **WeChat**, és kattintson a **OK**.
6. Kattintson a **az identitásszolgáltató beállítása**
7. Adja meg a **Alkalmazáskulcs** korábban kimásolt a **ügyfél-azonosító**.
8. Adja meg a **alkalmazás titkos kulcs** korábban kimásolt a **Ügyfélkulcs**.
9. Kattintson a **OK** majd **létrehozása** a WeChat konfiguráció mentéséhez.

