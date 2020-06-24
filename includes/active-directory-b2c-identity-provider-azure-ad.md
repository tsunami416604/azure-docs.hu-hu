---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/07/2020
ms.author: mimart
ms.openlocfilehash: 818f33abfdb0655d96c0a8873a43903ee972b3bf
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2020
ms.locfileid: "82925464"
---
## <a name="register-an-azure-ad-app"></a>Azure AD-alkalmazás regisztrálása

Ha egy adott Azure AD-szervezet felhasználói számára engedélyezni szeretné a bejelentkezést, regisztrálnia kell egy alkalmazást a szervezeti Azure AD-bérlőn belül.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy a szervezeti Azure AD-bérlőt (például contoso.com) tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés szűrőt** a felső menüben, majd válassza ki azt a könyvtárat, amely tartalmazza az Azure ad-bérlőt.
1. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Alkalmazásregisztrációk**.
1. Válassza az **új regisztráció**lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: `Azure AD B2C App`.
1. Fogadja el a fiókok alapértelmezett kiválasztását ebben a **szervezeti könyvtárban csak** ehhez az alkalmazáshoz.
1. Az **átirányítási URI**esetében fogadja el a **web**értékét, és írja be az alábbi URL-címet minden kisbetűvel, ahol a `your-B2C-tenant-name` helyére a Azure ad B2C bérlő neve kerül.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Például: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Kattintson a **Register** (Regisztrálás) elemre. Jegyezze fel az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra.
1. Válassza a **tanúsítványok & titkok**lehetőséget, majd válassza az **új ügyfél titka**lehetőséget.
1. Adja meg a titok **leírását** , válasszon ki egy lejáratot, majd kattintson a **Hozzáadás**gombra. Jegyezze fel a titok **értékét** egy későbbi lépésben való használatra.

### <a name="configuring-optional-claims"></a>Választható jogcímek konfigurálása

Ha a `family_name` és a `given_name` jogcímeket az Azure ad-től szeretné beszerezni, az alkalmazáshoz a Azure Portal felhasználói felületén vagy az alkalmazás-jegyzékfájlban konfigurálhat választható jogcímeket is. További információkért lásd: [opcionális jogcímek megadása az Azure ad-alkalmazáshoz](/azure/active-directory/develop/active-directory-optional-claims).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **kezelés** szakaszban válassza a **Alkalmazásregisztrációk**lehetőséget.
1. Válassza ki azt az alkalmazást, amelyhez választható jogcímeket szeretne konfigurálni a listában.
1. A **kezelés** szakaszban válassza a **jogkivonat-konfiguráció**elemet.
1. Válassza a **választható jogcím hozzáadása**lehetőséget.
1. A **jogkivonat típusa**beállításnál válassza az **azonosító**lehetőséget.
1. Válassza ki a hozzáadandó választható jogcímeket, `family_name` és `given_name` .
1. Kattintson a **Hozzáadás** parancsra.