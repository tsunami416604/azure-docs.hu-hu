---
title: Az Azure Advisor használatának első lépései |} A Microsoft Docs
description: Ismerkedés az Azure Advisor.
services: advisor
documentationcenter: NA
author: kasparks
manager: ''
ms.assetid: ''
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: kasparks
ms.openlocfilehash: 6e66fed21223701cd6c61bd1e903b4e7d7fbe0d0
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850091"
---
# <a name="get-started-with-azure-advisor"></a>Ismerkedés az Azure Advisor szolgáltatással

Ismerje meg, hogyan Advisor eléréséhez az Azure Portalon keresztül, a javaslatokat kaphat és ajánlások megvalósítása.

## <a name="get-advisor-recommendations"></a>Javaslatok az Advisor használatával kapcsolatban

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A bal oldali ablaktáblán kattintson a **Advisor**.  Ha nem látja az Advisor a bal oldali ablaktáblán, kattintson a **minden szolgáltatás**.  A szolgáltatás menüjében panelen alatt **megfigyelési és felügyeleti**, kattintson a **Advisor**.
 Az Advisor irányítópult jelenik meg.

   ![Hozzáférés az Azure Advisor az Azure portal használatával](./media/advisor-get-started/advisor-portal-menu.png) 

4. Az Advisor irányítópult az összes kiválasztott előfizetés javaslatok összegzését jeleníti meg.  Választhat a javaslatok az előfizetés használatával megjeleníteni kívánt előfizetéseket szűrőjének legördülő listája.

5. Egy adott kategória javaslatokat beolvasni, kattintson az a lapfülek egyikére: **magas rendelkezésre állású**, **biztonsági**, **teljesítmény**, vagy **költség**. 

  ![Az Azure Advisor-irányítópult](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-advisor-recommendation-details-and-implement-a-solution"></a>Az Advisor-javaslat részletei kaphat, és a megoldás megvalósítása

Az Advisor – a javaslatokat és az érintett erőforrások – például a további részletek megtekintéséhez és a javaslat a megoldás megvalósításához válasszon ki egy javaslatot.  

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), majd nyissa meg [Advisor](https://aka.ms/azureadvisordashboard).

2. Válassza ki az adott kategórián belül a javaslatok listájának megjelenítéséhez, vagy válasszon egy javaslat kategóriát a **összes** fülre kattintva megtekintheti az összes javaslatot.

3. Kattintson egy javaslatot, hogy meg szeretné tekinteni a részleteket.

4. Tekintse át a javaslatot, és az erőforrásokat, amelyekre vonatkozik a javaslat adatait.

5. Kattintson a **javasolt művelet** történő megvalósításához.

## <a name="filter-advisor-recommendations"></a>Szűrés az Advisor-javaslatok

Mi az Ön számára legfontosabb lehatolhat javaslatok szűrheti.  Előfizetést, erőforrástípust vagy a javaslat állapota szerint szűrheti.  

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), majd nyissa meg [Advisor](https://aka.ms/azureadvisordashboard).

2.  Az Advisor irányítópult a legördülő menük használatával előfizetést, erőforrástípust vagy a javaslat állapota alapján.

    ![Az Advisor search-szűrési feltételeket](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-advisor-recommendations"></a>Halassza el, vagy hagyja figyelmen kívül az Advisor-javaslatok

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), majd nyissa meg [Advisor](https://aka.ms/azureadvisordashboard).

2. Keresse meg az ajánlás elhalasztása, vagy hagyja figyelmen kívül.

3. A javaslatra kattint.

4. Kattintson a **elhalasztása**. 

5. Adjon meg egy elhalasztás időszakot, vagy válasszon **soha** elvetése a javaslatot.

## <a name="exclude-subscriptions-or-resource-groups-from-advisor"></a>Az Advisor előfizetések vagy erőforráscsoportok kizárása

Előfordulhat, hogy erőforráscsoport vagy előfizetés, amelyeket nem szeretne kapni az Advisor-javaslatok – például a "teszt".  Konfigurálhatja az Advisor csak az adott előfizetésekhez és erőforráscsoportokhoz az ajánlások előállításához.

> [!NOTE]
> Vagy egy előfizetésen vagy erőforráscsoporton kizárja az Advisor, egy előfizetés tulajdonosának kell lennie.  Ha nem rendelkezik a szükséges engedélyekkel, erőforráscsoport vagy előfizetés, bevonhat vagy kizárhat, hogy a beállítás le van tiltva a felhasználói felületen.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), majd nyissa meg [Advisor](https://aka.ms/azureadvisordashboard).

2. Kattintson a **konfigurálása** a művelet sávon.

3. Törölje a jelet bármely előfizetések vagy erőforráscsoportok nem szeretne kapni az Advisor-javaslatok.

    ![Az Advisor konfigurálása erőforrások példa](./media/advisor-get-started/advisor-configure-resources.png)

4. Kattintson a **alkalmaz** gombra.

## <a name="configure-the-average-cpu-utilization-rule-for-the-low-usage-virtual-machine-recommendation"></a>Az alacsony kihasználtságú virtuális gépre vonatkozó javaslatok átlagos CPU-kihasználtság szabályának konfigurálása

Az Advisor 14 nap a virtuális gép használatát figyeli, és alacsony-kihasználtság virtuális gépek azonosítja. Virtuális gépek amelynek átlagos processzorhasználat 5 %-os vagy kisebb, és a hálózati forgalom 7 MB vagy belül a négy vagy több napot számítanak a kis-kihasználtság virtuális gépeket.

Ha szeretné, az alacsony kihasználtságú virtuális gépek azonosítása agresszívebb legyen, módosíthatja a átlagos CPU kihasználtsága szabály minden előfizetés.  A átlagos CPU-kihasználtság szabály állítható 5 %-os, 10 %-os, 15 %-os vagy 20 %-át.

> [!NOTE]
> Módosíthatja a Processzor átlagos kihasználtsága szabály alacsony kihasználtságú virtuális gépek azonosítására szolgáló, rendelkeznie kell egy előfizetés *tulajdonosa*.  Ha nem rendelkezik a szükséges engedélyeket egy előfizetést, vagy az erőforráscsoportot, vagy kizárja a azt a beállítást letiltja a felhasználói felületen. 

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), majd nyissa meg [Advisor](https://aka.ms/azureadvisordashboard).

2. Kattintson a **konfigurálása** a művelet sávon.

3. Kattintson a **szabályok** fülre.

4. Válassza ki az előfizetéseket, az átlagos CPU kihasználtsága szabály beállítása, és kattintson a kívánt **szerkesztése**.

5. Válassza ki a kívánt átlagos CPU-kihasználtsági értéket, és kattintson a **alkalmaz**.

6. Kattintson a **javaslatok frissítése** frissítése az új átlagos CPU-kihasználtság szabály használata a meglévő javaslatokat. 

   ![Az Advisor konfigurálása javaslat szabályok példa](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-your-advisor-recommendations"></a>Töltse le az Advisor-javaslatok

Az Advisor lehetővé teszi, hogy töltse le a javaslatok összegzését.  A javaslatok egy PDF-fájlt vagy CSV-fájl is letölthető.  A javaslatok letöltése lehetővé teszi, hogy könnyedén megoszthatja munkatársaival, vagy saját elemzését az ajánlás adatok felett.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), majd nyissa meg [Advisor](https://aka.ms/azureadvisordashboard).

2. Kattintson a **letöltése CSV-fájlként** vagy **letöltése PDF-fájlként** elemre a művelet sávon.

A letöltési lehetőséget tiszteletben tartja az Advisor irányítópult alkalmazott szűrők.  Ha egy adott javaslatot kategória vagy ajánlás megtekintése közben a letöltési lehetőséget választja, az a letöltött összegzése csak az adott kategória vagy javaslat adatait tartalmazza. 

## <a name="next-steps"></a>További lépések

Az Advisor kapcsolatos további információkért lásd:
* [Bevezetés az Azure Advisor](advisor-overview.md)
* [Az Advisor magas rendelkezésre állás – javaslatok](advisor-high-availability-recommendations.md)
* [Az Advisor biztonsági javaslatok](advisor-security-recommendations.md)
-  [Advisor-teljesítményajánlások](advisor-performance-recommendations.md)
* [Az Advisor díjakkal kapcsolatos ajánlások](advisor-performance-recommendations.md)
