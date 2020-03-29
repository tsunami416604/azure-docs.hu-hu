---
title: Regisztráció és bejelentkezés beállítása QQ-fiókkal az Azure Active Directory B2C használatával
description: Az Azure Active Directory B2C használatával QQ-fiókkal rendelkező ügyfelek nek adhat meg regisztrációt és bejelentkezést az alkalmazásokban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b0f15124c64e5cca54112987d486ddadaca79452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187987"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés beállítása QQ-fiókkal az Azure Active Directory B2C használatával

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-qq-application"></a>QQ-alkalmazás létrehozása

A QQ-fiók identitásszolgáltatóként való használatához az Azure Active Directory B2C (Azure AD B2C) szolgáltatásban létre kell hoznia egy alkalmazást a bérlőben, amely azt képviseli. Ha még nem rendelkezik QQ-fiókkal, regisztrálhat a. [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033)

### <a name="register-for-the-qq-developer-program"></a>Regisztráljon a QQ fejlesztői programra

1. Jelentkezzen be a [QQ fejlesztői portálra](http://open.qq.com) a QQ-fiók hitelesítő adataival.
1. A bejelentkezés után [https://open.qq.com/reg](https://open.qq.com/reg) nyissa meg a regisztrációt fejlesztőként.
1. Válassza **a (egyéni** fejlesztő) lehetőséget.
1. Adja meg a szükséges adatokat, és válassza **a 啦ンン (következő** lépés).
1. Fejezze be az e-mail-ellenőrzési folyamatot. A fejlesztőként történő regisztráció után néhány napot meg kell várnia a jóváhagyásra.

### <a name="register-a-qq-application"></a>QQ-alkalmazás regisztrálása

1. Nyissa [https://connect.qq.com/index.html](https://connect.qq.com/index.html)meg a.
1. Válassza ki **a (app** management).
1. Válassza ki **a (create** app) (alkalmazás létrehozása) lehetőséget, és adja meg a szükséges információkat.
1. Írja `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` be **a** következő ta- Ha például `tenant_name` a contoso, állítsa be `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`az URL-címet.
1. Válassza a **(alkalmazás** létrehozása) lehetőséget.
1. A megerősítést kérő lapon válassza az **alkalmazáskezelés** (alkalmazáskezelés) lehetőséget az alkalmazáskezelő lapra való visszatéréshez.
1. Válassza **a** létrehozott alkalmazás (nézet) lehetőséget.
1. Válassza **a (szerkesztés)** lehetőséget.
1. Másolja az **APP-azonosítót** és az **APP-kulcsot.** Mindkét értékre szüksége van az identitásszolgáltató hozzáadása a bérlőhöz.

## <a name="configure-qq-as-an-identity-provider"></a>QQ beállítása identitásszolgáltatóként

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
1. Válassza ki a **Könyvtár + Előfizetés** ikont a portál eszköztárán, majd válassza ki az Azure AD B2C-bérlőt tartalmazó könyvtárat.
1. Az Azure Portalon keresse meg és válassza ki **az Azure AD B2C parancsot.**
1. Válassza **az Identitásszolgáltatók**lehetőséget, majd válassza a **QQ (Előzetes verzió)** lehetőséget.
1. Írjon be egy **nevet**. Például *QQ*.
1. Az **ügyfélazonosítóhoz**adja meg a korábban létrehozott QQ-alkalmazás APP-azonosítóját.
1. Az **ügyfél titkos kulcsa,** adja meg az APP kulcs, amely rögzített.
1. Kattintson a **Mentés** gombra.
