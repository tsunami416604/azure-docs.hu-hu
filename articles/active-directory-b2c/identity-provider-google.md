---
title: Regisztráció és bejelentkezés beállítása Google-fiókkal
titleSuffix: Azure AD B2C
description: Az Azure Active Directory B2C használatával google-fiókkal rendelkező ügyfelek nek adhat meg regisztrációt és bejelentkezést az alkalmazásokban.
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
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés beállítása Google-fiókkal az Azure Active Directory B2C használatával

## <a name="create-a-google-application"></a>Google-alkalmazás létrehozása

Ha egy Google-fiókot [szeretne identitásszolgáltatóként](authorization-code-flow.md) használni az Azure Active Directory B2C (Azure AD B2C) szolgáltatásban, létre kell hoznia egy alkalmazást a Google Developers Console-ban. Ha még nem rendelkezik Google-fiókkal, [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)regisztrálhat a következő helyen: .

1. Jelentkezzen be a [Google Fejlesztői konzolba](https://console.developers.google.com/) google-fiókhitelesítő adataival.
1. A lap bal felső sarkában jelölje ki a projektlistát, majd válassza az **Új projekt**lehetőséget.
1. Adja meg a **Projekt nevét,** és válassza **a Létrehozás lehetőséget.**
1. Győződjön meg arról, hogy az új projektet használja, jelölje ki a projektet a képernyő bal felső részén, jelölje ki a projektet név szerint, majd válassza a **Megnyitás**lehetőséget.
1. Válassza a bal oldali menü **OAuth consent screen (OAuth consent screen) (OAuth consent screen) (OAuth consent screen) (OAuth consent screen) (OAuth consent screen) (OAuth consent screen) (OAuth consent screen)** **External** **Create**
Adja meg az alkalmazás **nevét.** Írja be *b2clogin.com* az Engedélyezett tartományok **szakaszba,** és válassza a **Mentés lehetőséget.**
1. Válassza a bal oldali menü **Hitelesítő adatok parancsát,** majd válassza a Hitelesítő adatok > létrehozása**Oauth ügyfélazonosító** **t.**
1. Az **Alkalmazástípusa csoportban**válassza a **Webalkalmazás**lehetőséget.
1. Adja meg az alkalmazás `https://your-tenant-name.b2clogin.com` **nevét,** írja be az `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` **Engedélyezett JavaScript-eredetet**és az Engedélyezett **átirányítási URI-k mezőbe.** Cserélje `your-tenant-name` le a bérlő nevét. Az összes kisbetűt kell használnia a bérlő nevének megadásakor, még akkor is, ha a bérlő nagybetűkkel van definiálva az Azure AD B2C-ben.
1. Kattintson **a Létrehozás gombra.**
1. Másolja az **ügyfélazonosító** és az **ügyféltitkos csoport értékeit.** Mindkettőre szüksége lesz ahhoz, hogy a Google-t identitásszolgáltatóként konfigurálja a bérlőben. **Az ügyféltitkos adat** fontos biztonsági hitelesítő adat.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Google-fiók beállítása identitásszolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza **az Identitásszolgáltatók**lehetőséget, majd a **Google**lehetőséget.
1. Írjon be egy **nevet**. Például a *Google*.
1. Az **ügyfélazonosítóhoz**adja meg a korábban létrehozott Google-alkalmazás ügyfélazonosítóját.
1. Az **ügyféltitkos kulcsot**adja meg a rögzített ügyféltitkoskulcsot.
1. Kattintson a **Mentés** gombra.
