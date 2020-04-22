---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/07/2020
ms.author: mimart
ms.openlocfilehash: 31a6d116ae1afce9afdd3786747490980963d823
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678041"
---
## <a name="register-an-azure-ad-app"></a>Azure AD-alkalmazás regisztrálása

Ahhoz, hogy engedélyezze a bejelentkezést egy adott Azure AD-szervezet felhasználói számára, regisztrálnia kell egy alkalmazást a szervezeti Azure AD-bérlőn belül.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy a szervezeti Azure AD-bérlőt (például contoso.com) tartalmazó könyvtárat használja. Válassza ki a **Könyvtár + előfizetés szűrőa** felső menüben, majd válassza ki az Azure AD-bérlőt tartalmazó könyvtárat.
1. Válassza az **Összes szolgáltatás lehetőséget** az Azure Portal bal felső sarkában, majd keresse meg és válassza az **Alkalmazásregisztrációk lehetőséget.**
1. Válassza **az Új regisztráció lehetőséget.**
1. Adja meg az alkalmazás **nevét.** Például: `Azure AD B2C App`.
1. Csak ehhez az alkalmazáshoz fogadja el a fiókok alapértelmezett kiválasztását ebben a **szervezeti címtárban.**
1. Az **átirányításURI,** fogadja el a **web**értékét, és adja meg `your-B2C-tenant-name` a következő URL-t az összes kisbetű, ahol lecseréli az Azure AD B2C bérlő nevét.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Például: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Kattintson a **Register** (Regisztrálás) elemre. Rögzítse az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra.
1. Válassza **a Tanúsítványok & titkos kulcsok**at, majd az Új **ügyféltitok**lehetőséget.
1. Adja meg a titkos **kulcsot,** válasszon egy lejárati lehetőséget, majd válassza a **Hozzáadás**lehetőséget. Rögzítse a titkos **adatik értékét** egy későbbi lépésben való használatra.

### <a name="configuring-optional-claims"></a>Választható jogcímek konfigurálása

Ha azt szeretné, `family_name` `given_name` hogy a és a jogcímek az Azure AD-ből, konfigurálhatja az alkalmazás választható jogcímeket az Azure Portal felhasználói felületén vagy az alkalmazásjegyzékben. További információ: [Választható jogcímek biztosítása az Azure AD-alkalmazáshoz.](/active-directory/develop/active-directory-optional-claims.md)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **Kezelés** szakaszban válassza **az Alkalmazásregisztrációk**lehetőséget.
1. Válassza ki azt az alkalmazást, amelyhez a nem kötelező jogcímeket konfigurálni szeretné a listában.
1. A **Kezelés** szakaszban válassza a **Token konfigurációja**lehetőséget.
1. Válassza **a Választható jogcím hozzáadása**lehetőséget.
1. A **Token típushoz**válassza az **Azonosító**lehetőséget.
1. Válassza ki a hozzáadni `family_name` `given_name`kívánt választható jogcímeket, és válassza ki a lehetőséget.
1. Kattintson a **Hozzáadás** parancsra.