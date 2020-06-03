---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: bbf1daf3a70e5ca87b4a0027b53aed5fab5691d1
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/02/2020
ms.locfileid: "84317543"
---
Az alkalmazások Azure AD B2C-bérlőben való regisztrálásához használhatja az új, egységes **Alkalmazásregisztrációk** -élményt vagy az örökölt **alkalmazások (örökölt)** felületét. [További információ az új felületről](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[Alkalmazásregisztrációk](#tab/app-reg-ga/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C**lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C**lehetőséget.
1. Válassza a **Alkalmazásregisztrációk**lehetőséget, majd válassza az **új regisztráció**lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: *testapp1*.
1. Válassza **a fiókok lehetőséget bármely szervezeti címtárban vagy bármely identitás-szolgáltatóban**.
1. Az **átirányítási URI**területen válassza a **web**lehetőséget, majd írja be `https://jwt.ms` az URL-cím szövegmezőbe.
1. Az **engedélyek**területen jelölje be a *rendszergazdai jóváhagyás megadása az OpenID-hez és a offline_access engedélyekhez* jelölőnégyzetet.
1. Kattintson a **Register** (Regisztrálás) elemre.

Az alkalmazás regisztrációjának befejezése után engedélyezze az implicit engedélyezési folyamatot:

1. A **kezelés**területen válassza a **hitelesítés**lehetőséget.
1. Válassza **az új élmény kipróbálása** (ha látható) lehetőséget.
1. Az **implicit engedélyezés**területen jelölje be a **hozzáférési jogkivonatok** és az **azonosító tokenek** jelölőnégyzetet.
1. Kattintson a **Mentés** gombra.

#### <a name="applications-legacy"></a>[Alkalmazások (örökölt)](#tab/applications-legacy/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C**lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C**lehetőséget.
1. Válassza az **alkalmazások**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.
1. Adjon nevet az alkalmazásnak. Például: *testapp1*.
1. A **Web App/web API**esetében válassza az **Igen**lehetőséget.
1. A **Válasz URL-cím**mezőbe írja be a következőt:`https://jwt.ms`
1. Kattintson a **Létrehozás** gombra.