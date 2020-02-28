---
title: Figyelési megoldások a Azure Monitorban | Microsoft Docs
description: A Azure Monitor figyelési megoldásai olyan logikai, vizualizációs és adatgyűjtési szabályok gyűjteményei, amelyek egy adott problémás terület körüli metrikákat biztosítanak.  Ez a cikk a figyelési megoldások telepítésével és használatával kapcsolatos információkat tartalmaz.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: a04ca3768ade6058c59393591c252bc4347a3663
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77662994"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>A Azure Monitor figyelési megoldásai
A monitorozási megoldások kihasználják az Azure szolgáltatásait, hogy további információkat szolgáltassanak egy adott alkalmazás vagy szolgáltatás működéséről. Ez a cikk rövid áttekintést nyújt az Azure-beli monitorozási megoldásokról, valamint a használatáról és telepítéséről.

> [!NOTE]
> A figyelési megoldásokat korábban felügyeleti megoldásoknak nevezzük.

A figyelési megoldások jellemzően gyűjtik a naplózási adatokat, és lekérdezéseket és nézeteket biztosítanak az összegyűjtött adatok elemzéséhez. Azure Automation és az alkalmazáshoz vagy szolgáltatáshoz kapcsolódó műveleteket, például más szolgáltatások is előfordulhat, hogy kihasználja.

A Azure Monitor a használt alkalmazásokhoz és szolgáltatásokhoz is hozzáadhat figyelési megoldásokat. Ezek általában díjmentesen érhetők el, de a használati díjakat meghívó adatokat gyűjtenek. A Microsoft által biztosított megoldások mellett a partnerek és az ügyfelek is [létrehozhatnak olyan felügyeleti megoldásokat](solutions-creating.md) , amelyeket a saját környezetükben használhatnak, vagy amelyeket a Közösségen belül elérhetővé tehetnek az ügyfeleknek.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="use-monitoring-solutions"></a>Monitorozási megoldások használata
Nyissa meg Azure Monitor **Áttekintés** lapját, és jelenítse meg a munkaterületen telepített összes megoldás csempéjét. 

1. Nyissa meg az [Azure Portalt](https://ms.portal.azure.com). Keresse meg és válassza a **figyelő**elemet.
1. Az **áttekintések** menüben válassza a **továbbiak**lehetőséget.
1. A munkaterületet, vagy az időtartomány, a csempék használt módosításához használja a képernyő tetején a legördülő mezőkben.
1. Kattintson a csempére egy megoldás megnyitásához, amely részletesebb elemzést tartalmaz az összegyűjtött adatokról.

![Áttekintés](media/solutions/overview.png)

A figyelési megoldások több típusú Azure-erőforrást is tartalmazhatnak, és a megoldáshoz tartozó összes erőforrást ugyanúgy megtekintheti, mint bármely más erőforrást. Például a megoldásban szereplő összes naplózási lekérdezés megjelenik a [lekérdezési](../log-query/get-started-portal.md#load-queries) lekérdezésekben a **megoldás-lekérdezések** területen, ha ad hoc elemzést végez a [naplók](../log-query/log-query-overview.md)lekérdezésével.

## <a name="list-installed-monitoring-solutions"></a>Telepített figyelési megoldások listázása 
Az alábbi eljárás segítségével listázhatja az előfizetésében telepített figyelési megoldásokat.

1. Nyissa meg az [Azure Portalt](https://ms.portal.azure.com). Keresse meg és válassza ki a **megoldásokat**.
1. Minden olyan a munkaterületen telepített megoldások jelennek meg. A megoldás nevét a-ben telepített munkaterület neve követi.
1. A képernyő tetején a legördülő mezőkben használatával vagy erőforráscsoportonként szűrés.


![Az összes megoldáshoz listázása](media/solutions/list-solutions-all.png)

Kattintson az összefoglalás lapon nyissa meg a megoldás nevére. Ezen a lapon a megoldásban szereplő bármely nézetek megjelenítése, és a megoldás különböző lehetőségeket kínál a saját maga és a munkaterületen. A megoldás összefoglaló lapjának megtekintéséhez a fenti eljárásokat lista megoldások egyikének használatával, és kattintson a megoldás nevére.

![Megoldás tulajdonságai](media/solutions/solution-properties.png)



## <a name="install-a-monitoring-solution"></a>Figyelési megoldás telepítése
A Microsoft és partnerei által kínált figyelési megoldások az [Azure piactéren](https://azuremarketplace.microsoft.com)érhetők el. Keresés az elérhető megoldások, és telepítse őket a következő eljárás használatával. Megoldás telepítésekor ki kell választania egy [log Analytics munkaterületet](../platform/manage-access.md) , ahol a megoldás telepítve lesz, és az adatok gyűjtése történik.

1. Az [előfizetéshez tartozó megoldások listájában](#list-installed-monitoring-solutions)kattintson a **Hozzáadás**gombra.
1. Válasszon ki egy megoldást, vagy keressen rá. [A keresési hivatkozáson](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions)keresztül is böngészhet a megoldásokban.
1. Keresse meg a kívánt figyelési megoldást, és olvassa el a leírását.
1. A telepítési folyamat elindításához kattintson a **Létrehozás** gombra.
1. A telepítési folyamat indításakor a rendszer felszólítja, hogy adja meg a Log Analytics munkaterületet, és adja meg a megoldás szükséges konfigurációját.

![A megoldás telepítése](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>A Közösségtől megoldás telepítése
A Közösség tagjai küldhet az Azure gyorsindítási sablonok adatkezelési megoldásokat. Közvetlenül telepítheti ezeket a megoldásokat, vagy letöltheti a későbbi telepítési sablonok.

1. A munkaterület és a fiók összekapcsolásához kövesse az [log Analytics munkaterület és az Automation-fiók](#log-analytics-workspace-and-automation-account) című témakörben leírt eljárást.
2. Ugrás az [Azure Gyorsindítás sablonjaira](https://azure.microsoft.com/documentation/templates/). 
3. Keressen olyan megoldás, amely érdekli.
4. A részletek megtekintéséhez az eredmények közül válassza ki a megoldást.
5. Kattintson az **üzembe helyezés az Azure-** ban gombra.
6. Kéri, hogy az erőforráscsoportot és helyet értékek mellett például információkat biztosítanak a paraméterek a megoldásban.
7. A megoldás telepítéséhez kattintson a **vásárlás** gombra.


## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics-munkaterületet és Automation-fiók
Minden figyelési megoldáshoz [log Analytics munkaterület](../platform/manage-access.md) szükséges a megoldás által gyűjtött adatok tárolásához, valamint a naplóbeli keresések és nézetek üzemeltetéséhez. Néhány megoldáshoz szükség van egy [Automation-fiókra](../../automation/automation-security-overview.md#automation-account-overview) is, amely runbookok és kapcsolódó erőforrásokat is tartalmaz. A munkaterületet és fiókot az alábbi követelményeknek kell megfelelnie.

* Minden egyes telepítéséhez, a megoldás csak egy Log Analytics-munkaterület és egy Automation-fiókot használhat. A megoldást külön-külön is telepítheti, több munkaterülettel.
* Ha a megoldás egy Automation-fiókot igényel, majd a Log Analytics-munkaterületet és Automation-fiók össze kell kapcsolni egymással. Log Analytics-munkaterületet egy Automation-fiók csak lehetnek összekapcsolva, és a egy Automation-fiókot csak egy Log Analytics munkaterülettel lehetnek összekapcsolva.
* Össze kell kapcsolni, a Log Analytics-munkaterületet és Automation-fióknak ugyanabban az erőforráscsoportban és régióban kell lennie. A kivétel az USA keleti régiójában egy munkaterületet és Automation-fiókot az USA 2. keleti régiója.

### <a name="create-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Hozzon létre egy Log Analytics-munkaterületet és Automation-fiók közötti kapcsolat
Hogyan adhatja meg a Log Analytics-munkaterületet és Automation-fiók attól függ, hogy a megoldás a telepítési módszer.

* Ha az Azure Marketplace-en keresztül telepít megoldást, a rendszer a munkaterület és az Automation-fiók megadását kéri. A hivatkozás között jön létre, ha már nincs csatolva.
* Megoldások az Azure piactéren kívül hozzá kell rendelnie a Log Analytics-munkaterületet és Automation-fiók a megoldás telepítése előtt. Jelölje ki a megoldással az Azure piactéren, és a Log Analytics-munkaterületet és Automation-fiók kiválasztásával teheti. Nem kell ténylegesen telepíteni a megoldás, mert a kapcsolat létrejött, amint a Log Analytics-munkaterületet és Automation-fiók ki van jelölve. Ha a kapcsolat létrejött, majd használhatja a Log Analytics-munkaterületet és Automation-fiók bármilyen megoldáshoz rendelkezésre állnak.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>A Log Analytics-munkaterületet és Automation-fiók közötti kapcsolat ellenőrzése
Log Analytics-munkaterületet és Automation-fiók az alábbi eljárást követve közötti kapcsolat ellenőrzéséhez.

1. Válassza ki az Automation-fiókot az Azure Portalon.
1. Görgessen a menü **kapcsolódó erőforrások** szakaszához.
1. Ha a **munkaterület** -beállítás engedélyezve van, akkor ez a fiók egy log Analytics munkaterülethez van csatolva. A munkaterület részleteinek megtekintéséhez kattintson a **munkaterület** elemre.

## <a name="remove-a-monitoring-solution"></a>Figyelési megoldás eltávolítása
Egy telepített megoldás eltávolításához keresse meg a [telepített megoldások listájában](#list-installed-monitoring-solutions). Kattintson a megoldás nevére az összefoglalás oldal megnyitásához, majd kattintson a **Törlés**gombra.


## <a name="next-steps"></a>Következő lépések
* Szerezze be a [Microsoft figyelési megoldásainak listáját](solutions-inventory.md).
* Megtudhatja, hogyan [hozhat létre lekérdezéseket](../log-query/log-query-overview.md) a figyelési megoldások által gyűjtött adatok elemzéséhez.

