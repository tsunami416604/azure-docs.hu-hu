---
title: Regisztráció és bejelentkezés beállítása Microsoft-fiókkal
titleSuffix: Azure AD B2C
description: Az alkalmazásokban Microsoft-fiókkal rendelkező ügyfelek számára Azure Active Directory B2C használatával regisztrálhat és regisztrálhat.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ad4b08a12e63b4ae3eed0eb09e295d9730de97bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388000"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés beállítása Microsoft-fiók használatával Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Microsoft-fiók-alkalmazás létrehozása

Ha Microsoft-fiók [identitás-szolgáltatóként](openid-connect.md) szeretné használni Azure Active Directory B2C (Azure ad B2C), létre kell hoznia egy alkalmazást az Azure ad-bérlőben. Az Azure AD-bérlő nem azonos a Azure AD B2C Bérlővel. Ha még nem rendelkezik Microsoft-fiókval, beszerezhet egyet [https://www.live.com/](https://www.live.com/) .

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy az Azure AD-bérlőt tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki az Azure ad-bérlőt tartalmazó könyvtárat.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Alkalmazásregisztrációk**.
1. Válassza az **új regisztráció**lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: *MSAapp1*.
1. A **támogatott fióktípus**területen válassza a **fiókok lehetőséget bármely szervezeti címtárban (bármely Azure ad-címtár-több-bérlős) és a személyes Microsoft-fiókokat (például Skype, Xbox)**.

   A különböző fióktípus-beállításokkal kapcsolatos további információkért tekintse meg a rövid útmutató [: alkalmazás regisztrálása a Microsoft Identity platformmal](../active-directory/develop/quickstart-register-app.md)című témakört.
1. Az **átirányítási URI (nem kötelező)** területen válassza a **web** lehetőséget, és írja be `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/oauth2/authresp` a szövegmezőbe. Cserélje le a helyére a `<tenant-name>` Azure ad B2C bérlő nevét.
1. **Regisztráció** kiválasztása
1. Jegyezze fel az alkalmazás – Áttekintés lapon megjelenő **alkalmazást (ügyfél-azonosítót)** . Erre akkor van szükség, amikor a következő szakaszban konfigurálja az identitás-szolgáltatót.
1. **Tanúsítványok kiválasztása & Secrets**
1. Kattintson az **Új titkos ügyfélkód** elemre.
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
1. Válassza a **Mentés** lehetőséget.
