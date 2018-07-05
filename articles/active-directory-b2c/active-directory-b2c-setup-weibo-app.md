---
title: Az Azure Active Directory B2C Weibo konfigurációs |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés a az Azure Active Directory B2C által védett alkalmazások Weibo fiókkal rendelkező felhasználók számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: bfd7dde290bd040f8457e6d095fdf896e802764b
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444789"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-weibo-accounts"></a>Az Azure Active Directory B2C: Adja meg előfizetési, és jelentkezzen be a Weibo fiókkal rendelkező felhasználók számára

> [!NOTE]
> Ez a funkció előzetes verzióban érhető el. Az éles környezetben ne használja az identitásszolgáltató.
> 

## <a name="create-a-weibo-application"></a>Weibo alkalmazás létrehozása

Identitás-szolgáltatóként az Azure Active Directory (Azure AD) B2C Weibo használatához kell Weibo alkalmazás létrehozása, és adja meg azt a megfelelő paraméterekkel. Ehhez egy Weibo fiók szükséges. Ha még nincs fiókja, beszerezheti a egyenként [ http://weibo.com/signup/signup.php?lang=en-us ](http://weibo.com/signup/signup.php?lang=en-us).

### <a name="register-for-the-weibo-developer-program"></a>Regisztráljon a Weibo developer-program

1. Nyissa meg a [Weibo fejlesztői portál](http://open.weibo.com/) , és jelentkezzen be a Weibo fiók hitelesítő adatait.
2. Miután bejelentkezett, kattintson a jobb felső sarokban a megjelenítési név.
3. A legördülő listában válassza ki a**编辑开发者信息**(fejlesztői adatok szerkesztése).
4. Adja meg a szükséges információkat az űrlapon, és kattintson a**提交**(beküldése).
5. Fejezze be az e-mail ellenőrzésének folyamatát.
6. Nyissa meg a [identitás-ellenőrzési lapot](http://open.weibo.com/developers/identity/edit).
7. Adja meg a szükséges információkat az űrlapon, és kattintson a**提交**(beküldése).

### <a name="register-a-weibo-application"></a>Egy Weibo alkalmazás regisztrálása

1. Nyissa meg a [új Weibo alkalmazásregisztrációs oldalán](http://open.weibo.com/apps/new).
2. Adja meg a szükséges alkalmazás adatait.
3. Kattintson a**创建**(létrehozás).
4. Másolja le az értékeket a **Alkalmazáskulcs** és **titkos Alkalmazáskulcs**. Ez később lesz szüksége.
5. A szükséges fényképek feltöltése, és adja meg a szükséges információkat.
6. Kattintson a**保存以上信息**(Mentés).
7. Kattintson a**高级信息**(speciális információk).
8. Kattintson a**编辑**(szerkesztési) mező mellett a OAuth2.0**授权设置**(átirányítási URL-cím).
9. Adja meg `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp` a OAuth2.0**授权设置**(átirányítási URL-cím). Például ha a `tenant_name` van contoso.onmicrosoft.com, állítsa be az URL-címet kell `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
10. Kattintson a**提交**(beküldése).  

## <a name="configure-weibo-as-an-identity-provider-in-your-tenant"></a>A bérlő Identitásszolgáltatóként Weibo konfigurálása
1. Az alábbi lépéseket követve [lépjen a B2C funkciók paneljére](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure Portalon.
2. Kattintson a B2C funkciók panelje **Identitásszolgáltatók**.
3. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
4. Adjon meg egy rövid **neve** az identitás szolgáltató a konfigurációhoz. Adja meg például a "Weibo".
5. Kattintson a **identitásszolgáltató típusa**válassza **Weibo**, és kattintson a **OK**.
6. Kattintson a **az identitásszolgáltató beállítása**
7. Adja meg a **Alkalmazáskulcs** , mint korábban másolt a **ügyfél-azonosító**.
8. Adja meg a **titkos Alkalmazáskulcs** , mint korábban másolt a **titkos Ügyfélkód**.
9. Kattintson a **OK** majd **létrehozás** a Weibo konfigurációjának mentéséhez.

