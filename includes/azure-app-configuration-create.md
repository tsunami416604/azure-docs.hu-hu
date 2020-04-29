---
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: include
ms.date: 1/31/2020
ms.openlocfilehash: 089a199da08503e13dbd42518970c3b7ed8a984d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "77619298"
---
1. Új alkalmazás-konfigurációs tároló létrehozásához jelentkezzen be a [Azure Portalba](https://portal.azure.com). A Kezdőlap bal felső sarkában válassza az **erőforrás létrehozása**lehetőséget. A **Keresés a piactéren** mezőbe írja be az **alkalmazás konfigurációját** , és válassza az ENTER billentyűt.

    ![Alkalmazás konfigurációjának keresése](media/azure-app-configuration-create/azure-portal-search.png)

1. Válassza ki az **alkalmazás konfigurációját** a keresési eredmények közül, majd válassza a **Létrehozás**lehetőséget.

    ![Létrehozás kiválasztása](media/azure-app-configuration-create/azure-portal-app-configuration-create.png)

1. Az **alkalmazás-konfiguráció** > **létrehozása** panelen adja meg a következő beállításokat:

    | Beállítás | Ajánlott érték | Leírás |
    |---|---|---|
    | **Erőforrás neve** | Globálisan egyedi név | Adja meg az alkalmazás konfigurációs tárolási erőforrásához használandó egyedi erőforrás nevét. A névnek 5 és 50 karakter közötti sztringnek kell lennie, és csak számokat, betűket és `-` karaktert tartalmazhat. A név nem kezdődhet vagy végződhet a `-` karakterrel.  |
    | **Előfizetés** | Az Ön előfizetése | Válassza ki az alkalmazás konfigurációjának teszteléséhez használni kívánt Azure-előfizetést. Ha a fiókja csak egyetlen előfizetéssel rendelkezik, akkor automatikusan ki van választva, és az **előfizetések** listája nem jelenik meg. |
    | **Erőforráscsoport** | *AppConfigTestResources* | Válasszon ki vagy hozzon létre egy erőforráscsoportot az alkalmazás konfigurációs tárolójának erőforrásához. Ez a csoport akkor lehet hasznos, ha több olyan erőforrást szeretne szervezni, amelyet az erőforráscsoport törlésével egyszerre törölni kíván. További információ: [erőforráscsoportok használata az Azure-erőforrások kezeléséhez](/azure/azure-resource-manager/resource-group-overview). |
    | **Hely** | *USA középső régiója* | A **Location (hely** ) használatával adja meg azt a földrajzi helyet, amelyben az alkalmazás konfigurációs tárolója üzemel. A legjobb teljesítmény érdekében hozza létre az erőforrást ugyanabban a régióban, mint az alkalmazás többi összetevőjét. |
    | **Díjszabási csomag** | *Ingyenes* | Válassza ki a kívánt árképzési szintet. További részletekért tekintse meg az [alkalmazás konfigurációjának díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/app-configuration/).

    ![Alkalmazás-konfigurációs tároló erőforrásának létrehozása](media/azure-app-configuration-create/azure-portal-app-configuration-create-settings.png)

1. Kattintson a **Létrehozás** gombra. Az üzembe helyezés néhány percet is igénybe vehet.

1. Az üzembe helyezés befejeződése után válassza a **Beállítások** > **hozzáférési kulcsok**elemet. Jegyezze fel az elsődleges írásvédett kulcs-összekapcsolási karakterláncot. Ezt a kapcsolódási karakterláncot később fogja használni az alkalmazás konfigurálásához az Ön által létrehozott alkalmazás-konfigurációs tárolóval való kommunikációhoz.
