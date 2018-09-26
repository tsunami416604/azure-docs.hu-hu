---
title: Regisztráció és bejelentkezés a az Azure Active Directory B2C fiókkal Gyorsműveletek beállítása |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés Gyorsműveletek az alkalmazások az Azure Active Directory B2C fiókkal rendelkező ügyfelek számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 23e44763b773835583d255104771d2329851aac8
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181414"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés a az Azure Active Directory B2C fiókkal Gyorsműveletek beállítása

> [!NOTE]
> Ez a funkció előzetes verzióban érhető el.
> 

## <a name="create-a-qq-application"></a>Gyorsműveletek alkalmazás létrehozása

Gyorsműveletek fiók használatára az identitásszolgáltatójaként az Azure Active Directory (Azure AD) B2C-ben, hozzon létre egy alkalmazást a bérlőben, hogy az azt jelölő kell. Ha egy Gyorsműveletek fiók még nem rendelkezik, beszerezheti a [ https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033 ](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Regisztráljon a Gyorsműveletek developer-program

1. Jelentkezzen be a [Gyorsműveletek fejlesztői portál](http://open.qq.com) Gyorsműveletek-fiókja hitelesítő adataival.
2. Miután bejelentkezett, lépjen [ http://open.qq.com/reg ](http://open.qq.com/reg) fejlesztőként regisztrálja magát.
3. Válassza ki**个人**(az egyéni fejlesztői).
4. Adja meg a szükséges információkat, és válassza ki**下一步**(a következő lépésre).
5. Fejezze be az e-mail ellenőrzésének folyamatát. Jóváhagyásra váró fejlesztőként a regisztrálás után néhány napot várni kell. 

### <a name="register-a-qq-application"></a>Egy Gyorsműveletek alkalmazás regisztrálása

1. Nyissa meg a következőt: [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
2. Válassza ki**应用管理**(felügyeleti).
5. Válassza ki**创建应用**(alkalmazás létrehozása), és adja meg a szükséges adatokat.
7. Adja meg `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` a**授权回调域**(visszahívási URL-cím). Például ha a `tenant_name` a contoso, állítsa be az URL-címet kell `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
8. Válassza ki**创建应用**(alkalmazás létrehozása).
9. A megerősítő oldalon válassza ki a**应用管理**(Alkalmazáskezelés) az alkalmazás felügyeleti lapra való visszatéréshez.
10. Válassza ki**查看**(megtekintése) mellett a létrehozott alkalmazást.
11. Válassza ki**修改**(Szerkesztés).
12. Másolás a **Alkalmazásazonosító** és **ALKALMAZÁSKULCS**. Mindkét ezeket az értékeket az identitásszolgáltató hozzáadása a bérlőhöz van szüksége.

## <a name="configure-qq-as-an-identity-provider"></a>Gyorsműveletek konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
5. Adjon meg egy **neve**. Adja meg például *Gyorsműveletek*.
6. Válassza ki **identitásszolgáltató típusa**válassza **Gyorsműveletek (előzetes verzió)**, és kattintson a **OK**.
7. Válassza ki **az identitásszolgáltató beállítása** , és adja meg az alkalmazás azonosítója, mint korábban rögzített a **ügyfél-azonosító** , és írja be az Alkalmazáskulcs, feljegyzett a **titkos Ügyfélkód** a Gyorsműveletek az a korábban létrehozott alkalmazás.
8. Kattintson a **OK** majd **létrehozás** a Gyorsműveletek konfigurációjának mentéséhez.

