---
title: Figyelési megoldások a Azure Monitorban | Microsoft Docs
description: A Azure Monitor figyelési megoldásai olyan logikai, vizualizációs és adatgyűjtési szabályok gyűjteményei, amelyek egy adott problémás terület körüli metrikákat biztosítanak.  Ez a cikk a figyelési megoldások telepítésével és használatával kapcsolatos információkat tartalmaz.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: bwren
ms.openlocfilehash: 6932f9f302bf1816842d78a662f01ebaafed8686
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989214"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>A Azure Monitor figyelési megoldásai
A monitorozási megoldások kihasználják az Azure szolgáltatásait, hogy további információkat szolgáltassanak egy adott alkalmazás vagy szolgáltatás működéséről. Ez a cikk rövid áttekintést nyújt az Azure-beli monitorozási megoldásokról, valamint a használatáról és telepítéséről.

> [!NOTE]
> A figyelési megoldásokat korábban felügyeleti megoldásoknak nevezzük.

A figyelési megoldások jellemzően gyűjtik a naplózási adatokat, és lekérdezéseket és nézeteket biztosítanak az összegyűjtött adatok elemzéséhez. Azure Automation és az alkalmazáshoz vagy szolgáltatáshoz kapcsolódó műveleteket, például más szolgáltatások is előfordulhat, hogy kihasználja.

A Azure Monitor a használt alkalmazásokhoz és szolgáltatásokhoz is hozzáadhat figyelési megoldásokat. Általában érhetők el, nem tudta meghívni a használati díjak költség azonban gyűjt adatokat. A Microsoft megoldásai mellett partnerei és ügyfelei is [felügyeleti megoldásokat hozhat létre](solutions-creating.md) saját környezetben használja, vagy szeretné elérhetővé tenni az ügyfelek számára, a közösségi.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="use-monitoring-solutions"></a>Monitorozási megoldások használata
Nyissa meg Azure Monitor **Áttekintés** lapját, és jelenítse meg a munkaterületen telepített összes megoldás csempéjét. 

1. Jelentkezzen be az Azure Portalra.
1. Nyissa meg **minden szolgáltatás** , és keresse meg **figyelő**.
1. Alatt a **Insights** menüjében válassza **további**.
1. A munkaterületet, vagy az időtartomány, a csempék használt módosításához használja a képernyő tetején a legördülő mezőkben.
1. Kattintson a csempére a részletes elemzés tartalmazó nézet megnyitásához a megoldás az összegyűjtött adatokat.

![Áttekintés](media/solutions/overview.png)

A figyelési megoldások több típusú Azure-erőforrást is tartalmazhatnak, és a megoldáshoz tartozó összes erőforrást ugyanúgy megtekintheti, mint bármely más erőforrást. Például a megoldásban szereplő összes naplózási lekérdezés megjelenik a lekérdezési lekérdezésekben a [](../log-query/get-started-portal.md#load-queries) **megoldás-lekérdezések** területen, ha ad hoc elemzést végez a [naplók](../log-query/log-query-overview.md)lekérdezésével.

## <a name="list-installed-monitoring-solutions"></a>Telepített figyelési megoldások listázása 
Az alábbi eljárás segítségével listázhatja az előfizetésében telepített figyelési megoldásokat.

1. Jelentkezzen be az Azure Portalra.
1. Nyissa meg **minden szolgáltatás** , és keresse meg **megoldások**.
4. Minden olyan a munkaterületen telepített megoldások jelennek meg. A megoldás nevét a-ben telepített munkaterület neve követi.
1. A képernyő tetején a legördülő mezőkben használatával vagy erőforráscsoportonként szűrés.


![Az összes megoldáshoz listázása](media/solutions/list-solutions-all.png)

Kattintson az összefoglalás lapon nyissa meg a megoldás nevére. Ezen a lapon a megoldásban szereplő bármely nézetek megjelenítése, és a megoldás különböző lehetőségeket kínál a saját maga és a munkaterületen. A megoldás összefoglaló lapjának megtekintéséhez a fenti eljárásokat lista megoldások egyikének használatával, és kattintson a megoldás nevére.

![Megoldás tulajdonságai](media/solutions/solution-properties.png)



## <a name="install-a-monitoring-solution"></a>Figyelési megoldás telepítése
A Microsoft és partnerei által kínált figyelési megoldások az [Azure piactéren](https://azuremarketplace.microsoft.com)érhetők el. Keresés az elérhető megoldások, és telepítse őket a következő eljárás használatával. Amikor telepít egy megoldást, ki kell választania egy [Log Analytics-munkaterület](../platform/manage-access.md) , a megoldás telepítve lesz, és az adatok hova legyenek összegyűjtve.

1. Az a [az előfizetés-megoldások listája](#list-installed-monitoring-solutions), kattintson a **Hozzáadás**.
1. Válasszon ki egy megoldást, vagy keressen rá. [A keresési hivatkozáson](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions)keresztül is böngészhet a megoldásokban.
1. Keresse meg a kívánt figyelési megoldást, és olvassa el a leírását.
1. Kattintson a **létrehozás** a telepítési folyamat elindításához.
1. A telepítési folyamat indításakor a rendszer felszólítja, hogy adja meg a Log Analytics munkaterületet, és adja meg a megoldás szükséges konfigurációját.

![A megoldás telepítése](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>A Közösségtől megoldás telepítése
A Közösség tagjai küldhet az Azure gyorsindítási sablonok adatkezelési megoldásokat. Közvetlenül telepítheti ezeket a megoldásokat, vagy letöltheti a későbbi telepítési sablonok.

1. Kövesse az ismertetett folyamatot [Log Analytics-munkaterületet és Automation-fiók](#log-analytics-workspace-and-automation-account) összekapcsolása egy munkaterületet és fiókot.
2. Lépjen a [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/). 
3. Keressen olyan megoldás, amely érdekli.
4. A részletek megtekintéséhez az eredmények közül válassza ki a megoldást.
5. Kattintson a **üzembe helyezés az Azure** gombra.
6. Kéri, hogy az erőforráscsoportot és helyet értékek mellett például információkat biztosítanak a paraméterek a megoldásban.
7. Kattintson a **beszerzési** a megoldás telepítéséhez.


## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics-munkaterületet és Automation-fiók
Minden figyelési megoldáshoz [log Analytics munkaterület](../platform/manage-access.md) szükséges a megoldás által gyűjtött adatok tárolásához, valamint a naplóbeli keresések és nézetek üzemeltetéséhez. Egyes megoldások is megkövetelheti egy [Automation-fiók](../../automation/automation-security-overview.md#automation-account-overview) a runbookok és kapcsolódó erőforrásokat tartalmaznak. A munkaterületet és fiókot az alábbi követelményeknek kell megfelelnie.

* Minden egyes telepítéséhez, a megoldás csak egy Log Analytics-munkaterület és egy Automation-fiókot használhat. A megoldást külön-külön is telepítheti, több munkaterülettel.
* Ha a megoldás egy Automation-fiókot igényel, majd a Log Analytics-munkaterületet és Automation-fiók össze kell kapcsolni egymással. Log Analytics-munkaterületet egy Automation-fiók csak lehetnek összekapcsolva, és a egy Automation-fiókot csak egy Log Analytics munkaterülettel lehetnek összekapcsolva.
* Össze kell kapcsolni, a Log Analytics-munkaterületet és Automation-fióknak ugyanabban az erőforráscsoportban és régióban kell lennie. A kivétel az USA keleti régiójában egy munkaterületet és Automation-fiókot az USA 2. keleti régiója.

### <a name="create-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Hozzon létre egy Log Analytics-munkaterületet és Automation-fiók közötti kapcsolat
Hogyan adhatja meg a Log Analytics-munkaterületet és Automation-fiók attól függ, hogy a megoldás a telepítési módszer.

* Amikor telepít egy megoldást az Azure piactéren, a rendszer kéri egy munkaterületet és Automation-fiók. A hivatkozás között jön létre, ha már nincs csatolva.
* Megoldások az Azure piactéren kívül hozzá kell rendelnie a Log Analytics-munkaterületet és Automation-fiók a megoldás telepítése előtt. Jelölje ki a megoldással az Azure piactéren, és a Log Analytics-munkaterületet és Automation-fiók kiválasztásával teheti. Nem kell ténylegesen telepíteni a megoldás, mert a kapcsolat létrejött, amint a Log Analytics-munkaterületet és Automation-fiók ki van jelölve. Ha a kapcsolat létrejött, majd használhatja a Log Analytics-munkaterületet és Automation-fiók bármilyen megoldáshoz rendelkezésre állnak.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>A Log Analytics-munkaterületet és Automation-fiók közötti kapcsolat ellenőrzése
Log Analytics-munkaterületet és Automation-fiók az alábbi eljárást követve közötti kapcsolat ellenőrzéséhez.

1. Válassza ki az Automation-fiókot az Azure Portalon.
1. Görgessen a **kapcsolódó erőforrások** a menü részét.
1. Ha a **munkaterület** beállítás engedélyezve van, akkor ezt a fiókot egy Log Analytics-munkaterülethez van csatolva. Kattintson a **munkaterület** a munkaterület részleteinek megtekintéséhez.

## <a name="remove-a-monitoring-solution"></a>Figyelési megoldás eltávolítása
Keresse meg a telepített megoldás eltávolításához a [telepített megoldások listája](#list-installed-monitoring-solutions). Kattintson az összefoglalás lapon megnyitásához, és kattintson a megoldás nevére a **törlése**.


## <a name="next-steps"></a>További lépések
* Szerezze be a [Microsoft figyelési megoldásainak listáját](solutions-inventory.md).
* Megtudhatja, hogyan [hozhat létre lekérdezéseket](../log-query/log-query-overview.md) a figyelési megoldások által gyűjtött adatok elemzéséhez.

