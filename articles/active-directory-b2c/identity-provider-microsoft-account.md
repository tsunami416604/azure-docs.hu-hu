---
title: Regisztráció és bejelentkezés beállítása Microsoft-fiókkal
titleSuffix: Azure AD B2C
description: Az Azure Active Directory B2C használatával microsoftos fiókkal rendelkező ügyfelek nek adhat meg regisztrációt és bejelentkezést az alkalmazásokban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 25784eb161a860398b0741d1d20375cabd1c4eca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188018"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés beállítása Microsoft-fiókkal az Azure Active Directory B2C használatával

## <a name="create-a-microsoft-account-application"></a>Microsoft-fiókalkalmazás létrehozása

Microsoft-fiók használata [identitásszolgáltatóként](openid-connect.md) az Azure Active Directory B2C (Azure AD B2C), létre kell hoznia egy alkalmazást az Azure AD-bérlőben. Az Azure AD-bérlő nem ugyanaz, mint az Azure AD B2C-bérlő. Ha még nem rendelkezik Microsoft-fiókkal, a [https://www.live.com/](https://www.live.com/)alkalmazásban is beszerezhet egyet.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Győződjön meg arról, hogy az Azure AD-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja az Azure AD-bérlőt tartalmazó könyvtárat.
1. Válassza az **Összes szolgáltatás lehetőséget** az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Alkalmazásregisztrációk lehetőséget.**
1. Válassza **az Új regisztráció lehetőséget.**
1. Adja meg az alkalmazás **nevét.** Például *MSAapp1*.
1. A **Támogatott fióktípusok csoportban**válassza a Fiókok lehetőséget **bármely szervezeti címtárban és személyes Microsoft-fiókban (pl. Skype, Xbox, Outlook.com).** Ez a beállítás a Microsoft-identitások legszélesebb körét célozza meg.

   A különböző fióktípus-beállításokról további információt a [Rövid útmutató: Alkalmazás regisztrálása a Microsoft identity platformmal](../active-directory/develop/quickstart-register-app.md)című témakörben talál.
1. Az **Uri átirányítása (nem kötelező)** csoportban válassza a **Web** lehetőséget, és írja be `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` a szövegmezőbe. Cserélje `your-tenant-name` le az Azure AD B2C bérlői nevét.
1. **Regisztráció** kiválasztása
1. Rögzítse az alkalmazás áttekintése lapon megjelenő **alkalmazásazonosítót.** Erre akkor van szüksége, ha a következő szakaszban konfigurálja az identitásszolgáltatót.
1. Jelölje be **a tanúsítványok & titkos kulcsok at**
1. Kattintson **az Új ügyféltitok gombra.**
1. Írja be a titkos **adatik leírását,** például *az Alkalmazás jelszava 1*, majd kattintson a **Hozzáadás gombra.**
1. Rögzítse az Alkalmazás jelszavát az **Érték** oszlopban. Erre akkor van szüksége, ha a következő szakaszban konfigurálja az identitásszolgáltatót.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Microsoft-fiók konfigurálása identitásszolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza **az Identitásszolgáltatók**lehetőséget, majd válassza a **Microsoft-fiók**lehetőséget.
1. Írjon be egy **nevet**. Például *az MSA*.
1. Az **ügyfélazonosítóhoz**adja meg a korábban létrehozott Azure AD-alkalmazás alkalmazásazonosítóját (ügyfélazonosítóját).
1. Az **ügyféltitkos kulcsot**adja meg a rögzített ügyféltitokhoz.
1. Kattintson a **Mentés** gombra.
