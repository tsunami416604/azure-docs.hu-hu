---
title: Az Azure-ban megoldások |} Microsoft Docs
description: Az Azure-ban megoldások programot, a képi megjelenítés és az adatokat, amelyek egy adott probléma terület körül átalakítani metrikák biztosítanak beszerzési szabályok gyűjteménye.  Ez a cikk bemutatja, telepítéséről és használatáról a felügyeleti megoldás.
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
ms.date: 04/20/2018
ms.author: bwren
ms.openlocfilehash: 0df54d1758693bce5fb5fd74c3be9c4cfd7dccb6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="management-solutions-in-azure"></a>Az Azure-ban megoldások
Megoldások kihasználhatja az Azure arra, hogy egy adott alkalmazás vagy szolgáltatás működésének nyújt további betekintést. Ez a cikk megoldások Azure-ban és részletek rövid áttekintést nyújt a használatával, és telepíteni kell őket.

Megoldások általában Log Analyticshez való információgyűjtés, és adja meg a napló keresések és nézetek összegyűjtött adatok elemzésére. Más szolgáltatásokon, például az alkalmazáshoz vagy szolgáltatáshoz kapcsolódó műveletek elvégzéséhez Azure Automation is előfordulhat, hogy kihasználja.

Alkalmazások és szolgáltatások Azure-előfizetése megoldásokat is hozzáadhat. Általában érhetők el: nem tudta meghívni a használati díjak költség, de a gyűjtés adatokat. A Microsoft által biztosított megoldások mellett partnereinknek és ügyfeleinknek is [létrehozása kezelési megoldásai](../monitoring/monitoring-solutions-creating.md) a saját környezetben használt vagy elérhetővé tenni az ügyfelek a közösségi keresztül.

## <a name="using-management-solutions"></a>Felügyeleti megoldás használata
A **áttekintése** az egyes Naplóelemzési munkaterület megjeleníti minden megoldás a munkaterületen telepítve egy csempe lapon. Kattintson a csempére a részletesebb elemzés tartalmazó nézet megnyitásához a megoldás az összegyűjtött adatokat.

![Áttekintés](media/monitoring-solutions/overview.png)

Megoldások Azure-erőforrások többféle típusú tartalmazhat, és minden olyan erőforrásnál, csakúgy, mint bármely más erőforrás megoldás részét képező tekintheti meg. Például bármely napló keresések tartalmaz a megoldás érhetők el a **mentett keresések** a munkaterületen. Ezek a keresések használhatja a Log Analyticshez alkalmi elemzés végrehajtása során.

## <a name="list-installed-management-solutions"></a>A telepített felügyeleti megoldások felsorolása 
Az alábbi eljárás segítségével a felügyeleti megoldásokra telepítve az előfizetés a listában.

1. Jelentkezzen be az Azure Portalra.
2. A bal oldali panelen válassza ki a **minden szolgáltatás**.
3. Görgessen le a vagy **megoldások** vagy típus *megoldások* be a **szűrő** párbeszédpanel.
4. A munkaterületek telepített megoldások vannak felsorolva. A megoldás neve a telepítve van a Naplóelemzési munkaterület neve követi.
1. A képernyő tetején a legördülő listák segítségével előfizetésenként vagy erőforráscsoportonként szűrés.


![Minden megoldás felsorolása](media/monitoring-solutions/list-solutions-all.png)

Kattintson az összefoglalás lapon nyissa meg az adott megoldás neve. Ezen a lapon megjeleníti, amelyben minden Naplóelemzési nézeteket tartalmaz a megoldás és a megoldás különböző lehetőségek közül önmagában, illetve a munkaterületen. Az összefoglalás lapon megoldás megtekintéséhez a fenti eljárásokat lista megoldások egyikét használva, és kattintson a megoldás neve.

![Megoldás tulajdonságai](media/monitoring-solutions/solution-properties.png)


## <a name="find-management-solutions"></a>Keresse meg a felügyeleti megoldást
Keresse meg és a rendelkezésre álló megoldások telepítse a Microsoft és a partnerek a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace). Hajtsa végre a [keressen *megoldások* ](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions) megoldások szűrését, és kattintson a további információkhoz juthat bármelyik elemre.

![Piactér](media/monitoring-solutions/marketplace.png)

## <a name="install-a-management-solution"></a>A kezelési megoldás telepítése

### <a name="install-a-management-solution-from-the-azure-marketplace"></a>Az Azure piactérről felügyeleti megoldás telepítése
Az alábbi módszerek bármelyikét használhatja keresse meg és a kezelési megoldás a telepítés elindításához.

- Kattintson a **most töltse le innen** a felügyeleti megoldást a [Azure piactér](#find-management-solutions).
- Az a [az előfizetéshez tartozó megoldások listáját](#list-installed-management-solutions), kattintson a **Hozzáadás**. Jobb oldalán **megoldások**, kattintson a **további**. Keresse meg, majd kattintson a felügyeleti megoldás **létrehozása**.
- Válassza ki az Azure-portálon **hozzon létre egy erőforrást** > **figyelés + felügyeleti** > **láthatja az összes**. Jobb oldalán **megoldások**, kattintson a **további**. Keresse meg, majd kattintson a felügyeleti megoldás **létrehozása**.

A telepítési folyamat indításakor kéri, adja meg a szükséges konfiguráció változik az egyes megoldások. Az összes szükséges a Naplóelemzési munkaterület kiválasztása a megoldást futtató és az adatok hova legyenek összegyűjtve. Előfordulhat, hogy szükség [adja meg az Automation-fiók](#log-analytics-workspace-and-automation-account) Ha a megoldás a szükséges.

### <a name="install-a-solution-from-the-community"></a>A Közösség megoldás telepítése
A Közösség tagjai elküldheti a megoldások Azure gyors üzembe helyezési sablonokat. Ezek a megoldások közvetlenül telepítse, vagy letöltheti a fájlokat a későbbi telepítési sablonok.

1. Kövesse az ismertetett folyamatot [Naplóelemzési munkaterületet, és az Automation-fiók](#log-analytics-workspace-and-automation-account) egy munkaterület és a fiók összekapcsolásához.
2. Ugrás a [Azure gyors üzembe helyezési sablonokat](https://azure.microsoft.com/documentation/templates/). 
3. Keressen olyan megoldás, amely kíváncsiak vagyunk.
4. Az eredmények a részletek megtekintéséhez válasszon a megoldás.
5. Kattintson a **az Azure telepítéséhez** gombra.
6. Adja meg az információkat, például az erőforráscsoportot és helyet értékek mellett azokat a paramétereket a megoldásban kéri.
7. Kattintson a **beszerzési** a megoldás telepítéséhez.


## <a name="log-analytics-workspace-and-automation-account"></a>A Naplóelemzési munkaterület és Automation-fiók
Minden felügyeleti megoldás szükséges egy [Naplóelemzési munkaterület](../log-analytics/log-analytics-manage-access.md) a megoldás által gyűjtött adatok tárolására és futtatására, a napló keresések és nézetek. Néhány megoldásokhoz is szükséges, hogy egy [Automation-fiók](../automation/automation-security-overview.md#automation-account-overview) magában foglalja a runbookok és kapcsolódó erőforrások. A munkaterület és a fiók az alábbi követelményeknek kell megfelelniük.

* A megoldás összes példányát csak akkor tudja használni, egy Naplóelemzési munkaterület és egy Automation-fiók. Telepítheti a megoldás külön-külön történő több munkaterületek.
* Ha a megoldás Automation-fiók szükséges, majd a Naplóelemzési munkaterület és Automation-fiók össze kell kapcsolni egy másik. A Naplóelemzési munkaterület csak lehet, hogy egy Automation-fiókhoz csatolva, és egy Automation-fiók csak lehet, hogy egy Naplóelemzési munkaterület kapcsolódik.
* Kell társítani, a Naplóelemzési munkaterület és Automation-fiók ugyanabban az erőforráscsoportban és régióban kell lennie. A kivétel: USA keleti régiójában munkaterületeinek és Automation-fiók az USA keleti régiója 2.

### <a name="creating-a-link-between-a-log-analytics-workspace-and-automation-account"></a>A Naplóelemzési munkaterület és Automation-fiók csatolása
A megoldás a telepítési módszer függ, hogy hogyan határozza meg a Naplóelemzési munkaterület és Automation-fiók.

* Amikor telepít egy megoldást az Azure piactéren keresztül, a munkaterület és Automation-fiók kéri. Egymás közötti kapcsolat jön létre, ha már nincs csatolva.
* Az Azure piactéren kívül megoldásainak kell kapcsolni a Naplóelemzési munkaterület és Automation-fiók a megoldás telepítése előtt. Ehhez jelölje ki a megoldás az Azure piactéren, és a Naplóelemzési munkaterület és Automation-fiók kiválasztása. Nem kell ténylegesen telepíteni a megoldás, mert a kapcsolat létrejött, amint a Naplóelemzési munkaterület Automation-fiók ki van választva. Ha a kapcsolat létrejött, majd használhatja, hogy a Naplóelemzési munkaterület és Automation-fiók minden megoldás.

### <a name="verifying-the-link-between-a-log-analytics-workspace-and-automation-account"></a>A Naplóelemzési munkaterület és az Automation-fiók közötti kapcsolat ellenőrzése
Ellenőrizheti a Naplóelemzési munkaterület és a következő eljárás segítségével Automation-fiók közötti kapcsolatot.

1. Válassza ki az Automation-fiók az Azure portálon.
1. Görgessen a **kapcsolódó erőforrások** a menü részét.
1. Ha a **munkaterület** beállítás engedélyezve van, akkor ez a fiók csatolva van a Naplóelemzési munkaterület. Rákattinthat a **munkaterület** a munkaterület részletes adatainak megtekintéséhez.

## <a name="remove-a-management-solution"></a>Távolítsa el az olyan felügyeleti megoldást
Telepített megoldás eltávolításához keresse meg azt a [telepített megoldások listáját](#list-installed-management-solutions). Kattintson a nevére, hogy nyissa meg az Összegzés lapon, és kattintson a **törlése**.




## <a name="next-steps"></a>További lépések
* Első egy [listája a Microsoft megoldások](monitoring-solutions-inventory.md).
* Megtudhatja, hogyan [létrehozhat olyan lekérdezéseket,](../log-analytics/log-analytics-log-searches.md) megoldások által gyűjtött adatok elemzésére.

