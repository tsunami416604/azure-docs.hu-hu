---
title: Gyorsműveletek konfigurálása az Azure Active Directory B2C |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés a az Azure Active Directory B2C által védett alkalmazások Gyorsműveletek fiókkal rendelkező felhasználók számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/26/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1f9a0f56158f08dd3b22078f111c9ec6911b726c
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444429"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-qq-accounts"></a>Az Azure Active Directory B2C: Adja meg előfizetési, és jelentkezzen be a Gyorsműveletek fiókkal rendelkező felhasználók számára

> [!NOTE]
> Ez a funkció előzetes verzióban érhető el.
> 

## <a name="create-a-qq-application"></a>Gyorsműveletek alkalmazás létrehozása

Gyorsműveletek használata Identitásszolgáltatóként az Azure Active Directory (Azure AD) B2C-vel, meg kell Gyorsműveletek alkalmazás létrehozása, és adja meg azt a megfelelő paraméterekkel. Ehhez egy Gyorsműveletek fiók szükséges. Ha még nincs fiókja, beszerezheti a egyenként [ https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033 ](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Regisztráljon a Gyorsműveletek developer-program

1. Nyissa meg a [Gyorsműveletek fejlesztői portál](http://open.qq.com) , és jelentkezzen be a Gyorsműveletek fiók hitelesítő adatait.
2. Miután bejelentkezett, lépjen [ http://open.qq.com/reg ](http://open.qq.com/reg) fejlesztőként regisztrálja magát.
3. Válassza a menüben**个人**(az egyéni fejlesztői).
4. Adja meg a szükséges információkat az űrlapon, és kattintson a**下一步**(a következő lépésre).
5. Fejezze be az e-mail ellenőrzésének folyamatát.

> [!NOTE]
> Jóváhagyásra váró fejlesztőként a regisztrálás után néhány napot várni kell. 

### <a name="register-a-qq-application"></a>Egy Gyorsműveletek alkalmazás regisztrálása

1. Nyissa meg a következőt: [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
2. Kattintson a**应用管理**(felügyeleti).
3. Kattintson a**创建应用**(alkalmazás létrehozása).
4. Adja meg a szükséges alkalmazásadatok.
5. Kattintson a**创建应用**(alkalmazás létrehozása).
6. Adja meg a szükséges adatokat.
7. Az a**授权回调域**(visszahívási URL-cím) mezőbe írja be `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Például ha a `tenant_name` van contoso.onmicrosoft.com, állítsa be az URL-címet kell `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
8. Kattintson a**创建应用**(alkalmazás létrehozása).
9. A megerősítő oldalon kattintson a**应用管理**(Alkalmazáskezelés) az alkalmazás felügyeleti lapra való visszatéréshez.
10. Kattintson a**查看**(megtekintése) mellett az imént létrehozott alkalmazást.
11. Kattintson a**修改**(Szerkesztés).
12. A lap tetején, másolja a **Alkalmazásazonosító** és **ALKALMAZÁSKULCS**.

## <a name="configure-qq-as-an-identity-provider-in-your-tenant"></a>Gyorsműveletek konfigurálása identitás-szolgáltatóként a bérlőben
1. Az alábbi lépéseket követve [lépjen a B2C funkciók paneljére](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure Portalon.
2. Kattintson a B2C funkciók panelje **Identitásszolgáltatók**.
3. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
4. Adjon meg egy rövid **neve** az identitás szolgáltató a konfigurációhoz. Adja meg például a "Gyorsműveletek".
5. Kattintson a **identitásszolgáltató típusa**válassza **Gyorsműveletek**, és kattintson a **OK**.
6. Kattintson a **az identitásszolgáltató beállítása**
7. Adja meg a **Alkalmazáskulcs** , mint korábban másolt a **ügyfél-azonosító**.
8. Adja meg a **titkos Alkalmazáskulcs** , mint korábban másolt a **titkos Ügyfélkód**.
9. Kattintson a **OK** majd **létrehozás** a Gyorsműveletek konfigurációjának mentéséhez.

