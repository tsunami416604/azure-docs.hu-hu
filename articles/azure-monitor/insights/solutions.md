---
title: Figyelési megoldások az Azure Monitorban | Microsoft dokumentumok
description: Az Azure Monitor figyelési megoldásai logikai, vizualizációs és adatgyűjtési szabályok gyűjteményei, amelyek egy adott problémás terület körül elforgatott metrikákat biztosítanak.  Ez a cikk a figyelési megoldások telepítésével és használatával kapcsolatos információkat tartalmaz.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2020
ms.openlocfilehash: c2690ad7cc4dcaa295bfb08b8c0396438ada0807
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437546"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Figyelési megoldások az Azure Monitorban
A figyelési megoldások az Azure-beli szolgáltatásokat használják ki, hogy további betekintést nyújtsanak egy adott alkalmazás vagy szolgáltatás működésébe. Ez a cikk rövid áttekintést nyújt az Azure-beli figyelési megoldásokról, valamint azok használatáról és telepítéséről.

> [!NOTE]
> A figyelési megoldásokat korábban felügyeleti megoldásoknak nevezték.

A figyelési megoldások általában naplóadatokat gyűjtenek, és lekérdezéseket és nézeteket biztosítanak az összegyűjtött adatok elemzéséhez. Más szolgáltatásokat is használhatnak, például az Azure Automationt az alkalmazáshoz vagy szolgáltatáshoz kapcsolódó műveletek végrehajtásához.

Figyelési megoldásokat adhat hozzá az Azure Monitorhoz a használt alkalmazásokhoz és szolgáltatásokhoz. Általában díjmentesen érhetők el, de olyan adatokat gyűjtenek, amelyek használati díjakat hívhatnak meg. A Microsoft által biztosított megoldásokon kívül a partnerek és az ügyfelek felügyeleti [megoldásokat is létrehozhatnak, amelyeket](solutions-creating.md) saját környezetükben használhatnak, vagy a közösségen keresztül az ügyfelek rendelkezésére bocsáthatnak.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="use-monitoring-solutions"></a>Monitorozási megoldások használata
Nyissa **meg** az Áttekintés lapot az Azure Monitorban a munkaterületre telepített minden egyes megoldás csempéjének megjelenítéséhez. 

1. Nyissa meg az [Azure Portalt.](https://ms.portal.azure.com) Keresse meg a Keresést, és válassza **a Monitor**lehetőséget.
1. Az **Elemzések menüben** válassza az **Egyebek**lehetőséget.
1. A képernyő tetején található legördülő listák segítségével módosíthatja a munkaterületet vagy a csempékhez használt időtartományt.
1. Kattintson a csempe a megoldás, hogy nyissa meg a nézetet, amely részletesebb elemzést az összegyűjtött adatokat.

![Áttekintés](media/solutions/overview.png)

A figyelési megoldások többféle Azure-erőforrást tartalmazhatnak, és a megoldáshoz tartozó erőforrásokat ugyanúgy megtekintheti, mint bármely más erőforrást. A megoldásban található naplólekérdezések például a **Megoldáslekérdezések** a [log queries](../log-query/log-query-overview.md) [Lekérdezéskezelőben](../log-query/get-started-portal.md#load-queries) területen találhatók.

## <a name="list-installed-monitoring-solutions"></a>Telepített figyelési megoldások listázása 
Az alábbi eljárással sorolja fel az előfizetésben telepített figyelési megoldásokat.

1. Nyissa meg az [Azure Portalt.](https://ms.portal.azure.com) Keressen és válassza a **Megoldások lehetőséget.**
1. Az összes munkaterületre telepített megoldások listája szerepel. A megoldás nevét annak a munkaterületnek a neve követi, amelybe telepítve van.
1. A képernyő tetején lévő legördülő listák segítségével előfizetés vagy erőforráscsoport szerint szűrheti.


![Az összes megoldás listázása](media/solutions/list-solutions-all.png)

Kattintson a megoldás nevére az összefoglaló oldal megnyitásához. Ez a lap a megoldásban található nézeteket jeleníti meg, és különböző lehetőségeket biztosít magának a megoldásnak és munkaterületének. Tekintse meg a megoldás összefoglaló lapját a fenti eljárások egyikével a megoldások listázásához, majd kattintson a megoldás nevére.

![Megoldás tulajdonságai](media/solutions/solution-properties.png)



## <a name="install-a-monitoring-solution"></a>Felügyeleti megoldás telepítése
A Microsoft és partnerei figyelési megoldásai az [Azure Marketplace-en](https://azuremarketplace.microsoft.com)érhetők el. Az alábbi eljárással kereshet az elérhető megoldások között, és telepítheti azokat. A megoldás telepítésekor ki kell választania egy [Log Analytics-munkaterületet,](../platform/manage-access.md) ahol a megoldás telepítve lesz, és ahol az adatokat gyűjtik.

1. Az [előfizetésmegoldási listából](#list-installed-monitoring-solutions)kattintson a **Hozzáadás gombra.**
1. Keresse meg vagy keressen megoldást. A keresési linkről is böngészhet [megoldásokat.](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions)
1. Keresse meg a kívánt figyelési megoldást, és olvassa el annak leírását.
1. A telepítési folyamat elindításához kattintson a **Létrehozás** gombra.
1. Amikor a telepítési folyamat elindul, a rendszer kéri, hogy adja meg a Log Analytics munkaterületet, és adja meg a megoldás szükséges konfigurációját.

![Megoldás telepítése](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Telepítsen egy megoldást a közösségből
A közösség tagjai felügyeleti megoldásokat küldhetnek az Azure gyorsindítási sablonjaiba. Ezeket a megoldásokat közvetlenül telepítheti, vagy letöltheti őket a későbbi telepítéshez.

1. Kövesse a [Log Analytics-munkaterület és automation-fiókban](#log-analytics-workspace-and-automation-account) leírt folyamatot egy munkaterület és fiók összekapcsolásához.
2. Nyissa meg az [Azure gyorsindítási sablonjait.](https://azure.microsoft.com/documentation/templates/) 
3. Keressen egy olyan megoldást, amely érdekli.
4. Válassza ki a megoldást az eredmények közül a részletek megtekintéséhez.
5. Kattintson az **Azure-ba való üzembe helyezés** gombra.
6. A rendszer kéri, hogy a megoldás bármely paraméterének értékein kívül adja meg az erőforrásokcsoportot és a helyet.
7. A megoldás telepítéséhez kattintson a **Vásárlás** gombra.


## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics munkaterületi és automatizálási fiók
Minden figyelési megoldás hoz egy [Log Analytics munkaterületet](../platform/manage-access.md) a megoldás által gyűjtött adatok tárolásához, valamint a naplókeresések és nézetek üzemeltetéséhez. Egyes megoldások is szükség van egy [Automation-fiók](../../automation/automation-security-overview.md#automation-account-overview) runbookok és a kapcsolódó erőforrások at tartalmaz. A munkaterületnek és a fióknak meg kell felelnie az alábbi követelményeknek.

* A megoldás minden egyes telepítése csak egy Log Analytics-munkaterületet és egy Automation-fiókot használhat. A megoldást külön-külön is telepítheti több munkaterületre.
* Ha egy megoldás automatizálási fiókot igényel, akkor a Log Analytics munkaterületet és az Automation-fiókot egymáshoz kell kapcsolni. A Log Analytics-munkaterület csak egy Automation-fiókhoz kapcsolható, és egy Automation-fiók csak egy Log Analytics-munkaterülethez kapcsolható.
* Az összekapcsoláshoz a Log Analytics munkaterület és automation-fiók ugyanabban az erőforráscsoportban és régióban kell lennie. Kivételt képez az USA keleti régiójában található munkaterület és az USA keleti régiójában 2.

Amikor telepít egy megoldást az Azure Marketplace-en keresztül, a rendszer kéri egy munkaterületi és automation-fiók. A köztük lévő kapcsolat akkor jön létre, ha még nincsenek összekapcsolva.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>A Log Analytics-munkaterület és az Automation-fiók közötti kapcsolat ellenőrzése
A Log Analytics-munkaterület és az Automation-fiók közötti kapcsolatot az alábbi eljárással ellenőrizheti.

1. Válassza ki az Automation-fiókot az Azure Portalon.
1. Görgessen a menü **Kapcsolódó erőforrások** szakaszához.
1. Ha a **Munkaterület** beállítás engedélyezve van, akkor ez a fiók egy Log Analytics-munkaterülethez kapcsolódik. A munkaterület részleteinek megtekintéséhez kattintson a **Munkaterületre.**

## <a name="remove-a-monitoring-solution"></a>Figyelési megoldás eltávolítása
Telepített megoldás eltávolításához keresse meg azt [a telepített megoldások listájában.](#list-installed-monitoring-solutions) Kattintson a megoldás nevére az összefoglaló oldal megnyitásához, majd kattintson a **Törlés gombra.**


## <a name="next-steps"></a>További lépések
* A [Microsoft figyelési megoldásainak listája.](solutions-inventory.md)
* Ismerje meg, hogyan [hozhat létre lekérdezéseket](../log-query/log-query-overview.md) a figyelési megoldások által gyűjtött adatok elemzéséhez.

