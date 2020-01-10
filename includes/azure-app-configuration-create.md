---
author: yegu
ms.author: yegu
ms.service: azure-app-configuration
ms.topic: include
ms.date: 12/03/2019
ms.openlocfilehash: ceeeb5ee155624e050f36e733a464c2cb21db88c
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750292"
---
1. Új alkalmazás-konfigurációs tároló létrehozásához jelentkezzen be a [Azure Portalba](https://portal.azure.com). A Kezdőlap bal felső sarkában válassza az **erőforrás létrehozása**lehetőséget. A **Keresés a piactéren** mezőbe írja be az **alkalmazás konfigurációját** , és válassza az ENTER billentyűt.

    ![Alkalmazás konfigurációjának keresése](media/azure-app-configuration-create/azure-portal-search.png)

1. Válassza ki az **alkalmazás konfigurációját** a keresési eredmények közül, majd válassza a **Létrehozás**lehetőséget.

    ![Létrehozás kiválasztása](media/azure-app-configuration-create/azure-portal-app-configuration-create.png)

1. Az **alkalmazás konfigurációja** > **Létrehozás** ablaktáblán adja meg a következő beállításokat:

    | Beállítás | Ajánlott érték | Leírás |
    |---|---|---|
    | **Erőforrás neve** | Globálisan egyedi név | Adja meg az alkalmazás konfigurációs tárolási erőforrásához használandó egyedi erőforrás nevét. A névnek 1 és 63 karakter közötti sztringnek kell lennie, és kizárólag számokat, betűket és a `-` karaktert tartalmazhatja. A név nem kezdődhet vagy végződhet a `-` karakterrel, és az egymást követő `-` karakterek nem érvényesek.  |
    | **Előfizetés** | Az Ön előfizetése | Válassza ki az alkalmazás konfigurációjának teszteléséhez használni kívánt Azure-előfizetést. Ha a fiókja csak egyetlen előfizetéssel rendelkezik, akkor automatikusan ki van választva, és az **előfizetések** listája nem jelenik meg. |
    | **Erőforráscsoport** | *AppConfigTestResources* | Válasszon ki vagy hozzon létre egy erőforráscsoportot az alkalmazás konfigurációs tárolójának erőforrásához. Ez a csoport akkor lehet hasznos, ha több olyan erőforrást szeretne szervezni, amelyet az erőforráscsoport törlésével egyszerre törölni kíván. További információkat az [Azure-erőforrások erőforráscsoportokkal való kezeléséről](/azure/azure-resource-manager/resource-group-overview) szóló cikkben olvashat. |
    | **Hely** | *USA középső régiója* | A **Location (hely** ) használatával adja meg azt a földrajzi helyet, amelyben az alkalmazás konfigurációs tárolója üzemel. A legjobb teljesítmény érdekében hozza létre az erőforrást ugyanabban a régióban, mint az alkalmazás többi összetevőjét. |

    ![Alkalmazás-konfigurációs tároló erőforrásának létrehozása](media/azure-app-configuration-create/azure-portal-app-configuration-create-settings.png)

1. Kattintson a **Létrehozás** gombra. Az üzembe helyezés néhány percet is igénybe vehet.

1. Az üzembe helyezés befejeződése után válassza a **beállítások** > **hozzáférési kulcsok**elemet. Jegyezze fel az elsődleges írásvédett kulcs-összekapcsolási karakterláncot. Ezt a kapcsolódási karakterláncot később fogja használni az alkalmazás konfigurálásához az Ön által létrehozott alkalmazás-konfigurációs tárolóval való kommunikációhoz.
