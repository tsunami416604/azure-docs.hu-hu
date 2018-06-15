---
title: Az Azure Active Directory B2C Weibo konfigurációs |} Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés az Azure Active Directory B2C által védett alkalmazások Weibo fiókkal rendelkező felhasználók számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 3/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c11931b0b1c568591a89277844722dceeac59039
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711467"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-weibo-accounts"></a>Az Azure Active Directory B2C: Regisztráció és bejelentkezés adhat Weibo fiókkal rendelkező felhasználók

> [!NOTE]
> Ez a funkció előzetes verzióban érhető el. Ne használja az identitásszolgáltató az éles környezetben.
> 

## <a name="create-a-weibo-application"></a>Weibo-alkalmazás létrehozása

Az Azure Active Directory (Azure AD) B2C identitás-szolgáltatóként Weibo használatához szüksége Weibo-alkalmazás létrehozása, és adja meg azt a megfelelő paraméterekkel. Ehhez Weibo fiók szükséges. Ha még nincs fiókja, akkor kaphat egyenként [ http://weibo.com/signup/signup.php?lang=en-us ](http://weibo.com/signup/signup.php?lang=en-us).

### <a name="register-for-the-weibo-developer-program"></a>Regisztrálja a Weibo fejlesztői programhoz

1. Lépjen a [Weibo fejlesztői portálján](http://open.weibo.com/) és jelentkezzen be a Weibo fiók hitelesítő adataival.
2. Történő bejelentkezés után kattintson a jobb felső sarokban a megjelenített név.
3. A legördülő listában jelölje ki**编辑开发者信息**(fejlesztői adatainak szerkesztése).
4. Adja meg a szükséges adatokat az űrlapon, és kattintson a**提交**(küldje el).
5. Végezze el az e-mailek ellenőrzési folyamata.
6. Lépjen a [identitás ellenőrzése lapon](http://open.weibo.com/developers/identity/edit).
7. Adja meg a szükséges adatokat az űrlapon, és kattintson a**提交**(küldje el).

### <a name="register-a-weibo-application"></a>Egy Weibo alkalmazás regisztrálása

1. Lépjen a [új Weibo regisztrációs oldalra](http://open.weibo.com/apps/new).
2. Adja meg a szükséges alkalmazás adatokat.
3. Kattintson a**创建**(létrehozása).
4. Értékeinek másolására **Alkalmazáskulcs** és **alkalmazás titkos kulcs**. Ezt később lesz szüksége.
5. Töltse fel a szükséges fényképek, és adja meg a szükséges adatokat.
6. Kattintson a**保存以上信息**(Mentés).
7. Kattintson a**高级信息**(speciális információk).
8. Kattintson a**编辑**(Szerkesztés) mező mellett a OAuth2.0**授权设置**(átirányítási URL-cím).
9. Adja meg `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp` a OAuth2.0**授权设置**(átirányítási URL-cím). Például ha a `tenant_name` van contoso.onmicrosoft.com, állítsa be az URL-címet kell `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
10. Kattintson a**提交**(küldje el).  

## <a name="configure-weibo-as-an-identity-provider-in-your-tenant"></a>A bérlő az identitás-szolgáltatóként Weibo konfigurálása
1. Az alábbi lépéseket követve [lépjen a B2C funkciók panelje](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure portálon.
2. Kattintson a B2C funkciók panelje **identitás-szolgáltatóktól**.
3. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
4. Adjon meg egy rövid **neve** a az identitás-szolgáltató konfigurációját. Írja be például a "Weibo".
5. Kattintson a **identitás szolgáltatótípus**, jelölje be **Weibo**, és kattintson a **OK**.
6. Kattintson a **az identitásszolgáltató beállítása**
7. Adja meg a **Alkalmazáskulcs** korábban kimásolt a **ügyfél-azonosító**.
8. Adja meg a **alkalmazás titkos kulcs** korábban kimásolt a **Ügyfélkulcs**.
9. Kattintson a **OK** majd **létrehozása** a Weibo konfiguráció mentéséhez.

