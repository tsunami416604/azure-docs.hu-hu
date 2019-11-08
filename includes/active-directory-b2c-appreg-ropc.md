---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: dec912ef6479e3668f22227c5f05de5ae8b12fcc
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73799878"
---
Az alkalmazások Azure AD B2C-bérlőben való regisztrálásához használhatja a jelenlegi **alkalmazásokat** , vagy az új, egyesített **Alkalmazásregisztrációk (előzetes verzió)** élményt. [További információ az új felületről](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Alkalmazások](#tab/applications/)

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C**lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C**lehetőséget.
1. Válassza az **alkalmazások**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.
1. Adja meg az alkalmazás nevét. Például: *ROPC_Auth_app*.
1. **Natív ügyfél**esetén válassza az **Igen**lehetőséget.
1. Hagyja meg a többi értéket, és válassza a **Létrehozás**lehetőséget.
1. Jegyezze fel az **alkalmazás azonosítóját** egy későbbi lépésben való használatra.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C**lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C**lehetőséget.
1. Válassza a **Alkalmazásregisztrációk (előzetes verzió)** lehetőséget, majd válassza az **új regisztráció**lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: *ROPC_Auth_app*.
1. Hagyja meg a többi értéket, és válassza a **regisztráció**lehetőséget.
1. Jegyezze fel az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra.
1. A **kezelés**területen válassza a **hitelesítés**lehetőséget.
1. Válassza **az új élmény kipróbálása** (ha látható) lehetőséget.
1. Az **alapértelmezett ügyfél típusa**területen válassza az **Igen** lehetőséget az alkalmazás nyilvános ügyfélként való kezelésére. Ez a beállítás a ROPC folyamathoz szükséges.
1. Kattintson a **Mentés** gombra.