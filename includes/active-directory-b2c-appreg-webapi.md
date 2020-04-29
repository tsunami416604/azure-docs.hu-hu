---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 9367719b1a1050ce8abf36804b3d0d0d58c7484b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78183377"
---
A webes API-erőforrásokat regisztrálni kell a bérlőn, mielőtt azok elfogadják és reagálni tudják a védett erőforrásokra vonatkozó kéréseket olyan ügyfélalkalmazások számára, amelyek hozzáférési jogkivonatot mutatnak be.

Az alkalmazások Azure AD B2C-bérlőben való regisztrálásához használhatja a jelenlegi **alkalmazásokat** , vagy az új, egyesített **Alkalmazásregisztrációk (előzetes verzió)** élményt. [További információ az új felületről](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C**lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C**lehetőséget.
1. Válassza az **alkalmazások**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.
1. Adjon nevet az alkalmazásnak. Például: *webapi1*.
1. A **Web App/web API**esetében válassza az **Igen**lehetőséget.
1. Az **implicit folyamat engedélyezéséhez**válassza az **Igen**lehetőséget.
1. A **Válasz URL-cím**mezőben adjon meg egy végpontot, amelyben Azure ad B2C vissza kell adnia az alkalmazás által kért jogkivonatokat. Ebben az oktatóanyagban a minta helyileg fut, és figyeli a `https://localhost:5000`következőt:.
1. Az **alkalmazás-azonosító URI-ja**esetében adjon hozzá egy API-végpont-azonosítót a MEGJELENÍTett URI azonosítóhoz. Ehhez az oktatóanyaghoz írja `api`be a következőt:, hogy a teljes `https://contosob2c.onmicrosoft.com/api`URI hasonló legyen.
1. Kattintson a **Létrehozás** gombra.
1. Jegyezze fel az **alkalmazás azonosítóját** egy későbbi lépésben való használatra.

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C**lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C**lehetőséget.
1. Válassza a **Alkalmazásregisztrációk (előzetes verzió)** lehetőséget, majd válassza az **új regisztráció**lehetőséget.
1. Adja meg az alkalmazás **nevét** . Például: *webapi1*.
1. Az **átirányítási URI**területen válassza a **web**lehetőséget, majd adjon meg egy végpontot, amelyben Azure ad B2C vissza kell adnia az alkalmazás által kért jogkivonatokat. Ebben az oktatóanyagban a minta helyileg fut, és figyeli a `http://localhost:5000`következőt:.
1. Kattintson a **Register** (Regisztrálás) elemre.
1. Jegyezze fel az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra.

Ezután engedélyezze az implicit engedélyezési folyamatot:

1. A **kezelés**területen válassza a **hitelesítés**lehetőséget.
1. Válassza **az új élmény kipróbálása** (ha látható) lehetőséget.
1. Az **implicit engedélyezés**területen jelölje be a **hozzáférési jogkivonatok** és az **azonosító tokenek** jelölőnégyzetet.
1. Kattintson a **Mentés** gombra.