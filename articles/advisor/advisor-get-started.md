---
title: Az Azure Advisor használatának első lépései |} A Microsoft Docs
description: Ismerkedés az Azure Advisor.
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: kasparks
ms.openlocfilehash: a7e82fffdd9c865de6040c05ec28bc8bd2dced61
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58111395"
---
# <a name="get-started-with-azure-advisor"></a>Ismerkedés az Azure Advisor szolgáltatással

Ismerje meg, hogyan Advisor eléréséhez az Azure Portalon keresztül, a javaslatokat kaphat és ajánlások megvalósítása.

> [!NOTE]
> Az Azure Advisor automatikusan fut a háttérben keresse meg az újonnan létrehozott erőforrásokat. Javaslatok meg ezeket az erőforrásokat akár 24 órát is igénybe vehet.

## <a name="get-recommendations"></a>Javaslatok

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali ablaktáblán kattintson a **Advisor**.  Ha nem látja az Advisor a bal oldali ablaktáblán, kattintson a **minden szolgáltatás**.  A szolgáltatás menüjében panelen alatt **megfigyelési és felügyeleti**, kattintson a **Advisor**. Az Advisor irányítópult jelenik meg.

   ![Hozzáférés az Azure Advisor az Azure portal használatával](./media/advisor-get-started/advisor-portal-menu.png) 

1. Az Advisor irányítópult az összes kiválasztott előfizetés javaslatok összegzését jeleníti meg.  Választhat a javaslatok az előfizetés használatával megjeleníteni kívánt előfizetéseket szűrőjének legördülő listája.

1. Egy adott kategória javaslatokat beolvasni, kattintson az a lapfülek egyikére: **Magas rendelkezésre állású**, **biztonsági**, **teljesítmény**, vagy **költség**. 

   ![Az Azure Advisor-irányítópult](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-recommendation-details-and-implement-a-solution"></a>Első javaslat részletei és a egy megoldás megvalósítása

Az Advisor – a javaslatokat és az érintett erőforrások – például a további részletek megtekintéséhez és a javaslat a megoldás megvalósításához válasszon ki egy javaslatot.  

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), majd nyissa meg [Advisor](https://aka.ms/azureadvisordashboard).

1. Válassza ki az adott kategórián belül a javaslatok listájának megjelenítéséhez, vagy válasszon egy javaslat kategóriát a **összes** fülre kattintva megtekintheti az összes javaslatot.

1. Kattintson egy javaslatot, hogy meg szeretné tekinteni a részleteket.

1. Tekintse át a javaslatot, és az erőforrásokat, amelyekre vonatkozik a javaslat adatait.

1. Kattintson a **javasolt művelet** történő megvalósításához.

## <a name="filter-recommendations"></a>Szűrő javaslatok

Mi az Ön számára legfontosabb lehatolhat javaslatok szűrheti.  Előfizetést, erőforrástípust vagy a javaslat állapota szerint szűrheti.  

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), majd nyissa meg [Advisor](https://aka.ms/azureadvisordashboard).

1. Az Advisor irányítópult a legördülő menük használatával előfizetést, erőforrástípust vagy a javaslat állapota alapján.

    ![Az Advisor search-szűrési feltételeket](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-recommendations"></a>Elhalaszthatja vagy javaslatok elvetése

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), majd nyissa meg [Advisor](https://aka.ms/azureadvisordashboard).

1. Keresse meg az ajánlás elhalasztása, vagy hagyja figyelmen kívül.

1. A javaslatra kattint.

1. Kattintson a **elhalasztása**. 

1. Adjon meg egy elhalasztás időszakot, vagy válasszon **soha** elvetése a javaslatot.

## <a name="exclude-subscriptions-or-resource-groups"></a>Előfizetések vagy erőforráscsoportok kizárása

Előfordulhat, hogy erőforráscsoport vagy előfizetés, amelyeket nem szeretne kapni az Advisor-javaslatok – például a "teszt".  Konfigurálhatja az Advisor csak az adott előfizetésekhez és erőforráscsoportokhoz az ajánlások előállításához.

> [!NOTE]
> Vagy egy előfizetésen vagy erőforráscsoporton kizárja az Advisor, egy előfizetés tulajdonosának kell lennie.  Ha nem rendelkezik a szükséges engedélyekkel, erőforráscsoport vagy előfizetés, bevonhat vagy kizárhat, hogy a beállítás le van tiltva a felhasználói felületen.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), majd nyissa meg [Advisor](https://aka.ms/azureadvisordashboard).

1. Kattintson a **konfigurálása** a művelet sávon.

1. Törölje a jelet bármely előfizetések vagy erőforráscsoportok nem szeretne kapni az Advisor-javaslatok.

    ![Az Advisor konfigurálása erőforrások példa](./media/advisor-get-started/advisor-configure-resources.png)

1. Kattintson a **alkalmaz** gombra.

## <a name="configure-low-usage-vm-recommendation"></a>Az alacsony kihasználtságú virtuális gépekre vonatkozó javaslatot konfigurálása

Ezzel az eljárással az átlagos CPU kihasználtsága szabály az alacsony kihasználtságú virtuális gépre vonatkozó javaslatok.

Az Advisor 14 nap a virtuális gép használatát figyeli, és alacsony-kihasználtság virtuális gépek azonosítja. Virtuális gépek amelynek átlagos processzorhasználat 5 %-os vagy kisebb, és a hálózati forgalom 7 MB vagy belül a négy vagy több napot számítanak a kis-kihasználtság virtuális gépeket.

Ha szeretné, az alacsony kihasználtságú virtuális gépek azonosítása agresszívebb legyen, módosíthatja a átlagos CPU kihasználtsága szabály minden előfizetés.  A átlagos CPU-kihasználtság szabály állítható 5 %-os, 10 %-os, 15 %-os vagy 20 %-át.

> [!NOTE]
> Módosíthatja a Processzor átlagos kihasználtsága szabály alacsony kihasználtságú virtuális gépek azonosítására szolgáló, rendelkeznie kell egy előfizetés *tulajdonosa*.  Ha nem rendelkezik a szükséges engedélyeket egy előfizetést, vagy az erőforráscsoportot, vagy kizárja a azt a beállítást letiltja a felhasználói felületen. 

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), majd nyissa meg [Advisor](https://aka.ms/azureadvisordashboard).

1. Kattintson a **konfigurálása** a művelet sávon.

1. Kattintson a **szabályok** fülre.

1. Válassza ki az előfizetéseket, az átlagos CPU kihasználtsága szabály beállítása, és kattintson a kívánt **szerkesztése**.

1. Válassza ki a kívánt átlagos CPU-kihasználtsági értéket, és kattintson a **alkalmaz**.

1. Kattintson a **javaslatok frissítése** frissítése az új átlagos CPU-kihasználtság szabály használata a meglévő javaslatokat. 

   ![Az Advisor konfigurálása javaslat szabályok példa](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-recommendations"></a>Javaslatok letöltése

Az Advisor lehetővé teszi, hogy töltse le a javaslatok összegzését.  A javaslatok egy PDF-fájlt vagy CSV-fájl is letölthető.  A javaslatok letöltése lehetővé teszi, hogy könnyedén megoszthatja munkatársaival, vagy saját elemzését az ajánlás adatok felett.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), majd nyissa meg [Advisor](https://aka.ms/azureadvisordashboard).

1. Kattintson a **letöltése CSV-fájlként** vagy **letöltése PDF-fájlként** elemre a művelet sávon.

A letöltési lehetőséget tiszteletben tartja az Advisor irányítópult alkalmazott szűrők.  Ha egy adott javaslatot kategória vagy ajánlás megtekintése közben a letöltési lehetőséget választja, az a letöltött összegzése csak az adott kategória vagy javaslat adatait tartalmazza. 

## <a name="next-steps"></a>További lépések

Az Advisor kapcsolatos további információkért lásd:

- [Bevezetés az Azure Advisor](advisor-overview.md)
- [Az Advisor magas rendelkezésre állás – javaslatok](advisor-high-availability-recommendations.md)
- [Az Advisor biztonsági javaslatok](advisor-security-recommendations.md)
- [Advisor-teljesítményajánlások](advisor-performance-recommendations.md)
- [Az Advisor díjakkal kapcsolatos ajánlások](advisor-performance-recommendations.md)
