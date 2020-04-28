---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 31d4b65c6cd6a3dfe3cb779022329f338436c69b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78184349"
---
Az alkalmazások Azure AD B2C-bérlőben való regisztrálásához használhatja a jelenlegi **alkalmazásokat** , vagy az új, egyesített **Alkalmazásregisztrációk (előzetes verzió)** élményt. [További információ az új felületről](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure Active Directory**lehetőséget.
1. A **kezelés**területen válassza a **Alkalmazásregisztrációk (örökölt)** lehetőséget.
1. Válassza az **Új alkalmazás regisztrálása** elemet.
1. Adjon nevet az alkalmazásnak. Például: *managementapp1*.
1. Az **alkalmazás típusa**mezőben válassza a **Web App/API**lehetőséget.
1. Írjon be egy érvényes URL-címet a **bejelentkezési URL-cím**mezőbe. Például: `https://localhost`. A végpontnak nem kell elérhetőnek lennie, de érvényes URL-címnek kell lennie.
1. Kattintson a **Létrehozás** gombra.
1. Jegyezze fel a **regisztrált** alkalmazás – áttekintés oldalon megjelenő **Application ID** -t. Ezt az értéket egy későbbi lépésben kell használni.

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A Azure Portal keresse meg és válassza a **Azure ad B2C**lehetőséget.
1. Válassza a **Alkalmazásregisztrációk (előzetes verzió)** lehetőséget, majd válassza az **új regisztráció**lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: *managementapp1*.
1. **Csak a szervezeti címtárban**válassza a fiókok elemet.
1. Az **engedélyek**területen törölje a *rendszergazdai jóváhagyás megadása az OpenID-hez és a offline_access engedélyekhez* jelölőnégyzet jelölését.
1. Kattintson a **Register** (Regisztrálás) elemre.
1. Jegyezze fel az alkalmazás – áttekintés oldalon megjelenő **alkalmazást (ügyfél-azonosítót)** . Ezt az értéket egy későbbi lépésben kell használni.