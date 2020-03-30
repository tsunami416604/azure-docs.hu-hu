---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 80e5166775b0cf5acbfce32e61d91c0889e3b086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186359"
---
Ha regisztrálegy alkalmazást az Azure AD B2C-bérlőben, használhatja az aktuális **alkalmazások** vagy az új egyesített **alkalmazásregisztrációk (előzetes verzió)** használatát. [További információ az új felületről](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, majd válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
1. A bal oldali menüben válassza az **Azure AD B2C**lehetőséget. Vagy válassza a **Minden szolgáltatás** lehetőséget, és keresse meg az **Azure AD B2C elemet.**
1. Válassza **az Alkalmazások**lehetőséget, majd a **Hozzáadás**lehetőséget.
1. Adjon nevet az alkalmazásnak. *Például, testapp1*.
1. **Webapp/ Web API**esetén válassza az **Igen**lehetőséget.
1. A **Válasz URL-címmezőbe**írja be a`https://jwt.ms`
1. Kattintson a **Létrehozás** gombra.

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, majd válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
1. A bal oldali menüben válassza az **Azure AD B2C**lehetőséget. Vagy válassza a **Minden szolgáltatás** lehetőséget, és keresse meg az **Azure AD B2C elemet.**
1. Válassza **az Alkalmazásregisztrációk (Előzetes verzió)** lehetőséget, majd az **Új regisztráció**lehetőséget.
1. Adja meg az alkalmazás **nevét.** *Például, testapp1*.
1. Válassza **a Fiókok lehetőséget bármely szervezeti címtárban vagy identitásszolgáltatóban.**
1. Az **Átirányítás URI**csoportban válassza `https://jwt.ms` a **Web**lehetőséget, majd írja be az URL-cím mezőbe.
1. **Az Engedélyek csoportban**jelölje be a Rendszergazda hozzájárulása a *nyílt és offline_access engedélyekhez* jelölőnégyzetet.
1. Kattintson a **Register** (Regisztrálás) elemre.

Az alkalmazás regisztrációjának befejezése után engedélyezze az implicit támogatási folyamatot:

1. A **Kezelés csoportban**válassza a **Hitelesítés**lehetőséget.
1. Válassza **az Új felület kipróbálása** lehetőséget (ha látható).
1. Az **Implicit támogatás**csoportban jelölje be mind az **Access-jogkivonatokat,** mind az **Azonosító-jogkivonatokat** jelölőnégyzetekbe.
1. Kattintson a **Mentés** gombra.