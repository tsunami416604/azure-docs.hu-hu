---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 09/30/2019
ms.author: marsma
ms.openlocfilehash: 4ac69b51e69031a1a4ce83a2c09dc6cc010ccd62
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720274"
---
A webes API-erőforrásokat regisztrálni kell a bérlőn, mielőtt azok elfogadják és reagálni tudják a védett erőforrásokra vonatkozó kéréseket olyan ügyfélalkalmazások számára, amelyek hozzáférési jogkivonatot mutatnak be.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C**lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C**lehetőséget.
1. Válassza az **alkalmazások**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.
1. Adja meg az alkalmazás nevét. Például: *webapi1*.
1. A **Web App/web API**esetében válassza az **Igen**lehetőséget.
1. Az **implicit folyamat engedélyezéséhez**válassza az **Igen**lehetőséget.
1. A **Válasz URL-cím**mezőben adjon meg egy végpontot, amelyben Azure ad B2C vissza kell adnia az alkalmazás által kért jogkivonatokat. Ebben az oktatóanyagban a minta helyileg fut, és figyeli a `https://localhost:5000`következőt:.
1. Az **alkalmazás-azonosító URI-ja**esetében adjon hozzá egy API-végpont-azonosítót a MEGJELENÍTett URI azonosítóhoz. Ebben az oktatóanyagban írja be a `api` értéket, hogy a teljes URI a `https://contosob2c.onmicrosoft.com/api` értékhez hasonlítson.
1. Kattintson a **Létrehozás** gombra.
1. Jegyezze fel az **alkalmazás azonosítóját** egy későbbi lépésben való használatra.
