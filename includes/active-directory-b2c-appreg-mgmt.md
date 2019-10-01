---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 10/01/2019
ms.author: marsma
ms.openlocfilehash: 67a0832f868fecd47983aa8cddff9cdf139b3f2a
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71702160"
---
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure Active Directory** (*nem* Azure ad B2C) lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki **Azure Active Directory**.
1. A **kezelés**területen válassza a **Alkalmazásregisztrációk (örökölt)** lehetőséget.
1. Válassza az **Új alkalmazás regisztrálása** elemet.
1. Adja meg az alkalmazás nevét. Például: *managementapp1*.
1. Az **alkalmazás típusa**mezőben válassza a **Web App/API**lehetőséget.
1. Írjon be egy érvényes URL-címet a **bejelentkezési URL-cím**mezőbe. Például: `https://localhost`. A végpontnak nem kell elérhetőnek lennie, de érvényes URL-címnek kell lennie.
1. Kattintson a **Létrehozás** gombra.
1. Jegyezze fel a **regisztrált** alkalmazás – áttekintés oldalon megjelenő **Application ID** -t. Ezt az értéket egy későbbi lépésben kell használni.
