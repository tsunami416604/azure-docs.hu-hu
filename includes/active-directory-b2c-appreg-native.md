---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: d3a323a28d92e2a5834b65316d61c7d53a42aacf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183778"
---
Ha regisztrálegy alkalmazást az Azure AD B2C-bérlőben, használhatja az aktuális **alkalmazások** vagy az új egyesített **alkalmazásregisztrációk (előzetes verzió)** használatát. [További információ az új felületről](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, majd válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
1. A bal oldali menüben válassza az **Azure AD B2C**lehetőséget. Vagy válassza a **Minden szolgáltatás** lehetőséget, és keresse meg az **Azure AD B2C elemet.**
1. Válassza **az Alkalmazások**lehetőséget, majd a **Hozzáadás**lehetőséget.
1. Adjon nevet az alkalmazásnak. Például *nativeapp1*.
1. **Natív ügyfél esetén**válassza az **Igen**lehetőséget.
1. Adjon meg **egy egyéni átirányítási URI-t** egyedi sémával. Például: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Az átirányítási URI kiválasztásakor két fontos szempontot kell figyelembe venni:
    * **Egyedi**: Az átirányítási URI sémának minden alkalmazásnál egyedinek kell lennie. A `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`példában `com.onmicrosoft.contosob2c.exampleapp` a rendszer szerepel. Ezt a mintát kell követni. Ha két alkalmazás ugyanazt a sémát használja, a felhasználó választhat egy alkalmazás kiválasztásával. Ha a felhasználó helytelenül választ, a bejelentkezés sikertelen lesz.
    * **Teljes**: Az átirányítási URI-nak rendszerrel és elérési úttal is rendelkeznie kell. Az elérési útnak legalább egy perjelet kell tartalmaznia a tartomány után. Például, `//oauth/` működik, miközben `//oauth` nem sikerül. Ne adjon meg speciális karaktereket az URI-ban, például aláhúzásokat.
1. Kattintson a **Létrehozás** gombra.

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, majd válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
1. A bal oldali menüben válassza az **Azure AD B2C**lehetőséget. Vagy válassza a **Minden szolgáltatás** lehetőséget, és keresse meg az **Azure AD B2C elemet.**
1. Válassza **az Alkalmazásregisztrációk (Előzetes verzió)** lehetőséget, majd az **Új regisztráció**lehetőséget.
1. Adja meg az alkalmazás **nevét.** Például *nativeapp1*.
1. A **Támogatott fióktípusok csoportban**válassza a Fiókok lehetőséget **bármely szervezeti címtárban vagy bármely identitásszolgáltatóban.**
1. Az **Átirányítás URI csoportban**válassza a **Nyilvános ügyfél/natív (mobil & asztali)** lehetőséget a legördülő menüben.
1. Adjon meg egy átirányítási URI-t egyedi sémával. Például: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Az átirányítási URI kiválasztásakor két fontos szempontot kell figyelembe venni:
    * **Egyedi**: Az átirányítási URI sémának minden alkalmazásnál egyedinek kell lennie. A `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`példában `com.onmicrosoft.contosob2c.exampleapp` a rendszer szerepel. Ezt a mintát kell követni. Ha két alkalmazás ugyanazt a sémát használja, a felhasználó választhat egy alkalmazás kiválasztásával. Ha a felhasználó helytelenül választ, a bejelentkezés sikertelen lesz.
    * **Teljes**: Az átirányítási URI-nak rendszerrel és elérési úttal is rendelkeznie kell. Az elérési útnak legalább egy perjelet kell tartalmaznia a tartomány után. Például, `//oauth/` működik, miközben `//oauth` nem sikerül. Ne adjon meg speciális karaktereket az URI-ban, például aláhúzásokat.
1. **Az Engedélyek csoportban**jelölje be a Rendszergazda hozzájárulása a *nyílt és offline_access engedélyekhez* jelölőnégyzetet.
1. Kattintson a **Register** (Regisztrálás) elemre.