---
title: Egy QQ-fiókkal történő bejelentkezés és bejelentkezés beállítása Azure Active Directory B2C
description: Megadhatja a regisztrációt és a bejelentkezést az ügyfeleknek a QQ-fiókokkal az alkalmazásokban Azure Active Directory B2C használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d230bc8a1e9bf388e1cca4e3a3a691223146d734
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85387983"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Egy QQ-fiókkal történő bejelentkezés és bejelentkezés beállítása Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-qq-application"></a>QQ-alkalmazás létrehozása

Ha a QQ-fiókot Azure Active Directory B2C (Azure AD B2C) identitás-szolgáltatóként kívánja használni, létre kell hoznia egy alkalmazást a bérlőben, amely azt jelképezi. Ha még nem rendelkezik QQ-fiókkal, regisztrálhat a következő címen: [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033) .

### <a name="register-for-the-qq-developer-program"></a>Regisztráljon a QQ fejlesztői programra

1. Jelentkezzen be a [QQ fejlesztői portálra](http://open.qq.com) a QQ-fiókja hitelesítő adataival.
1. Bejelentkezés után lépjen a következőre: [https://open.qq.com/reg](https://open.qq.com/reg) regisztráció fejlesztőként.
1. Válassza a**个人**(egyéni fejlesztő) lehetőséget.
1. Adja meg a szükséges adatokat, és válassza a**下一步**(következő lépés) lehetőséget.
1. Fejezze be az e-mail ellenőrzési folyamatát. A fejlesztőként való regisztrációt követően néhány napot várnia kell a jóváhagyásra.

### <a name="register-a-qq-application"></a>QQ-alkalmazás regisztrálása

1. Ugrás a következőre: [https://connect.qq.com/index.html](https://connect.qq.com/index.html) .
1. Válassza a**应用管理**(alkalmazás-kezelés) lehetőséget.
1. Válassza a**创建应用**(alkalmazás létrehozása) lehetőséget, és adja meg a szükséges információkat.
1. Adja meg `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` a**授权回调域**(visszahívási URL-cím). Ha például a `tenant_name` contoso, állítsa be az URL-címet a következőre: `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` .
1. Válassza a**创建应用**(alkalmazás létrehozása) lehetőséget.
1. A jóváhagyás lapon válassza a**应用管理**(alkalmazás-kezelés) lehetőséget az alkalmazás-kezelés lapra való visszatéréshez.
1. A létrehozott alkalmazás mellett válassza a**查看**(nézet) elemet.
1. Válassza a**修改**(Szerkesztés) lehetőséget.
1. Másolja az **alkalmazás azonosítóját** és az **alkalmazás kulcsát**. Mindkét értékre szüksége van, hogy hozzáadja az identitás-szolgáltatót a bérlőhöz.

## <a name="configure-qq-as-an-identity-provider"></a>A QQ konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C**lehetőséget.
1. Válassza az **identitás-szolgáltatók**, majd a **QQ (előzetes verzió)** lehetőséget.
1. Adjon meg egy **nevet**. Például: *QQ*.
1. Az **ügyfél-azonosító**mezőben adja meg a korábban létrehozott QQ-alkalmazás alkalmazás-azonosítóját.
1. Az **ügyfél titkos**kulcsa mezőben adja meg a rögzített alkalmazás kulcsát.
1. Kattintson a **Mentés** gombra.
