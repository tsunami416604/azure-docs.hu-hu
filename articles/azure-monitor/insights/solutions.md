---
title: Figyelési megoldások a Azure Monitorban | Microsoft Docs
description: A Azure Monitor figyelési megoldásai olyan logikai, vizualizációs és adatgyűjtési szabályok gyűjteményei, amelyek egy adott problémás terület körüli metrikákat biztosítanak.  Ez a cikk a figyelési megoldások telepítésével és használatával kapcsolatos információkat tartalmaz.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: article
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: 4006a3c66da9cb98db16b72da0b5fb7059affe22
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900664"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>A Azure Monitor figyelési megoldásai
A monitorozási megoldások kihasználják az Azure szolgáltatásait, hogy további információkat szolgáltassanak egy adott alkalmazás vagy szolgáltatás működéséről. Ez a cikk rövid áttekintést nyújt az Azure-beli monitorozási megoldásokról, valamint a használatáról és telepítéséről.

> [!NOTE]
> A figyelési megoldásokat korábban felügyeleti megoldásoknak nevezzük.

A figyelési megoldások jellemzően gyűjtik a naplózási adatokat, és lekérdezéseket és nézeteket biztosítanak az összegyűjtött adatok elemzéséhez. Más szolgáltatásokat is használhatnak, például az Azure Automation az alkalmazással vagy szolgáltatással kapcsolatos műveletek elvégzéséhez.

A Azure Monitor a használt alkalmazásokhoz és szolgáltatásokhoz is hozzáadhat figyelési megoldásokat. Ezek általában díjmentesen érhetők el, de a használati díjakat meghívó adatokat gyűjtenek. A Microsoft által biztosított megoldások mellett a partnerek és az ügyfelek is [létrehozhatnak olyan felügyeleti megoldásokat](solutions-creating.md) , amelyeket a saját környezetükben használhatnak, vagy amelyeket a Közösségen belül elérhetővé tehetnek az ügyfeleknek.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="use-monitoring-solutions"></a>Monitorozási megoldások használata
Nyissa meg Azure Monitor **Áttekintés** lapját, és jelenítse meg a munkaterületen telepített összes megoldás csempéjét. 

1. Jelentkezzen be az Azure Portalra.
1. Nyissa meg **az összes szolgáltatást** és keresse meg a **figyelőt**.
1. Az **áttekintések** menüben válassza a **továbbiak**lehetőséget.
1. A képernyő felső részén található legördülő listák használatával módosíthatja a munkaterületet vagy a csempék időtartományát.
1. Kattintson a csempére egy megoldás megnyitásához, amely részletesebb elemzést tartalmaz az összegyűjtött adatokról.

![Áttekintés](media/solutions/overview.png)

A figyelési megoldások több típusú Azure-erőforrást is tartalmazhatnak, és a megoldáshoz tartozó összes erőforrást ugyanúgy megtekintheti, mint bármely más erőforrást. Például a megoldásban szereplő összes naplózási lekérdezés megjelenik a [lekérdezési](../log-query/get-started-portal.md#load-queries) lekérdezésekben a **megoldás-lekérdezések** területen, ha ad hoc elemzést végez a [naplók](../log-query/log-query-overview.md)lekérdezésével.

## <a name="list-installed-monitoring-solutions"></a>Telepített figyelési megoldások listázása 
Az alábbi eljárás segítségével listázhatja az előfizetésében telepített figyelési megoldásokat.

1. Jelentkezzen be az Azure Portalra.
1. Nyissa meg **az összes szolgáltatást** , és keresse meg a **megoldásokat**.
4. Az összes munkaterületre telepített megoldások listája látható. A megoldás nevét a-ben telepített munkaterület neve követi.
1. Az előfizetés vagy az erőforráscsoport alapján történő szűréshez használja a képernyő felső részén található legördülő listákat.


![Az összes megoldás listázása](media/solutions/list-solutions-all.png)

A megoldás nevére kattintva megnyithatja az összefoglalás lapját. Ezen a lapon láthatók a megoldásban szereplő összes nézet, és különböző lehetőségeket biztosít a megoldáshoz és a munkaterületéhez. Tekintse meg a megoldás Összegzés lapját a megoldások listázásához a fenti eljárások egyikével, majd kattintson a megoldás nevére.

![Megoldás tulajdonságai](media/solutions/solution-properties.png)



## <a name="install-a-monitoring-solution"></a>Figyelési megoldás telepítése
A Microsoft és partnerei által kínált figyelési megoldások az [Azure piactéren](https://azuremarketplace.microsoft.com)érhetők el. A következő eljárással kereshet az elérhető megoldásokban, és telepítheti őket. Megoldás telepítésekor ki kell választania egy [log Analytics munkaterületet](../platform/manage-access.md) , ahol a megoldás telepítve lesz, és az adatok gyűjtése történik.

1. Az [előfizetéshez tartozó megoldások listájában](#list-installed-monitoring-solutions)kattintson a **Hozzáadás**gombra.
1. Válasszon ki egy megoldást, vagy keressen rá. [A keresési hivatkozáson](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions)keresztül is böngészhet a megoldásokban.
1. Keresse meg a kívánt figyelési megoldást, és olvassa el a leírását.
1. A telepítési folyamat elindításához kattintson a **Létrehozás** gombra.
1. A telepítési folyamat indításakor a rendszer felszólítja, hogy adja meg a Log Analytics munkaterületet, és adja meg a megoldás szükséges konfigurációját.

![Megoldás telepítése](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Megoldás telepítése a Közösségből
A Közösség tagjai felügyeleti megoldásokat küldhetnek az Azure Gyorsindítás sablonjaiba. Ezeket a megoldásokat közvetlenül is telepítheti, vagy később is letöltheti a sablonokat.

1. A munkaterület és a fiók összekapcsolásához kövesse az [log Analytics munkaterület és az Automation-fiók](#log-analytics-workspace-and-automation-account) című témakörben leírt eljárást.
2. Ugrás az [Azure Gyorsindítás sablonjaira](https://azure.microsoft.com/documentation/templates/). 
3. Keressen egy olyan megoldást, amely érdekli.
4. A részletek megtekintéséhez válassza ki a megoldást az eredmények közül.
5. Kattintson az **üzembe helyezés az Azure-** ban gombra.
6. A rendszer arra kéri, hogy adjon meg olyan információkat, mint például az erőforráscsoport és a hely a megoldás paramétereinek értékei mellett.
7. A megoldás telepítéséhez kattintson a **vásárlás** gombra.


## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics munkaterület és Automation-fiók
Minden figyelési megoldáshoz [log Analytics munkaterület](../platform/manage-access.md) szükséges a megoldás által gyűjtött adatok tárolásához, valamint a naplóbeli keresések és nézetek üzemeltetéséhez. Néhány megoldáshoz szükség van egy [Automation-fiókra](../../automation/automation-security-overview.md#automation-account-overview) is, amely runbookok és kapcsolódó erőforrásokat is tartalmaz. A munkaterületnek és a fióknak meg kell felelnie az alábbi követelményeknek.

* Egy megoldás telepítése csak egyetlen Log Analytics munkaterületet és egy Automation-fiókot használhat. A megoldást külön is telepítheti több munkaterületre.
* Ha egy megoldáshoz Automation-fiókra van szükség, akkor a Log Analytics munkaterület és az Automation-fióknak egymáshoz kell kapcsolódnia. Log Analytics munkaterület csak egy Automation-fiókhoz kapcsolható, és egy Automation-fiók csak egy Log Analytics munkaterülethez kapcsolható.
* A csatoláshoz a Log Analytics munkaterület és az Automation-fióknak ugyanabban az erőforráscsoporthoz és régióban kell lennie. A kivétel az USA keleti régiójában és az USA 2. keleti régiójában található Automation-fiókhoz tartozó munkaterület.

### <a name="create-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Log Analytics munkaterület és Automation-fiók közötti kapcsolat létrehozása
A Log Analytics munkaterület és az Automation-fiók megadása a megoldás telepítési módjától függ.

* Ha az Azure Marketplace-en keresztül telepít megoldást, a rendszer a munkaterület és az Automation-fiók megadását kéri. A közöttük létrejött kapcsolat akkor jön létre, ha még nincsenek összekapcsolva.
* Az Azure Marketplace-en kívüli megoldások esetében a megoldás telepítése előtt össze kell kapcsolni a Log Analytics munkaterületet és az Automation-fiókot. Ezt úgy teheti meg, ha kijelöl egy megoldást az Azure piactéren, és kiválasztja a Log Analytics munkaterület és az Automation-fiók elemet. Nem kell ténylegesen telepítenie a megoldást, mert a hivatkozás akkor jön létre, amikor a Log Analytics munkaterület és az Automation-fiók van kiválasztva. A hivatkozás létrehozása után bármely megoldáshoz használhatja a Log Analytics munkaterület és Automation-fiókot.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Log Analytics munkaterület és az Automation-fiók közötti kapcsolat ellenőrzése
A következő eljárással ellenőrizheti, hogy Log Analytics munkaterület és egy Automation-fiók közötti kapcsolat látható-e.

1. Válassza ki az Automation-fiókot a Azure Portal.
1. Görgessen a menü **kapcsolódó erőforrások** szakaszához.
1. Ha a **munkaterület** -beállítás engedélyezve van, akkor ez a fiók egy log Analytics munkaterülethez van csatolva. A munkaterület részleteinek megtekintéséhez kattintson a **munkaterület** elemre.

## <a name="remove-a-monitoring-solution"></a>Figyelési megoldás eltávolítása
Egy telepített megoldás eltávolításához keresse meg a [telepített megoldások listájában](#list-installed-monitoring-solutions). Kattintson a megoldás nevére az összefoglalás oldal megnyitásához, majd kattintson a **Törlés**gombra.


## <a name="next-steps"></a>Következő lépések
* Szerezze be a [Microsoft figyelési megoldásainak listáját](solutions-inventory.md).
* Megtudhatja, hogyan [hozhat létre lekérdezéseket](../log-query/log-query-overview.md) a figyelési megoldások által gyűjtött adatok elemzéséhez.

