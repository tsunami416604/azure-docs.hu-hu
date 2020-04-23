---
title: Regisztráció és bejelentkezés beállítása Google-fiókkal
titleSuffix: Azure AD B2C
description: Az alkalmazásokban a Google-fiókokkal való regisztrációt és bejelentkezést a Azure Active Directory B2C használatával biztosíthatja az ügyfeleknek.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 48955caddb64069f897078f5e47066d9f11d119b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188140"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés beállítása Google-fiókkal Azure Active Directory B2C használatával

## <a name="create-a-google-application"></a>Google-alkalmazás létrehozása

Ha a Google-fiókot identitás- [szolgáltatóként](authorization-code-flow.md) szeretné használni Azure Active Directory B2C (Azure ad B2C), létre kell hoznia egy alkalmazást a Google fejlesztői konzolján. Ha még nem rendelkezik Google-fiókkal, regisztrálhat a következő címen [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp):.

1. Jelentkezzen be a [Google fejlesztői konzolra](https://console.developers.google.com/) a Google-fiókja hitelesítő adataival.
1. A lap bal felső sarkában válassza ki a Project (projekt) listát, majd válassza az **új projekt**lehetőséget.
1. Adja meg a **projekt nevét**, majd válassza a **Létrehozás**lehetőséget.
1. Győződjön meg arról, hogy az új projektet használja a képernyő bal felső sarkában található Project (projekt) legördülő menü kiválasztásával, majd válassza ki a projektet név szerint, majd válassza a **Megnyitás**lehetőséget.
1. A bal oldali menüben válassza ki a **OAuth-beleegyezés képernyőt** , válassza a **külső**lehetőséget, majd válassza a **Létrehozás**lehetőséget.
Adja meg az alkalmazás **nevét** . Adja meg a *b2clogin.com* a **jogosultsággal rendelkező tartományok** szakaszban, majd válassza a **Mentés**lehetőséget.
1. Válassza a bal oldali menüben a **hitelesítő adatok** lehetőséget, majd válassza a **hitelesítő adatok** > létrehozása**OAuth-ügyfél-azonosító**lehetőséget.
1. Az **alkalmazás típusa**területen válassza a **webalkalmazás**lehetőséget.
1. Adja meg az alkalmazás **nevét** , adja meg `https://your-tenant-name.b2clogin.com` a megfelelő **JavaScript-eredetet**, és `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` a **jóváhagyott átirányítási URI**-k között. Cserélje `your-tenant-name` le a helyére a bérlő nevét. A bérlő nevének megadásakor az összes kisbetűt kell használnia, még akkor is, ha a bérlőt nagybetűvel definiálták Azure AD B2C.
1. Kattintson a **Létrehozás**gombra.
1. Másolja ki az **ügyfél-azonosító** és az **ügyfél titkos kulcsának**értékeit. Mindkettőre szüksége lesz a Google identitás-szolgáltatóként való konfigurálásához a bérlőben. Az **ügyfél titkos kulcsa** fontos biztonsági hitelesítő adat.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Google-fiók konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza az **identitás-szolgáltatók**, majd a **Google**lehetőséget.
1. Adjon meg egy **nevet**. Például a *Google*.
1. Az **ügyfél-azonosító**mezőben adja meg a korábban létrehozott Google-alkalmazás ügyfél-azonosítóját.
1. Az **ügyfél titka**mezőben adja meg a rögzített ügyfél-titkot.
1. Kattintson a **Mentés** gombra.
