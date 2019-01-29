---
title: Regisztráció és bejelentkezés a az Azure Active Directory B2C fiókkal WeChat beállítása |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés WeChat az alkalmazások az Azure Active Directory B2C fiókkal rendelkező ügyfelek számára.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 9c9f6b930c5173e97e29148270dedc7eb086ef5a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159675"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés a az Azure Active Directory B2C fiókkal WeChat beállítása

> [!NOTE]
> Ez a funkció előzetes verzióban érhető el.
> 

## <a name="create-a-wechat-application"></a>WeChat alkalmazás létrehozása

WeChat fiók használatára az identitásszolgáltatójaként az Azure Active Directory (Azure AD) B2C-ben, hozzon létre egy alkalmazást a bérlőben, hogy az azt jelölő kell. Egy WeChat fiók még nem rendelkezik, ha információkat szerezhet a következő [ https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html ](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Egy WeChat alkalmazás regisztrálása

1. Jelentkezzen be a [ https://open.weixin.qq.com/ ](https://open.weixin.qq.com/) WeChat hitelesítő adataival.
2. Válassza ki**管理中心**(felügyeleti központ).
3. Kövesse a lépéseket egy új alkalmazás regisztrálásához.
4. Adja meg `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` a**授权回调域**(visszahívási URL-cím). Például ha a bérlő neve contoso, állítsa az URL-címet kell `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
5. Másolás a **Alkalmazásazonosító** és **ALKALMAZÁSKULCS**. Ezek az identitásszolgáltató hozzáadása a bérlőhöz kell.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>WeChat konfigurálása identitás-szolgáltatóként a bérlőben

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
5. Adjon meg egy **neve**. Adja meg például *WeChat*.
6. Válassza ki **identitásszolgáltató típusa**válassza **WeChat (előzetes verzió)**, és kattintson a **OK**.
7. Válassza ki **az identitásszolgáltató beállítása** , és adja meg az alkalmazás azonosítója, mint korábban rögzített a **ügyfél-azonosító** , és írja be az ALKALMAZÁSKULCS, feljegyzett a **titkos Ügyfélkód** , a WeChat alkalmazás, amelyet korábban hozott létre.
8. Kattintson a **OK** majd **létrehozás** a WeChat konfigurációjának mentéséhez.

