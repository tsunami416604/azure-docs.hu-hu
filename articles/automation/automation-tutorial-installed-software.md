---
title: A gépekre telepített szoftverek felderítése az Azure Automation használatával | Microsoft Docs
description: Az Inventory használatával felderítheti, milyen szoftverek vannak telepítve a környezetében működő gépeken.
services: automation
keywords: leltár, automatizálás, változás, követés
ms.date: 04/11/2018
ms.topic: tutorial
ms.subservice: change-inventory-management
ms.custom: mvc
ms.openlocfilehash: b93035fc7e315f8117516771236186f9d942a0aa
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81604668"
---
# <a name="discover-what-software-is-installed-on-your-azure-and-non-azure-machines"></a>Az Azure- és nem Azure-gépeken telepített szoftverek felderítése

Ez az oktatóanyag bemutatja, hogyan derítheti fel a környezetében telepített szoftvereket. Összegyűjtheti a számítógépeken található szoftverek, fájlok, Linux-démonok, Windows-szolgáltatások és Windows-beállításkulcsok listáját, és leltárt készíthet belőlük. A gépek konfigurációjának nyomon követésével megtalálhatja a környezetben felmerülő működési problémákat, és alaposabban megismerheti a gépek állapotát.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * A megoldás engedélyezése
> * Azure-beli virtuális gép előkészítése
> * Nem Azure-beli virtuális gép előkészítése
> * Telepített szoftverek megtekintése
> * Telepített szoftverek keresése az Inventory-naplókban

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Egy [Automation-fiók](automation-offering-get-started.md) , amely a megfigyelő és a műveleti runbookok, valamint a figyelő feladatot fogja tárolni.
* A szolgáltatásba felvenni kívánt [virtuális gép](../virtual-machines/windows/quick-create-portal.md).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.

## <a name="enable-change-tracking-and-inventory"></a>A Change Tracking és az Inventory engedélyezése

Az oktatóanyag első lépéseként engedélyeznie kell a Change Tracking és az Inventory megoldást. Ha korábban engedélyezte a **Change Tracking** megoldást, erre a lépésre nincs szükség.

Navigáljon az Automation-fiókjához, és válassza a **leltár** lehetőséget a **konfiguráció kezelése**területen.

Válassza ki az Log Analytics munkaterületet és Automation-fiókot, majd kattintson az **Engedélyezés** gombra a megoldás engedélyezéséhez. A megoldás engedélyezése akár 15 percet is igénybe vehet.

![Az Inventory felvételének konfigurációs szalagcíme](./media/automation-tutorial-installed-software/enableinventory.png)

A megoldás engedélyezéséhez konfigurálja a használni kívánt helyet, Log Analytics-munkaterületet és az Automation-fiókot, majd kattintson az **Engedélyezés** gombra. Ha a mezők szürkén jelennek meg, az azt jelenti, hogy egy másik automatizálási megoldás már engedélyezve van a virtuális gépen, ezért az ahhoz tartozó munkaterületet és Automation-fiókot kell használnia.

A [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)-munkaterület az Inventory, valamint a hasonló funkciók és szolgáltatások által létrehozott adatok gyűjtésére szolgál.
A munkaterület egyetlen központi helyet biztosít a több forrásból származó adatok áttekintéséhez és elemzéséhez.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

A megoldás engedélyezése akár 15 percet is igénybe vehet. Ez idő alatt ne zárja be a böngészőablakot.
A megoldás engedélyezését követően a virtuális gépen található telepített szoftverekre és változásokra vonatkozó információk Azure Monitor naplókra áramlanak.
Az adatok legalább 30 perc és legfeljebb 6 óra múlva állnak készen az elemzésre.

## <a name="onboard-a-vm"></a>Virtuális gép előkészítése

Az Automation-fiókban navigáljon a **leltár** menüpontra a **konfiguráció kezelése**területen.

Az **+ Azure-beli virtuális gép hozzáadása** elemre kattintva megnyílik a Virtuális gépek lap, amelyen kiválaszthat egy meglévő virtuális gépet a listából. Válassza ki az előkészíteni kívánt virtuális gépet. A megnyíló lapon kattintson az **Engedélyezés** gombra a megoldás engedélyezéséhez a virtuális gépen. A rendszer telepíti a Microsoft Management Agentet a virtuális gépre, és konfigurálja az ügynököt, hogy az a megoldás engedélyezésekor konfigurált Log Analytics-munkaterülettel beszélgessen. Az előkészítés végrehajtása eltarthat pár percig. Ezen a ponton kiválaszthat egy új virtuális gépet a listából, és előkészítheti azt.

## <a name="onboard-a-non-azure-machine"></a>Nem Azure-beli gép előkészítése

A nem Azure-beli gépek hozzáadásához telepítse a Windows rendszerhez készült [log Analytics-ügynököt](../azure-monitor/platform/agent-windows.md) vagy [log Analytics-ügynököt](automation-linux-hrw-install.md)az operációs rendszertől függően. Miután telepítette az ügynököt, navigáljon az Automation-fiókjához, és nyissa meg a **leltárt** a **konfiguráció kezelése**területen. A **Gépek kezelése** gombra kattintva megjelenik a Log Analytics-munkaterületre jelentő gépek listája, amelyeken a megoldás nincs engedélyezve. Válassza a környezetnek megfelelő beállítást.

* **Engedélyezés az összes elérhető gépen** – Ez a beállítás a megoldást az adott pillanatban a Log Analytics-munkaterületre jelentő összes gépen engedélyezi.
* **Engedélyezés az összes elérhető és jövőbeli gépen** – Ez a beállítás a megoldást az adott pillanatban a Log Analytics-munkaterületre jelentő összes gépen, majd később a munkaterülethez hozzáadott összes további gépen engedélyezi.
* **Engedélyezés a kijelölt gépeken** – Ez a beállítás a megoldást csak a kijelölt gépeken engedélyezi.

![Gépek kezelése](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>Telepített szoftverek megtekintése

Miután engedélyezte a Change Tracking és a leltározási megoldást, megtekintheti az eredményeket a leltár lapon.

Az Automation-fiókban válassza a **leltár** lehetőséget a **konfiguráció kezelése**területen.

Az Inventory lapon kattintson a **Szoftver** lapra.

A **Szoftver** lapon egy táblázat listázza a felderített szoftvereket. A szoftverek név és verzió szerint vannak csoportosítva.

Az egyes szoftverrekordok részletes információi megtekinthetők a táblázatban. Ilyen részletek például a szoftver neve, verziója, közzétevője, a legutóbbi frissítés időpontja (a legutóbbi frissítési idő a csoport egyik gépének jelentése alapján), valamint azon gépek száma, amelyekre a szoftver telepítve van.

![Szoftverleltár](./media/automation-tutorial-installed-software/inventory-software.png)

Kattintson egy sorra a szoftverrekord tulajdonságainak, valamint azon gépek neveinek megtekintéséhez, amelyen a szoftver telepítve van.

Ha egy adott szoftverre vagy szoftvercsoportra kíváncsi, közvetlenül a szoftverlista fölött található szövegmezőben is kereshet.
A szűrő segítségével a szoftver neve, verziója vagy közzétevője alapján is kereshet.

A **contoso** keresése például az összes olyan szoftvert adja vissza, amelynek a neve, közzétevője vagy verziója a **contoso**nevet tartalmazza.

## <a name="search-inventory-logs-for-installed-software"></a>Telepített szoftverek keresése az Inventory-naplókban

A leltár az Azure Monitor naplókba küldendő naplófájlokat hozza létre. Ha lekérdezések futtatásával szeretne keresni a naplókban, válassza a **log Analytics** elemet a leltár oldal tetején.

A leltári adat a típus `ConfigurationData`alatt tárolódik.
A következő minta Log Analytics lekérdezés azokat a leltározási eredményeket adja vissza, amelyekben a közzétevő megegyezik a **Microsoft Corporation**szolgáltatással.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

A naplófájlok Azure Monitor-naplókban való futtatásával és keresésével kapcsolatos további tudnivalókért tekintse meg [Azure monitor naplókat](../azure-monitor/log-query/log-query-overview.md).

### <a name="single-machine-inventory"></a>Leltár egyetlen gépről

Ha egyetlen gépen szeretné megtekinteni a szoftver leltárát, hozzáférhet az Azure-beli virtuális gép erőforrás-oldaláról, vagy Azure Monitor naplók használatával szűrheti le a megfelelő gépre.
A következő példa Log Analytics lekérdezés a **ContosoVM**nevű gép szoftverének listáját adja vissza.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
| summarize by Publisher, SoftwareName
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan tekintheti meg a szoftverleltárt, és ennek keretében hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A megoldás engedélyezése
> * Azure-beli virtuális gép előkészítése
> * Nem Azure-beli virtuális gép előkészítése
> * Telepített szoftverek megtekintése
> * Telepített szoftverek keresése az Inventory-naplókban

Ha többet szeretne megtudni a Change Tracking és az Inventory megoldásról, folytassa az áttekintéssel.

> [!div class="nextstepaction"]
> [Változáskezelés és az Inventory megoldás](automation-change-tracking.md)
