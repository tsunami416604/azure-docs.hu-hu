---
title: "Azure-beli virtuális gépek változásainak hibaelhárítása | Microsoft Docs"
description: "A Change Tracking megoldás használatával háríthatja el az Azure-beli virtuális gépek változásaival kapcsolatos hibákat."
services: automation
keywords: "változás, követés, automatizálás"
author: jennyhunter-msft
ms.author: jehunte
ms.date: 12/14/2017
ms.topic: tutorial
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 0aefa175d676bd7e98841d3a1e9ff5a8c90b7deb
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2018
---
# <a name="troubleshoot-changes-in-your-environment"></a>A környezet változásainak hibaelhárítása

Ez az oktatóanyag bemutatja, hogyan háríthatja el az Azure-beli virtuális gépek változásaival kapcsolatos hibákat. A változáskövetési megoldás engedélyezésével követheti a számítógépeken lévő szoftverek, fájlok, Linux-démonok, Windows-szolgáltatások és Windows-beállításkulcsok változásait.
A konfigurációk változásainak azonosításával megtalálhatja a környezetben felmerülő működési problémákat.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Virtuális gép felvétele a Change Tracking és az Inventory megoldásba
> * Leállított szolgáltatások változásnaplóinak keresése
> * Változáskövetés konfigurálása
> * Tevékenységnapló csatlakozásának engedélyezése
> * Események aktiválása
> * Változások megtekintése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](automation-offering-get-started.md) a megfigyelő és műveleti runbookok, valamint a figyelőfeladat tárolásához.
* A szolgáltatásba felvenni kívánt [virtuális gép](../virtual-machines/windows/quick-create-portal.md).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.

## <a name="enable-change-tracking-and-inventory"></a>A Change Tracking és az Inventory engedélyezése

Az oktatóanyag első lépéseként engedélyeznie kell a Change Tracking és az Inventory megoldást a virtuális gépen. Ha előzőleg már engedélyezett egy másik automatizálási megoldást valamely virtuális gépen, ez a lépés kihagyható.

1. A bal oldali menüben kattintson a **Virtuális gépek** elemre, majd válasszon ki egy virtuális gépet a listából.
1. A bal oldali menüben, a **Műveletek** szakaszban kattintson az **Inventory** elemre. Ekkor megjelenik a **Change Tracking és Inventory** lap.

A rendszer ellenőrzi, hogy a Change Tracking és az Inventory engedélyezve van-e a virtuális gépen.
A rendszer eközben azt is ellenőrzi, hogy létezik-e Log Analytics-munkaterület és egy csatlakoztatott Automation-fiók, valamint hogy a megoldás már jelen van-e a munkaterületen.

A [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)-munkaterület az Inventory, valamint a hasonló funkciók és szolgáltatások által létrehozott adatok gyűjtésére szolgál.
A munkaterület egyetlen központi helyet biztosít a több forrásból származó adatok áttekintéséhez és elemzéséhez.

Az ellenőrzési folyamat arra is kiterjed, hogy a virtuális gépen működik-e a Microsoft Monitoring Agent (MMA) és egy hibrid feldolgozó.
Ez az ügynök kommunikál a virtuális géppel, továbbá begyűjti a telepített szoftverekkel kapcsolatos információikat.
Az ellenőrzési folyamat arra is kiterjed, hogy a virtuális gépen működik-e a Microsoft Monitoring Agent (MMA) és az Automation hibrid runbook-feldolgozó.

Ha az előfeltételek nem teljesülnek, egy szalagcím jelenik meg, amelyen engedélyezheti a megoldást.

![A Change Tracking és az Inventory felvételének konfigurációs szalagcíme](./media/automation-tutorial-troubleshoot-changes/enableinventory.png)

A megoldás engedélyezéséhez kattintson a szalagcímre.
Ha az ellenőrzést követően az alábbi előfeltételek bármelyike hiányzik, a rendszer automatikusan hozzáadja azt:

* [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)-munkaterület
* [Automatizálás](./automation-offering-get-started.md)
* Engedélyezett [hibrid runbook-feldolgozó](./automation-hybrid-runbook-worker.md) a virtuális gépen

Megnyílik a **Change Tracking és Inventory** képernyő. Konfigurálja a használni kívánt helyet, Log Analytics-munkaterületet és Automation-fiókot, majd kattintson az **Engedélyezés** gombra. Ha a mezők szürkén jelennek meg, az azt jelenti, hogy egy másik automatizálási megoldás már engedélyezve van a virtuális gépen, ezért az ahhoz tartozó munkaterületet és Automation-fiókot kell használnia.

![Változáskövetési megoldás engedélyezési ablaka](./media/automation-tutorial-troubleshoot-changes/installed-software-enable.png)

A megoldás engedélyezése akár 15 percet is igénybe vehet. Ez idő alatt ne zárja be a böngészőablakot.
A megoldás engedélyezését követően a virtuális gépen telepített szoftverekkel és változásokkal kapcsolatos adatok elkezdenek beérkezni a Log Analytics szolgáltatásba.
Az adatok legalább 30 perc és legfeljebb 6 óra múlva állnak készen az elemzésre.


## <a name="using-change-tracking-in-log-analytics"></a>A Change Tracking megoldás használata a Log Analyticsben

A Change Tracking által létrehozott naplóadatok a Log Analyticsbe lesznek továbbítva. Ha lekérdezések futtatásával szeretne keresni a naplókban, kattintson a **Log Analytics** elemre a **Change Tracking** ablak felső részén.
A rendszer a változáskövetési adatokat a **ConfigurationChange** típus alatt tárolja. A következő Log Analytics-mintalekérdezés az összes leállított Windows-szolgáltatást adja vissza.

```
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

A naplófájlok a Log Analytics szolgáltatásban való futtatásával és keresésével kapcsolatos további információkért tekintse meg az [Azure Log Analyticsszel](https://docs.loganalytics.io/index) kapcsolatos cikket.

## <a name="configure-change-tracking"></a>A Change Tracking konfigurálása

A Change Tracking segítségével nyomon követheti a virtuális gép konfigurációjának változásait. Az alábbi lépések bemutatják, hogyan konfigurálhatja a beállításkulcsok és a fájlok követését.
 
A gyűjteni és követni kívánt fájlok és beállításkulcsok kiválasztásához kattintson a **Beállítások szerkesztése** gombra a **Change Tracking** lap tetején.

> [!NOTE]
> Az Inventory és a Change Tracking ugyanazokat az adatgyűjtési beállításokat használja, és a beállítások a munkaterület szintjén konfigurálhatók.

A **Munkaterület konfigurálása** ablakban adja hozzá a követni kívánt Windows-beállításkulcsokat és Windows- vagy Linux-fájlokat a következő három szakaszban foglaltak szerint.

### <a name="add-a-windows-registry-key"></a>Windows-beállításkulcs hozzáadása

1. A **Windows-beállításjegyzék** lapon kattintson a **Hozzáadás** gombra.
    Megnyílik a **Windows-beállításjegyzék felvétele a Change Tracking megoldásba** ablak.

   ![Change Tracking – beállításjegyzék hozzáadása](./media/automation-vm-change-tracking/change-add-registry.png)

2. Az **Engedélyezve** elem alatt válassza az **Igaz** lehetőséget.
3. Az **Elem neve** mezőben adjon meg egy rövid nevet.
4. (Nem kötelező) A **Csoport** mezőben adjon meg egy csoportnevet.
5. A **Windows-beállításkulcs** mezőbe írja be a követni kívánt beállításkulcs nevét.
6. Kattintson a **Mentés** gombra.

### <a name="add-a-windows-file"></a>Windows-fájl hozzáadása

1. A **Windows-fájlok** lapon kattintson a **Hozzáadás** gombra. Megnyílik a **Windows-fájl felvétele a Change Tracking megoldásba** ablak.

   ![Change Tracking – Windows-fájl hozzáadása](./media/automation-vm-change-tracking/change-add-win-file.png)

2. Az **Engedélyezve** elem alatt válassza az **Igaz** lehetőséget.
3. Az **Elem neve** mezőben adjon meg egy rövid nevet.
4. (Nem kötelező) A **Csoport** mezőben adjon meg egy csoportnevet.
5. Az **Elérési út megadása** mezőbe írja be a követni kívánt fájl teljes elérési útját és fájlnevét.
6. Kattintson a **Mentés** gombra.

### <a name="add-a-linux-file"></a>Linux-fájl hozzáadása

1. A **Linux-fájlok** lapon kattintson a **Hozzáadás** gombra. Megnyílik a **Linux-fájl felvétele a Change Tracking megoldásba** ablak.

   ![Change Tracking – Linux-fájl hozzáadása](./media/automation-vm-change-tracking/change-add-linux-file.png)

2. Az **Engedélyezve** elem alatt válassza az **Igaz** lehetőséget.
3. Az **Elem neve** mezőben adjon meg egy rövid nevet.
4. (Nem kötelező) A **Csoport** mezőben adjon meg egy csoportnevet.
5. Az **Elérési út megadása** mezőbe írja be a követni kívánt fájl teljes elérési útját és fájlnevét.
6. Az **Elérési út típusa** mezőben válassza a **Fájl** vagy **Könyvtár** lehetőséget.
7. A megadott elérési út és az alá tartozó összes fájl és elérési út követéséhez válassza a **Be** lehetőséget a **Rekurzió** területen. Ha csak a megadott elérési utat vagy fájlt szeretné követni, válassza a **Ki** lehetőséget.
8. Az olyan fájlok követéséhez, amelyeknek az eléréséhez a `sudo` parancs szükséges, válassza a **Be** lehetőséget a **Sudo használata** területen. Máskülönben válassza a **Ki** lehetőséget.
9. Kattintson a **Mentés** gombra.

## <a name="enable-activity-log-connection"></a>Tevékenységnapló csatlakozásának engedélyezése

A virtuális gép **Change Tracking** lapján válassza a **Tevékenységnapló-kapcsolat kezelése** lehetőséget. Ez a feladat megnyitja az **Azure-tevékenységnapló** lapot. A **Csatlakozás** gombra kattintva csatlakoztathatja a Change Tracking megoldást a virtuális gép Azure-tevékenységnaplójához.

A beállítás engedélyezése után lépjen a virtuális gép **Áttekintés** lapjára, majd kattintson a **Leállítás** gombra a virtuális gép leállításához. A megjelenő üzenetben kattintson az **Igen** gombra a virtuális gép leállításához. A felszabadított virtuális gépeket az **Indítás** gombra kattintva indíthatja újra.

A virtuális gép leállítása és elindítása egy-egy eseményt rögzít a tevékenységnaplóban. Lépjen vissza a **Change Tracking** oldalra. Az oldal alján válassza az **Események** lapot. Az események idővel megjelennek a diagramban és a táblázatban. Ahogy az előző lépésben is, az egyes események kijelölésével megjelenítheti a hozzájuk tartozó részletes adatokat.

![Változások részleteinek megtekintése a portálon](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

## <a name="view-changes"></a>Változások megtekintése

A Change Tracking és az Inventory megoldás engedélyezését követően az eredmények a **Change Tracking** lapon tekinthetők meg.

A virtuális gépen kattintson a **Change Tracking** elemre a **MŰVELETEK** területen.

![Képernyőkép a azokat a változásokat a virtuális géphez](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

A diagram az eltelt idő alatt bekövetkezett változásokat mutatja.
A Tevékenységnapló-kapcsolat hozzáadása után a felül látható vonaldiagramon az Azure tevékenységnapló eseményei jelennek meg.
A sávdiagramok minden egyes sora más-más követhető változástípust jelenít meg.
Ezek a típusok lehetnek Linux-démonok, fájlok, Windows-beállításkulcsok, szoftverek és Windows-szolgáltatások.
A Változások lapon jelennek meg a vizualizációban látható változások részletei a változás bekövetkezte szerinti fordított időrendben (vagyis a legutóbbi van legelöl).
Az **Események** lapon lévő táblában a csatlakoztatott Tevékenységnapló eseményei láthatók a vonatkozó részletekkel. A legutóbbi esemény van legelöl.

Az eredményekből látható, hogy több, a szolgáltatásokra és a szoftverekre vonatkozó változás is bekövetkezett a rendszerben. Az oldal tetején lévő szűrőkkel a **Változás típusa** vagy időtartomány szerint szűrheti az eredményeket.

Ha kijelöli valamelyik **WindowsServices** változást, megnyílik a **Változás részletei** ablak. A Változás részletei ablak a változással kapcsolatos részleteket, valamint a változást megelőző és az azt követő értékeket jeleníti meg. Jelen esetben a Szoftvervédelmi szolgáltatást leállt.

![Változások részleteinek megtekintése a portálon](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Virtuális gép felvétele a Change Tracking és az Inventory megoldásba
> * Leállított szolgáltatások változásnaplóinak keresése
> * Változáskövetés konfigurálása
> * Tevékenységnapló csatlakozásának engedélyezése
> * Események aktiválása
> * Változások megtekintése

Ha többet szeretne megtudni a Change Tracking és az Inventory megoldásról, folytassa az áttekintéssel.

> [!div class="nextstepaction"]
> [Változáskezelés és az Inventory megoldás](../log-analytics/log-analytics-change-tracking.md?toc=%2fazure%2fautomation%2ftoc.json)
