---
title: Regisztráció és bejelentkezés egy Microsoft-fiók – Azure Active Directory B2C-beállítása
description: Adja meg a regisztráció és bejelentkezés az alkalmazásokban az Azure Active Directory B2C használatával a Microsoft-fiókkal rendelkező ügyfelek számára.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 63aa93e72d76d430fc7e026478d91e496f27eae6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055115"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés a Microsoft-fiókkal az Azure Active Directory B2C beállítása

## <a name="create-a-microsoft-account-application"></a>Hozzon létre egy Microsoft-fiók alkalmazás

Használandó Microsoft-fiók, mint egy [identitásszolgáltató](active-directory-b2c-reference-oidc.md) Azure Active Directory (Azure AD) B2C-vel, az alkalmazás létrehozása a bérlőben, hogy az azt jelölő szüksége. Ha még nincs Microsoft-fiókkal, beszerezheti a [ https://www.live.com/ ](https://www.live.com/).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Válassza ki **minden szolgáltatás** az Azure Portalon, és majd keresse meg és válassza a bal felső sarkában lévő **alkalmazásregisztrációk**.
1. Válassza ki **új regisztrálása**
1. Adjon meg egy **neve** az alkalmazáshoz. Ha például *MSAapp1*.
1. A **támogatott fióktípusok**válassza **fiókok minden olyan szervezeti directory és személyes Microsoft-fiókok (például a Skype, Xbox, Outlook.com)** . Ez a beállítás célozza meg, a Microsoft identitások legszélesebb készletét.

   A másik fiók típusa ablakbon további információkért lásd: [a rövid útmutató: Alkalmazás regisztrálása a Microsoft identity platform az](../active-directory/develop/quickstart-register-app.md).
1. Alatt **átirányítási URI-t (nem kötelező)** válassza **webes** , és adja meg `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` a szövegmezőben. Cserélje le `your-tenant-name` együtt az Azure AD B2C bérlő neve.
1. Válassza ki **regisztrálása**
1. Rekord a **Alkalmazásazonosítót (ügyfél)** látható az alkalmazás Áttekintés oldalon. Ez szükséges a következő szakaszban az identitásszolgáltató konfigurálásakor.
1. Válassza ki **tanúsítványok és titkos kulcsok**
1. Kattintson a **új titkos ügyfélkulcsot**
1. Adjon meg egy **leírás** a titkos kulcsot, például a *alkalmazásjelszó 1*, és kattintson a **Hozzáadás**.
1. Jegyezze fel az alkalmazás a jelszót látható a **érték** oszlop. Ez szükséges a következő szakaszban az identitásszolgáltató konfigurálásakor.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Identitás-szolgáltatóként a Microsoft-fiók konfigurálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy használja az Azure AD B2C-bérlő kattintva tartalmazó könyvtárba a **címtár és előfizetés-szűrő** a felső menüben, és a könyvtár, amely tartalmazza a bérlő kiválasztása.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
1. Adjon meg egy **neve**. Adja meg például *MSA*.
1. Válassza ki **identitásszolgáltató típusa**válassza **Microsoft Account**, és kattintson a **OK**.
1. Válassza ki **az identitásszolgáltató beállítása** , és adja meg a korábban feljegyzett alkalmazás (ügyfél) Azonosítót a **ügyfél-azonosító** szöveg mezőbe, majd adja meg a titkos ügyfélkulcsot feljegyzett a **ügyfél titkos kulcs** szövegmezőben.
1. Kattintson a **OK** majd **létrehozás** a Microsoft-fiók konfigurációjának mentéséhez.
