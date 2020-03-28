---
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: include
ms.date: 1/31/2020
ms.openlocfilehash: 089a199da08503e13dbd42518970c3b7ed8a984d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77619298"
---
1. Új alkalmazáskonfigurációs áruház létrehozásához jelentkezzen be az [Azure Portalra.](https://portal.azure.com) A kezdőlap bal felső sarkában válassza az **Erőforrás létrehozása**lehetőséget. A **Keresés a piactéren** mezőbe írja be az **Alkalmazás konfigurációja** parancsot, és válassza az Enter lehetőséget.

    ![Alkalmazáskonfiguráció keresése](media/azure-app-configuration-create/azure-portal-search.png)

1. A keresési eredmények között válassza az **Alkalmazáskonfiguráció** lehetőséget, majd válassza a **Létrehozás gombot.**

    ![Válassza a Létrehozás lehetőséget](media/azure-app-configuration-create/azure-portal-app-configuration-create.png)

1. Az **Alkalmazáskonfiguráció** > **létrehozása** ablaktáblán adja meg a következő beállításokat:

    | Beállítás | Ajánlott érték | Leírás |
    |---|---|---|
    | **Erőforrás neve** | Globálisan egyedi név | Adja meg az Alkalmazáskonfigurációs tároló erőforráshoz használandó egyedi erőforrásnevet. A név nek 5 és 50 karakter közötti karakterláncnak kell `-` lennie, és csak számokat, betűket és karaktert tartalmazhat. A név nem kezdődhet `-` és nem végződhet a karakterrel.  |
    | **Előfizetés** | Az Ön előfizetése | Válassza ki az Azure-előfizetést, amelyet az alkalmazáskonfiguráció teszteléséhez kíván használni. Ha a fiókjához csak egy előfizetés tartozik, a rendszer automatikusan kiválasztja, és az **Előfizetések** lista nem jelenik meg. |
    | **Erőforráscsoport** | *AppConfigTesterőforrások* | Jelöljön ki vagy hozzon létre egy erőforráscsoportot az Alkalmazáskonfigurációs tároló erőforrásához. Ez a csoport több olyan erőforrás rendszerezéséhez hasznos, amelyeket az erőforráscsoport törlésével egyszerre szeretne törölni. További információ: [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez.](/azure/azure-resource-manager/resource-group-overview) |
    | **Helyen** | *USA középső régiója* | A **Hely** szolgáltatással megadhatja azt a földrajzi helyet, ahol az alkalmazás konfigurációs tárolója található. A legjobb teljesítmény érdekében hozza létre az erőforrást ugyanabban a régióban, mint az alkalmazás más összetevői. |
    | **Tarifacsomag** | *Ingyenes* | Válassza ki a kívánt tarifacsomagot. További részletekért tekintse meg az [Alkalmazáskonfiguráció díjszabási oldalát.](https://azure.microsoft.com/pricing/details/app-configuration/)

    ![Alkalmazáskonfigurációs tároló erőforrás létrehozása](media/azure-app-configuration-create/azure-portal-app-configuration-create-settings.png)

1. Kattintson a **Létrehozás** gombra. A telepítés eltarthat néhány percig.

1. A telepítés befejezése után válassza a Hozzáférési**kulcsok** **beállítása** > lehetőséget. Jegyezze fel az elsődleges írásvédett kulcskapcsolati karakterláncot. Ezt a kapcsolati karakterláncot később fogja használni az alkalmazás konfigurálásához, hogy kommunikáljon a létrehozott alkalmazáskonfigurációs tárolóval.
