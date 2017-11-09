---
title: "Ismerkedés az Azure Advisor |} Microsoft Docs"
description: "Ismerkedés az Azure Advisor."
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: makohli
ms.openlocfilehash: dc89cd29e1e8038f0ff317ff6acee332218ebce7
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2017
---
# <a name="get-started-with-azure-advisor"></a>Ismerkedés az Azure Advisor szolgáltatással

Útmutató az Azure-portálon keresztül férnek hozzá az Advisor javaslatok beszerzése és ajánlások megvalósításával.

## <a name="get-advisor-recommendations"></a>Javaslatok az Advisor használatával kapcsolatban

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Kattintson a bal oldali ablaktáblában **Advisor**.  Ha az Advisor nem látja a bal oldali panelen, kattintson a **további szolgáltatások**.  A szolgáltatás menü ablaktáblán alatt **figyelés és felügyelet**, kattintson a **Advisor**.
 Az Advisor irányítópult jelenik meg.

   ![Hozzáférés az Azure Advisor az Azure portál használatával](./media/advisor-get-started/advisor-portal-menu.png) 

4. Az Advisor irányítópult a kijelölt előfizetéseket javaslatok összegzését jeleníti meg.  Kiválaszthatja az előfizetéseket, amelyet meg szeretne megjeleníteni az előfizetés használatára vonatkozó javaslatok legördülő lista szűréséhez.

5. Ahhoz, hogy egy adott konkrét kategóriával javaslatokat, kattintson a lapfülek egyikére: **magas rendelkezésre állású**, **biztonsági**, **teljesítmény**, vagy **költség**.
 
> [!NOTE]
> Az előfizetéshez, előfizetés Azure Advisor használandó *tulajdonos* kell indítsa el az Advisor irányítópulton.  Ez a művelet regisztrálja az előfizetés az Advisor szolgáltatásban.  Bármely előfizetés meg *tulajdonos*, *közreműködő*, vagy *olvasó* férhetnek hozzá az előfizetés az Advisor-javaslatokra.  

  ![Az Azure Advisor irányítópult](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-advisor-recommendation-details-and-implement-a-solution"></a>Az Advisor javaslat részletes, és egy megoldás bevezetésére

Igény szerint a javaslat a Advisor – például a javasolt műveletek és az érintett erőforrásokról – a további részletek megtekintéséhez és a javasolt megoldás megvalósításához.  

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com), majd nyissa meg [Advisor](https://aka.ms/azureadvisordashboard).

2. Válassza ki az adott kategóriába javaslatok listájának megjelenítéséhez, vagy válasszon egy javaslat kategóriát a **összes** fülre kattintva megtekintheti a javaslatok.

3. Kattintson a azt ajánljuk, hogy meg szeretné tekinteni részletesen.

4. Tekintse át az információkat az ajánlás és az erőforrásokat, amelyek a javaslat vonatkozik.

5. Kattintson a **javasolt művelet** az ajánlás végrehajtásához.

## <a name="filter-advisor-recommendations"></a>Szűrő javaslatokat biztosít

Mi az Ön számára legfontosabb lebontva ajánlásokat jeleníthetők meg.  Az előfizetés, erőforrás típusa vagy javaslat állapot szerint szűrheti.  

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com), majd nyissa meg [Advisor](https://aka.ms/azureadvisordashboard).

2.  Előfizetés, erőforrás típusa vagy javaslat állapot szűréséhez használja az Advisor irányítópult valamelyik legördülő listában.

    ![Az Advisor keresési-szűrési feltételeket](./media/advisor-get-started/advisor-filters.png)

## <a name="snooze-or-dismiss-advisor-recommendations"></a>Emlékeztet, vagy hagyja figyelmen kívül az Advisor-javaslatokra

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com), majd nyissa meg [Advisor](https://aka.ms/azureadvisordashboard).

2. Nyissa meg a javaslat emlékeztet, vagy hagyja figyelmen kívül.

3. Kattintson a javaslat.

4. Kattintson a **emlékeztet**. 

5. Adjon meg egy emlékeztető időszakra vonatkozóan, vagy válasszon **soha** elvetni a javaslat.

## <a name="exclude-subscriptions-or-resource-groups-from-advisor"></a>Az Advisor előfizetések vagy erőforráscsoportok kizárása

Előfordulhat, hogy erőforráscsoportok vagy előfizetések, amelyek nem szeretné fogadni az Advisor-javaslatokra – például a "teszt".  Konfigurálhatja az Advisor csak az adott előfizetésekhez és erőforráscsoportokhoz javaslatok létrehozni.

> [!NOTE]
> Vagy kizárja a egy előfizetéshez vagy erőforráscsoporthoz Advisor szolgáltatásból való, egy előfizetés tulajdonosának kell lennie.  Ha nincs a megfelelő engedélyekkel a egy előfizetéshez vagy erőforráscsoporthoz, a bevonhat vagy kizárhat az le van tiltva a felhasználói felületen.

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com), majd nyissa meg [Advisor](https://aka.ms/azureadvisordashboard).

2. Kattintson a **konfigurálása** műveletsávon.

3. Törölje a jelet, semmilyen előfizetések vagy nem szeretne kapni az Advisor-javaslatokra erőforráscsoportok.

    ![Az Advisor konfigurálása erőforrások – példa](./media/advisor-get-started/advisor-configure-resources.png)

4. Kattintson a **alkalmaz** gombra.

## <a name="configure-the-average-cpu-utilization-rule-for-the-low-usage-virtual-machine-recommendation"></a>A kevésbé aktív a virtuális gép javaslat a átlagos CPU kihasználtsága szabály konfigurálása

Az Advisor figyeli a virtuális gépek használatának 14 napja, és majd azonosítja a kis-kihasználtságának virtuális gépek. Virtuális gépek, amelyek átlagos processzorkihasználtság csak 5 % vagy kevesebb és hálózati aktivitás a 7 MB vagy kisebb a négy vagy több napot minősülnek alacsony használt virtuális gépek.

Ha azt szeretné, kell lennie a szigorúbb kevésbé aktív virtuális gépek meghatározása, módosíthatja egy előfizetés alapon átlagos CPU kihasználtsága szabály.  Az átlagos CPU-kihasználtsági szabály 5 %, 10, 15 % vagy 20 % állítható be.

> [!NOTE]
> Állítsa be a átlagos CPU kihasználtsága szabály kevésbé aktív virtuális gépek azonosításához, előfizetés kell *tulajdonos*.  Ha nincs a megfelelő engedélyekkel a egy előfizetéshez vagy erőforráscsoporthoz, is bevonhat vagy kizárhat, hogy letiltja a felhasználói felületen. 

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com), majd nyissa meg [Advisor](https://aka.ms/azureadvisordashboard).

2. Kattintson a **konfigurálása** műveletsávon.

3. Kattintson a **szabályok** fülre.

4. Válassza ki szeretné vonatkozó átlagos CPU kihasználtsága szabály beállítása, és kattintson az előfizetések **szerkesztése**.

5. Válassza ki a kívánt átlagos CPU-kihasználtság értéket, és kattintson a **alkalmaz**.

6. Kattintson a **javaslatok frissítése** frissítésére a meglévő javaslatok a új átlagos CPU-kihasználtság szabály. 

   ![Az Advisor javaslat szabályok példa konfigurálása](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-your-advisor-recommendations"></a>Töltse le az Advisor-javaslatokra

Az Advisor lehetővé teszi, hogy töltse le a javaslatok összegzését.  A javaslatok, PDF-fájl vagy a CSV-fájl tölthető le.  A javaslatok letöltése lehetővé teszi könnyedén megoszthatja munkatársaival, vagy a saját elemzést az ajánlás adatok felett.

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com), majd nyissa meg [Advisor](https://aka.ms/azureadvisordashboard).

2. Kattintson a **letöltése CSV-ként** vagy **PDF letöltéséhez** művelet sávján látható.

A letöltési beállítás tiszteletben tartja az Advisor irányítópult alkalmazott szűrőket.  Ha egy adott javaslat kategóriát vagy javaslat megtekintésekor a letöltési lehetőséget választja, a letöltött összegzés csak az adott kategória vagy javaslat adatait tartalmazza. 

## <a name="next-steps"></a>Következő lépések

Az Advisor kapcsolatos további információkért lásd:
* [Bevezetés az Azure Advisor](advisor-overview.md)
* [Magas rendelkezésre állású javaslatokat biztosít](advisor-high-availability-recommendations.md)
* [Biztonsági javaslatokat biztosít](advisor-security-recommendations.md)
-  [Teljesítmény javaslatokat biztosít](advisor-performance-recommendations.md)
* [Költség javaslatokat biztosít](advisor-performance-recommendations.md)
