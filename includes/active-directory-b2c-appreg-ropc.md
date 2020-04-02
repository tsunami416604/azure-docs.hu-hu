---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: cea3245176e6c38137d68e3ad4b47477bedc78be
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529182"
---
Ha regisztrálegy alkalmazást az Azure AD B2C-bérlőben, használhatja az aktuális **alkalmazások** vagy az új egyesített **alkalmazásregisztrációk (előzetes verzió)** használatát. [További információ az új felületről](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, majd válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
1. A bal oldali menüben válassza az **Azure AD B2C**lehetőséget. Vagy válassza a **Minden szolgáltatás** lehetőséget, és keresse meg az **Azure AD B2C elemet.**
1. Válassza **az Alkalmazások**lehetőséget, majd a **Hozzáadás**lehetőséget.
1. Adjon nevet az alkalmazásnak. Például *ROPC_Auth_app*.
1. **Natív ügyfél esetén**válassza az **Igen**lehetőséget.
1. Hagyja a többi értéket úgy, ahogy vannak, majd válassza a **Létrehozás gombot.**
1. Rögzítse az **alkalmazásazonosítót** egy későbbi lépésben való használatra.

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, majd válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
1. A bal oldali menüben válassza az **Azure AD B2C**lehetőséget. Vagy válassza a **Minden szolgáltatás** lehetőséget, és keresse meg az **Azure AD B2C elemet.**
1. Válassza **az Alkalmazásregisztrációk (Előzetes verzió)** lehetőséget, majd az **Új regisztráció**lehetőséget.
1. Adja meg az alkalmazás **nevét.** Például *ROPC_Auth_app*.
1. Hagyja a többi értéket úgy, ahogy vannak, majd válassza a **Regisztráció**lehetőséget.
1. Rögzítse az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra.
1. A **Kezelés csoportban**válassza a **Hitelesítés**lehetőséget.
1. Válassza **az Új felület kipróbálása** lehetőséget (ha látható).
1. Az **Alapértelmezett ügyféltípus csoportban**válassza az **Igen** lehetőséget az alkalmazás nyilvános ügyfélként való kezeléséhez. Ez a beállítás szükséges a ROPC-folyamathoz.
1. Kattintson a **Mentés** gombra.
1. A bal oldali menüben válassza a **Jegyzékfájl** lehetőséget a jegyzékszerkesztő megnyitásához. 
1. Állítsa az **oauth2AllowImplicitFlow** attribútumot *true*értékre :
    ```json
    "oauth2AllowImplicitFlow": true,
    ```
1. Kattintson a **Mentés** gombra.