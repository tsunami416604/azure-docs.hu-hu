---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 77c3aee4fdb5f8b2ee7ed83f92917573ad6ad529
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73643589"
---
Az alkalmazások Azure AD B2C-bérlőben való regisztrálásához használhatja a jelenlegi **alkalmazásokat** , vagy az új, egyesített **Alkalmazásregisztrációk (előzetes verzió)** élményt. [További információ az új felületről](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Alkalmazások](#tab/applications/)

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
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

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C**lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C**lehetőséget.
1. Válassza a **Alkalmazásregisztrációk (előzetes verzió)** lehetőséget, majd válassza az **új regisztráció**lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: *managementapp1*.
1. **Csak a szervezeti címtárban**válassza a fiókok elemet.
1. Az **engedélyek**területen törölje a *rendszergazdai jóváhagyás megadása OpenID-és offline_access-engedélyekhez* jelölőnégyzet jelölését.
1. Kattintson a **Register** (Regisztrálás) elemre.
1. Jegyezze fel az alkalmazás – áttekintés oldalon megjelenő **alkalmazást (ügyfél-azonosítót)** . Ezt az értéket egy későbbi lépésben kell használni.