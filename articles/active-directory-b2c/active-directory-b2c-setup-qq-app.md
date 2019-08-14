---
title: Egy QQ-fiókkal történő bejelentkezés és bejelentkezés beállítása a Azure Active Directory B2C használatával | Microsoft Docs
description: Megadhatja a regisztrációt és a bejelentkezést az ügyfeleknek a QQ-fiókokkal az alkalmazásokban Azure Active Directory B2C használatával.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9679e3216184c0dc5b8dee241c30fc69d5423aeb
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963758"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Egy QQ-fiókkal történő bejelentkezés és bejelentkezés beállítása Azure Active Directory B2C

> [!NOTE]
> Ez a funkció előzetes verzióban érhető el.
> 

## <a name="create-a-qq-application"></a>QQ-alkalmazás létrehozása

Ha QQ-fiókot szeretne használni Azure Active Directory (Azure AD) B2C-beli identitás-szolgáltatóként, létre kell hoznia egy alkalmazást a bérlőben, amely azt képviseli. Ha még nem rendelkezik QQ-fiókkal, a következő címen érheti el [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033):.

### <a name="register-for-the-qq-developer-program"></a>Regisztráljon a QQ fejlesztői programra

1. Jelentkezzen be a [QQ fejlesztői portálra](http://open.qq.com) a QQ-fiókja hitelesítő adataival.
2. Bejelentkezés után lépjen a [https://open.qq.com/reg](https://open.qq.com/reg) következőre: regisztráció fejlesztőként.
3. Válassza a**个人**(egyéni fejlesztő) lehetőséget.
4. Adja meg a szükséges adatokat, és válassza a**下一步**(következő lépés) lehetőséget.
5. Fejezze be az e-mail ellenőrzési folyamatát. A fejlesztőként való regisztrációt követően néhány napot várnia kell a jóváhagyásra. 

### <a name="register-a-qq-application"></a>QQ-alkalmazás regisztrálása

1. Nyissa meg a következőt: [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
2. Válassza a**应用管理**(alkalmazás-kezelés) lehetőséget.
5. Válassza a**创建应用**(alkalmazás létrehozása) lehetőséget, és adja meg a szükséges információkat.
7. Adja `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` meg a**授权回调域**(visszahívási URL-cím). Ha például a `tenant_name` contoso, állítsa be az URL-címet `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`a következőre:.
8. Válassza a**创建应用**(alkalmazás létrehozása) lehetőséget.
9. A jóváhagyás lapon válassza a**应用管理**(alkalmazás-kezelés) lehetőséget az alkalmazás-kezelés lapra való visszatéréshez.
10. A létrehozott alkalmazás mellett válassza a**查看**(nézet) elemet.
11. Válassza a**修改**(Szerkesztés) lehetőséget.
12. Másolja az **alkalmazás azonosítóját** és az **alkalmazás kulcsát**. Mindkét értékre szüksége van, hogy hozzáadja az identitás-szolgáltatót a bérlőhöz.

## <a name="configure-qq-as-an-identity-provider"></a>A QQ konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
5. Adjon meg egy **neve**. Adja meg például a *QQ*értéket.
6. Válassza az **identitás-szolgáltató típusa**lehetőséget, válassza a **QQ (előzetes verzió)** lehetőséget, majd kattintson **az OK**gombra.
7. Válassza az **Identitáskezelő beállítása** lehetőséget, és adja meg az **ügyfél** -azonosítóval korábban rögzített alkalmazás azonosítóját, és adja meg a korábban létrehozott QQ-alkalmazás **ügyfél** -titkának feljegyzett alkalmazás kulcsát.
8. Kattintson **az OK** gombra, majd kattintson a **Létrehozás** gombra a QQ-konfiguráció mentéséhez.

