---
title: Regisztráció és bejelentkezés beállítása Microsoft-fiókkal – Azure Active Directory B2C
description: Az alkalmazásokban Microsoft-fiókkal rendelkező ügyfelek számára Azure Active Directory B2C használatával regisztrálhat és regisztrálhat.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 134ca92671a41eb53201101a3622215466a60c09
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065091"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés beállítása Microsoft-fiók használatával Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Microsoft-fiók-alkalmazás létrehozása

Ha Microsoft-fiók [identitás-szolgáltatóként](active-directory-b2c-reference-oidc.md) szeretné használni Azure Active Directory B2C (Azure ad B2C), létre kell hoznia egy alkalmazást az Azure ad-bérlőben. Az Azure AD-bérlő nem azonos a Azure AD B2C Bérlővel. Ha még nem rendelkezik Microsoft-fiókval, beszerezhet egyet [https://www.live.com/](https://www.live.com/).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy az Azure AD-bérlőt tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki az Azure ad-bérlőt tartalmazó könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Alkalmazásregisztrációk**.
1. Válassza az **új regisztráció**lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: *MSAapp1*.
1. A **támogatott fióktípus**területen válassza a **fiókok lehetőséget a szervezeti címtárban és a személyes Microsoft-fiókokban (például Skype, Xbox, Outlook.com)** . Ez a beállítás a Microsoft-identitások legszélesebb körét célozza meg.

   A különböző fióktípus-beállításokkal kapcsolatos további információkért lásd: [gyors útmutató: Alkalmazás regisztrálása a Microsoft Identity platformon](../active-directory/develop/quickstart-register-app.md).
1. Az **átirányítási URI (nem kötelező)** területen válassza a `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` **web** lehetőséget, és írja be a szövegmezőbe. Cserélje `your-tenant-name` le a helyére a Azure ad B2C bérlő nevét.
1. **Regisztráció** kiválasztása
1. Jegyezze fel az alkalmazás – Áttekintés lapon megjelenő **alkalmazást (ügyfél-azonosítót)** . Erre akkor van szükség, amikor a következő szakaszban konfigurálja az identitás-szolgáltatót.
1. **Tanúsítványok kiválasztása & Secrets**
1. Kattintson az **új ügyfél titka** elemre.
1. Adja meg a titok **leírását** , például az *1. alkalmazás jelszava*, majd kattintson a **Hozzáadás**gombra.
1. Jegyezze fel az **érték** oszlopban látható alkalmazás jelszavát. Erre akkor van szükség, amikor a következő szakaszban konfigurálja az identitás-szolgáltatót.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Microsoft-fiók konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza az **identitás-szolgáltatók**, majd a Microsoft- **fiók**lehetőséget.
1. Adjon meg egy **nevet**. Például: *MSA*.
1. Az **ügyfél-azonosító**mezőben adja meg a korábban létrehozott Azure ad-alkalmazás alkalmazás-(ügyfél-) azonosítóját.
1. Az **ügyfél titka**mezőben adja meg a rögzített ügyfél-titkot.
1. Kattintson a **Mentés** gombra.
