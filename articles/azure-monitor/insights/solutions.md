---
title: Felügyeleti megoldások az Azure-ban |} A Microsoft Docs
description: Felügyeleti megoldások az Azure-ban problémakörrel kapcsolatos egy adott területre metrikákat logikai, megjelenítési és adatgyűjtési szabályok gyűjteményei.  Ez a cikk információt nyújt a telepítését, és felügyeleti megoldásokkal.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: bwren
ms.openlocfilehash: a498c9251c305b4845cc911fc9a21fbdafda72fe
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52832258"
---
# <a name="management-solutions-in-azure"></a>Felügyeleti megoldások az Azure-ban
Felügyeleti megoldások használhatja a szolgáltatásokat nyújt további betekintést a művelet egy adott alkalmazás vagy szolgáltatás Azure-ban. Ez a cikk az Azure és a részletek megoldások rövid áttekintést nyújt a használatáról, és telepíteni kell őket.

Felügyeleti megoldások általában információk gyűjtése a Log Analytics, és adja meg a naplókereséseken és nézeteken összegyűjtött adatok elemzéséhez. Azure Automation és az alkalmazáshoz vagy szolgáltatáshoz kapcsolódó műveleteket, például más szolgáltatások is előfordulhat, hogy kihasználja.

Felügyeleti megoldások is hozzáadhat az alkalmazások és szolgáltatások Azure-előfizetéshez. Általában érhetők el, nem tudta meghívni a használati díjak költség azonban gyűjt adatokat. A Microsoft megoldásai mellett partnerei és ügyfelei is [felügyeleti megoldásokat hozhat létre](solutions-creating.md) saját környezetben használja, vagy szeretné elérhetővé tenni az ügyfelek számára, a közösségi.

## <a name="using-management-solutions"></a>Felügyeleti megoldások használatával
A **áttekintése** minden Log Analytics-munkaterület megjelenít egy csempét a munkaterületen telepített egyes megoldások oldala. Kattintson a részletesebb elemzés tartalmazó nézet megnyitásához a megoldás csempéje az összegyűjtött adatokat.

![Áttekintés](media/solutions/overview.png)

Felügyeleti megoldások Azure-erőforrások többféle adattípust tartalmazhat, és a egy megoldással, mint bármely más erőforrást tartalmaz minden olyan erőforrások megtekintéséhez. Például bármely Naplókeresés, a megoldásban a mellékelt **mentett keresések** a munkaterületen. Ezek a keresések is használhat, a Log Analytics alkalmi elemzések végrehajtása során.

## <a name="list-installed-management-solutions"></a>Telepített felügyeleti megoldások listája 
Az alábbi eljárás segítségével telepítve az előfizetésében felügyeleti megoldások listája.

1. Jelentkezzen be az Azure Portalra.
2. A bal oldali panelen válassza ki a **minden szolgáltatás**.
3. Vagy görgessen le a **megoldások** vagy típus *megoldások* be a **szűrő** párbeszédpanel.
4. Minden olyan a munkaterületen telepített megoldások jelennek meg. A megoldás nevére van telepítve a Log Analytics-munkaterület neve követi.
1. A képernyő tetején a legördülő mezőkben használatával vagy erőforráscsoportonként szűrés.


![Az összes megoldáshoz listázása](media/solutions/list-solutions-all.png)

Kattintson az összefoglalás lapon nyissa meg a megoldás nevére. Ezen a lapon a megoldásban szereplő bármely Log Analytics-nézetek megjelenítése, és a megoldás különböző lehetőségeket kínál a saját maga és a munkaterületen. A megoldás összefoglaló lapjának megtekintéséhez a fenti eljárásokat lista megoldások egyikének használatával, és kattintson a megoldás nevére.

![Megoldás tulajdonságai](media/solutions/solution-properties.png)



## <a name="install-a-management-solution"></a>Egy megoldás telepítése
A Microsoft és partnerei felügyeleti megoldások érhetők el a [Azure Marketplace-en](https://azuremarketplace.microsoft.com). Keresés az elérhető megoldások, és telepítse őket a következő eljárás használatával.

1. Az a [az előfizetés-megoldások listája](#list-installed-management-solutions), kattintson a **Hozzáadás**. 
1. Jobb oldalán **felügyeleti megoldások**, kattintson a **további**. 
1. Keresse meg a felügyeleti megoldást szeretne, majd olvassa el a leírását.
1. Kattintson a **létrehozás** a telepítési folyamat elindításához.
1. Amikor a telepítési folyamat elindul, adja meg a szükséges konfigurációs működésmódja egyes megoldások kéri. Ezek mindegyike szükséges válassza ki a Log Analytics-munkaterületet, a megoldás telepítve lesz, és az adatok hova legyenek összegyűjtve. 

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
Az összes felügyeleti megoldások szükséges egy [Log Analytics-munkaterület](../../log-analytics/log-analytics-manage-access.md) a megoldás által összegyűjtött adatok tárolására, és a naplókereséseken és nézeteken üzemeltetéséhez. Egyes megoldások is megkövetelheti egy [Automation-fiók](../../automation/automation-security-overview.md#automation-account-overview) a runbookok és kapcsolódó erőforrásokat tartalmaznak. A munkaterületet és fiókot az alábbi követelményeknek kell megfelelnie.

* Minden egyes telepítéséhez, a megoldás csak egy Log Analytics-munkaterület és egy Automation-fiókot használhat. A megoldást külön-külön is telepítheti, több munkaterülettel.
* Ha a megoldás egy Automation-fiókot igényel, majd a Log Analytics-munkaterületet és Automation-fiók össze kell kapcsolni egymással. Log Analytics-munkaterületet egy Automation-fiók csak lehetnek összekapcsolva, és a egy Automation-fiókot csak egy Log Analytics munkaterülettel lehetnek összekapcsolva.
* Össze kell kapcsolni, a Log Analytics-munkaterületet és Automation-fióknak ugyanabban az erőforráscsoportban és régióban kell lennie. A kivétel az USA keleti régiójában egy munkaterületet és Automation-fiókot az USA 2. keleti régiója.

### <a name="creating-a-link-between-a-log-analytics-workspace-and-automation-account"></a>A Log Analytics-munkaterületet és Automation-fiók közötti kapcsolat létrehozása
Hogyan adhatja meg a Log Analytics-munkaterületet és Automation-fiók attól függ, hogy a megoldás a telepítési módszer.

* Amikor telepít egy megoldást az Azure piactéren, a rendszer kéri egy munkaterületet és Automation-fiók. A hivatkozás között jön létre, ha már nincs csatolva.
* Megoldások az Azure piactéren kívül hozzá kell rendelnie a Log Analytics-munkaterületet és Automation-fiók a megoldás telepítése előtt. Jelölje ki a megoldással az Azure piactéren, és a Log Analytics-munkaterületet és Automation-fiók kiválasztásával teheti. Nem kell ténylegesen telepíteni a megoldás, mert a kapcsolat létrejött, amint a Log Analytics-munkaterületet és Automation-fiók ki van jelölve. Ha a kapcsolat létrejött, majd használhatja a Log Analytics-munkaterületet és Automation-fiók bármilyen megoldáshoz rendelkezésre állnak.

### <a name="verifying-the-link-between-a-log-analytics-workspace-and-automation-account"></a>A Log Analytics-munkaterületet és Automation-fiók közötti kapcsolat ellenőrzése
Log Analytics-munkaterületet és Automation-fiók az alábbi eljárást követve közötti kapcsolat ellenőrzéséhez.

1. Válassza ki az Automation-fiókot az Azure Portalon.
1. Görgessen a **kapcsolódó erőforrások** a menü részét.
1. Ha a **munkaterület** beállítás engedélyezve van, akkor ezt a fiókot egy Log Analytics-munkaterülethez van csatolva. Kattintson a **munkaterület** a munkaterület részleteinek megtekintéséhez.

## <a name="remove-a-management-solution"></a>Egy megoldás eltávolítása
Keresse meg a telepített megoldás eltávolításához a [telepített megoldások listája](#list-installed-management-solutions). Kattintson az összefoglalás lapon megnyitásához, és kattintson a megoldás nevére a **törlése**.




## <a name="next-steps"></a>További lépések
* Get- [listán, megoldások a Microsoft](solutions-inventory.md).
* Ismerje meg, hogyan [lekérdezések létrehozása](../../azure-monitor/log-query/log-query-overview.md) felügyeleti megoldások által gyűjtött adatok elemzéséhez.

