---
title: WeChat konfigurálása az Azure Active Directory B2C |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés a az Azure Active Directory B2C által védett alkalmazások WeChat fiókkal rendelkező felhasználók számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: a18d41a4f45b147790a17664156659d282e710d4
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445950"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-wechat-accounts"></a>Az Azure Active Directory B2C: Adja meg előfizetési, és jelentkezzen be a WeChat fiókkal rendelkező felhasználók számára

> [!NOTE]
> Ez a funkció előzetes verzióban érhető el.
> 

## <a name="create-a-wechat-application"></a>WeChat alkalmazás létrehozása

WeChat használata Identitásszolgáltatóként az Azure Active Directory (Azure AD) B2C-ben, meg kell WeChat alkalmazás létrehozása, és adja meg azt a megfelelő paraméterekkel. Ehhez egy WeChat fiók szükséges. Ha még nincs fiókja, beszerezheti az egyik a mobilalkalmazások egyik keresztül regisztráló vagy a Gyorsműveletek szám használatával. Ezt követően a fiók regisztrálva a WeChat developer-program beolvasása. További információt talál [Itt](http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Egy WeChat alkalmazás regisztrálása

1. Lépjen a [https://open.weixin.qq.com/](https://open.weixin.qq.com/) webhelyre, és jelentkezzen be.
2. Kattintson a**管理中心**(felügyeleti központ).
3. Hajtsa végre a szükséges lépéseket egy új alkalmazás regisztrálásához.
4. A**授权回调域**(visszahívási URL-cím), adja meg `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Például ha a `tenant_name` van contoso.onmicrosoft.com, állítsa be az URL-címet kell `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
5. Keresse meg és másolja a **Alkalmazásazonosító** és **ALKALMAZÁSKULCS**. Ezekre később lesz szüksége.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>WeChat konfigurálása identitás-szolgáltatóként a bérlőben
1. Az alábbi lépéseket követve [lépjen a B2C funkciók paneljére](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure Portalon.
2. Kattintson a B2C funkciók panelje **Identitásszolgáltatók**.
3. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
4. Adjon meg egy rövid **neve** az identitás szolgáltató a konfigurációhoz. Adja meg például a "WeChat".
5. Kattintson a **identitásszolgáltató típusa**válassza **WeChat**, és kattintson a **OK**.
6. Kattintson a **az identitásszolgáltató beállítása**
7. Adja meg a **Alkalmazáskulcs** , mint korábban másolt a **ügyfél-azonosító**.
8. Adja meg a **titkos Alkalmazáskulcs** , mint korábban másolt a **titkos Ügyfélkód**.
9. Kattintson a **OK** majd **létrehozás** a WeChat konfigurációjának mentéséhez.

