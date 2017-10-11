---
title: "Az Azure Active Directory B2C: Gyorsműveletek konfigurációs |} Microsoft Docs"
description: "Adja meg a regisztráció és bejelentkezés az Azure Active Directory B2C által védett alkalmazások Gyorsműveletek fiókkal rendelkező felhasználók számára."
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 18c2cf94-8004-4de1-81c2-e45be65ce12d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2017
ms.author: parakhj
ms.openlocfilehash: b32e81494b8c84799485f154ae43ad30af394caa
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-qq-accounts"></a>Az Azure Active Directory B2C: Regisztráció és bejelentkezés adhat Gyorsműveletek fiókkal rendelkező felhasználók

> [!NOTE]
> A funkció jelenleg előzetes verzió.
> 

## <a name="create-a-qq-application"></a>Gyorsműveletek-alkalmazás létrehozása

Gyorsműveletek az Azure Active Directory (Azure AD) B2C identitás-szolgáltatóként használatához szüksége Gyorsműveletek-alkalmazás létrehozása, és adja meg azt a megfelelő paraméterekkel. Ehhez Gyorsműveletek fiók szükséges. Ha még nincs fiókja, akkor kaphat egyenként [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Regisztrálja a Gyorsműveletek fejlesztői programhoz

1. Lépjen a [Gyorsműveletek fejlesztői portálján](http://open.qq.com) és jelentkezzen be a Gyorsműveletek fiók hitelesítő adataival.
2. Lépjen a bejelentkezést követően a [http://open.qq.com/reg](http://open.qq.com/reg) egy fejlesztő el.
3. A menüben válasszon ki**个人**(az egyéni fejlesztői).
4. Adja meg a szükséges adatokat az űrlapon, és kattintson a**下一步**(Tovább).
5. Végezze el az e-mailek ellenőrzési folyamata.

> [!NOTE]
> Jóváhagyásra váró fejlesztőként a regisztrálás után néhány napot várni kell. 

### <a name="register-a-qq-application"></a>Egy Gyorsműveletek alkalmazás regisztrálása

1. Ugrás a [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
2. Kattintson a**应用管理**(Alkalmazáskezelés).
3. Kattintson a**创建应用**(az alkalmazás létrehozása).
4. Adja meg a szükséges alkalmazást.
5. Kattintson a**创建应用**(az alkalmazás létrehozása).
6. Adja meg a szükséges adatokat.
7. Az a**授权回调域**(visszahívási URL-címe) mezőbe írja be `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Például ha a `tenant_name` van contoso.onmicrosoft.com, állítsa be az URL-címet kell `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
8. Kattintson a**创建应用**(az alkalmazás létrehozása).
9. A jóváhagyó lapon kattintson a**应用管理**(Alkalmazáskezelés) az alkalmazás felügyeleti lapra való visszatéréshez.
10. Kattintson a**查看**(megtekintése) mellett az imént létrehozott alkalmazást.
11. Kattintson a**修改**(Szerkesztés).
12. A lap tetején, másolja a **Alkalmazásazonosító** és **ALKALMAZÁSKULCS**.

## <a name="configure-qq-as-an-identity-provider-in-your-tenant"></a>Gyorsműveletek az Ön bérlőjében identitás-szolgáltatóként konfigurálása
1. Az alábbi lépéseket követve [lépjen a B2C funkciók panelje](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure portálon.
2. Kattintson a B2C funkciók panelje **identitás-szolgáltatóktól**.
3. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
4. Adjon meg egy rövid **neve** a az identitás-szolgáltató konfigurációját. Írja be például a "Gyorsműveletek".
5. Kattintson a **identitás szolgáltatótípus**, jelölje be **Gyorsműveletek**, és kattintson a **OK**.
6. Kattintson a **az identitásszolgáltató beállítása**
7. Adja meg a **Alkalmazáskulcs** korábban kimásolt a **ügyfél-azonosító**.
8. Adja meg a **alkalmazás titkos kulcs** korábban kimásolt a **Ügyfélkulcs**.
9. Kattintson a **OK** majd **létrehozása** a Gyorsműveletek konfiguráció mentéséhez.

