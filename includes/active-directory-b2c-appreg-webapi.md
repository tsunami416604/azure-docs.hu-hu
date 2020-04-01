---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 9367719b1a1050ce8abf36804b3d0d0d58c7484b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78183377"
---
A webes API-erőforrásokat regisztrálni kell a bérlőben, mielőtt elfogadnák és válaszolnának a hozzáférési jogkivonatot tartalmazó ügyfélalkalmazások védett erőforrás-kéréseire.

Ha regisztrálegy alkalmazást az Azure AD B2C-bérlőben, használhatja az aktuális **alkalmazások** vagy az új egyesített **alkalmazásregisztrációk (előzetes verzió)** használatát. [További információ az új felületről](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, majd válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
1. A bal oldali menüben válassza az **Azure AD B2C**lehetőséget. Vagy válassza a **Minden szolgáltatás** lehetőséget, és keresse meg az **Azure AD B2C elemet.**
1. Válassza **az Alkalmazások**lehetőséget, majd a **Hozzáadás**lehetőséget.
1. Adjon nevet az alkalmazásnak. Például *webapi1*.
1. **Webapp/ Web API**esetén válassza az **Igen**lehetőséget.
1. Az **Implicit folyamat engedélyezése csoportban**válassza az **Igen**lehetőséget.
1. A **válasz URL-cím,** adjon meg egy végpontot, ahol az Azure AD B2C vissza kell adnia az alkalmazás által kért jogkivonatokat. Ebben az oktatóanyagban a minta `https://localhost:5000`helyileg fut, és a .
1. **Az App ID URI**esetén adjon hozzá egy API-végpont-azonosítót a megjelenített URI-hoz. Ebben az oktatóanyagban írja be a `api` `https://contosob2c.onmicrosoft.com/api`be című t, hogy a teljes URI hasonló a hoz.
1. Kattintson a **Létrehozás** gombra.
1. Rögzítse az **alkalmazásazonosítót** egy későbbi lépésben való használatra.

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, majd válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
1. A bal oldali menüben válassza az **Azure AD B2C**lehetőséget. Vagy válassza a **Minden szolgáltatás** lehetőséget, és keresse meg az **Azure AD B2C elemet.**
1. Válassza **az Alkalmazásregisztrációk (Előzetes verzió)** lehetőséget, majd az **Új regisztráció**lehetőséget.
1. Adja meg az alkalmazás **nevét.** Például *webapi1*.
1. Az **ÁTirányítás URI,válassza**ki **a Web**lehetőséget, majd adjon meg egy végpontot, ahol az Azure AD B2C vissza kell adnia az alkalmazás által kért jogkivonatokat. Ebben az oktatóanyagban a minta `http://localhost:5000`helyileg fut, és a .
1. Kattintson a **Register** (Regisztrálás) elemre.
1. Rögzítse az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra.

Ezután engedélyezze az implicit támogatási folyamatot:

1. A **Kezelés csoportban**válassza a **Hitelesítés**lehetőséget.
1. Válassza **az Új felület kipróbálása** lehetőséget (ha látható).
1. Az **Implicit támogatás**csoportban jelölje be mind az **Access-jogkivonatokat,** mind az **Azonosító-jogkivonatokat** jelölőnégyzetekbe.
1. Kattintson a **Mentés** gombra.