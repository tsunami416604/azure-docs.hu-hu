---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: baf39c1fe72703d8ebc18b03bae1c963536d7ced
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475205"
---
Az alkalmazások Azure AD B2C-bérlőben való regisztrálásához használhatja a jelenlegi **alkalmazásokat** , vagy az új, egyesített **Alkalmazásregisztrációk (előzetes verzió)** élményt. [További információ az előzetes](https://aka.ms/b2cappregintro)verzióról.

#### <a name="applicationstabapplications"></a>[Alkalmazások](#tab/applications/)

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure Active Directory** (*nem* Azure ad B2C) lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki **Azure Active Directory**.
1. A **kezelés**területen válassza a **Alkalmazásregisztrációk (örökölt)** lehetőséget.
1. Válassza az **Új alkalmazás regisztrálása** elemet.
1. Adja meg az alkalmazás nevét. Például: *managementapp1*.
1. Az **alkalmazás típusa**mezőben válassza a **Web App/API**lehetőséget.
1. Írjon be egy érvényes URL-címet a **bejelentkezési URL-cím**mezőbe. Például: `https://localhost`. A végpontnak nem kell elérhetőnek lennie, de érvényes URL-címnek kell lennie.
1. Kattintson a **Létrehozás** gombra.
1. Jegyezze fel a **regisztrált** alkalmazás – áttekintés oldalon megjelenő **Application ID** -t. Ezt az értéket egy későbbi lépésben kell használni.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C**lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C**lehetőséget.
1. Válassza a **Alkalmazásregisztrációk (előzetes verzió)** lehetőséget, majd válassza az **új regisztráció**lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: *managementapp1*.
1. **Csak a szervezeti címtárban**válassza a fiókok elemet.
1. Az **engedélyek**területen törölje a *rendszergazdai jóváhagyás megadása OpenID-és offline_access-engedélyekhez* jelölőnégyzet jelölését.
1. Kattintson a **Register** (Regisztrálás) elemre.
1. Jegyezze fel az alkalmazás – áttekintés oldalon megjelenő **alkalmazást (ügyfél-azonosítót)** . Ezt az értéket egy későbbi lépésben kell használni.