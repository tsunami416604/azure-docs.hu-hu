---
title: Regisztráció és bejelentkezés beállítása WeiBo-fiókkal
titleSuffix: Azure AD B2C
description: A Azure Active Directory B2C használatával WeiBo-fiókkal rendelkező ügyfelek számára biztosítson regisztrációt és bejelentkezést az alkalmazásokban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3f84d1d33874ac70a21c9d596c6fa5a9e608bb84
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950306"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>WeiBo-fiókkal történő regisztráció és bejelentkezés beállítása Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-weibo-application"></a>WeiBo-alkalmazás létrehozása

Ha a WeiBo-fiókot Azure Active Directory B2C (Azure AD B2C) identitás-szolgáltatóként szeretné használni, létre kell hoznia egy alkalmazást a bérlőben, amely azt képviseli. Ha még nem rendelkezik WeiBo-fiókkal, regisztrálhat [https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us)címen.

1. Jelentkezzen be a [WeiBo fejlesztői portálra](https://open.weibo.com/) a WeiBo-fiókja hitelesítő adataival.
1. A bejelentkezést követően válassza ki a megjelenítendő nevet a jobb felső sarokban.
1. A legördülő menüben válassza a**编辑开发者信息**(fejlesztői adatok szerkesztése) elemet.
1. Adja meg a szükséges adatokat, és válassza a**提交**(elküldés) lehetőséget.
1. Fejezze be az e-mail ellenőrzési folyamatát.
1. Nyissa meg a [személyazonosság-ellenőrző lapot](https://open.weibo.com/developers/identity/edit).
1. Adja meg a szükséges adatokat, és válassza a**提交**(elküldés) lehetőséget.

### <a name="register-a-weibo-application"></a>WeiBo-alkalmazás regisztrálása

1. Nyissa meg az [új WeiBo-alkalmazás regisztrációs lapját](https://open.weibo.com/apps/new).
1. Adja meg a szükséges alkalmazás-információkat.
1. Válassza a**创建**(létrehozás) lehetőséget.
1. Másolja az **alkalmazás kulcsának** és az **alkalmazás titkos**kódjának értékét. Mindkét példányhoz hozzá kell adnia az identitás-szolgáltatót a bérlőhöz.
1. Töltse fel a szükséges fényképeket, és adja meg a szükséges információkat.
1. Válassza a**保存以上信息**(Mentés) lehetőséget.
1. Válassza a**高级信息**(speciális információ) lehetőséget.
1. Válassza a**编辑**(Szerkesztés) lehetőséget a OAuth 2.0**授权设置**(átirányítási URL-cím) mező mellett.
1. Adja meg `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` a OAuth 2.0**授权设置**(ÁTirányítási URL-cím). Ha például a bérlő neve contoso, a `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`URL-címet állítsa be.
1. Válassza a**提交**(elküldés) lehetőséget.

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>WeiBo-fiók konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza az **identitás-szolgáltatók**, majd a **WeiBo (előzetes verzió)** lehetőséget.
1. Adjon meg egy **nevet**. Például: *WeiBo*.
1. Az **ügyfél-azonosító**mezőben adja meg a korábban létrehozott WeiBo alkalmazás alkalmazás-kulcsát.
1. Az **ügyfél titka**mezőben adja meg a rögzített alkalmazás titkát.
1. Kattintson a **Mentés** gombra.
