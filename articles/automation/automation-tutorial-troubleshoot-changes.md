---
title: Azure-beli virtuális gépek változásainak hibaelhárítása | Microsoft Docs
description: A Change Tracking megoldás használatával háríthatja el az Azure-beli virtuális gépek változásaival kapcsolatos hibákat.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
keywords: változás, követés, automatizálás
author: jennyhunter-msft
ms.author: jehunte
ms.date: 12/05/2018
ms.topic: tutorial
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 9fa1e3ffd92b3c375837c7b9a4a0e7fd1a80893a
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54433679"
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
> * Riasztások konfigurálása

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](automation-offering-get-started.md) a megfigyelő és műveleti runbookok, valamint a figyelőfeladat tárolásához.
* A szolgáltatásba felvenni kívánt [virtuális gép](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="enable-change-tracking-and-inventory"></a>A Change Tracking és az Inventory engedélyezése

Az oktatóanyag első lépéseként engedélyeznie kell a Change Tracking és az Inventory megoldást a virtuális gépen. Ha előzőleg már engedélyezett egy másik automatizálási megoldást valamely virtuális gépen, ez a lépés kihagyható.

1. A bal oldali menüben kattintson a **Virtuális gépek** elemre, majd válasszon ki egy virtuális gépet a listából.
1. A bal oldali menüben, a **MŰVELETEK** szakaszban kattintson a **Leltározás** elemre. Megnyílik a **változáskövetési** lap.

![Módosítás engedélyezése](./media/automation-tutorial-troubleshoot-changes/enableinventory.png) Megnyílik a **változáskövetési** képernyő. Konfigurálja a használni kívánt helyet, Log Analytics-munkaterületet és Automation-fiókot, majd kattintson az **Engedélyezés** gombra. Ha a mezők szürkén jelennek meg, az azt jelenti, hogy egy másik automatizálási megoldás már engedélyezve van a virtuális gépen, ezért az ahhoz tartozó munkaterületet és Automation-fiókot kell használnia.

A [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)-munkaterület az Inventory, valamint a hasonló funkciók és szolgáltatások által létrehozott adatok gyűjtésére szolgál.
A munkaterület egyetlen központi helyet biztosít a több forrásból származó adatok áttekintéséhez és elemzéséhez.

Az előkészítés során a virtuális gépre kerül egy Microsoft Monitoring Agent (MMA) és egy hibrid feldolgozó.
Ez az ügynök kommunikál a virtuális géppel, továbbá begyűjti a telepített szoftverekkel kapcsolatos információikat.

A megoldás engedélyezése akár 15 percet is igénybe vehet. Ez idő alatt ne zárja be a böngészőablakot.
A megoldás engedélyezését követően a virtuális gépen telepített szoftverekkel és változásokkal kapcsolatos adatok elkezdenek beérkezni a Log Analytics szolgáltatásba.
Az adatok legalább 30 perc és legfeljebb 6 óra múlva állnak készen az elemzésre.

## <a name="using-change-tracking-in-log-analytics"></a>A Change Tracking megoldás használata a Log Analyticsben

A Change Tracking által létrehozott naplóadatok a Log Analyticsbe lesznek továbbítva.
Ha lekérdezések futtatásával szeretne keresni a naplókban, kattintson a **Log Analytics** elemre a **Change Tracking** ablak felső részén.
A rendszer a változáskövetési adatokat a **ConfigurationChange** típus alatt tárolja.
A következő Log Analytics-mintalekérdezés az összes leállított Windows-szolgáltatást adja vissza.

```loganalytics
ConfigurationChange
| where ConfigChangeType == "WindowsServices" and SvcState == "Stopped"
```

A naplófájlok a Log Analytics szolgáltatásban való futtatásával és keresésével kapcsolatos további információkért tekintse meg az [Azure Log Analyticsszel](../azure-monitor/log-query/log-query-overview.md) kapcsolatos cikket.

## <a name="configure-change-tracking"></a>A Change Tracking konfigurálása

A Change Tracking segítségével nyomon követheti a virtuális gép konfigurációjának változásait. Az alábbi lépések bemutatják, hogyan konfigurálhatja a beállításkulcsok és a fájlok követését.

A gyűjteni és követni kívánt fájlok és beállításkulcsok kiválasztásához kattintson a **Beállítások szerkesztése** gombra a **Change Tracking** lap tetején.

> [!NOTE]
> Az Inventory és a Change Tracking ugyanazokat az adatgyűjtési beállításokat használja, és a beállítások a munkaterület szintjén konfigurálhatók.

A **Munkaterület konfigurálása** ablakban adja hozzá a követni kívánt Windows-beállításkulcsokat és Windows- vagy Linux-fájlokat a következő három szakaszban foglaltak szerint.

### <a name="add-a-windows-registry-key"></a>Windows-beállításkulcs hozzáadása

1. A **Windows-beállításjegyzék** lapon kattintson a **Hozzáadás** gombra.
    Megnyílik a **Windows-beállításjegyzék felvétele a Change Tracking megoldásba** ablak.

1. **A Windows beállításjegyzékének hozzáadása a változáskövetéshez** ablakban írja be a követni kívánt kulcs adatait, és kattintson a **Mentés** gombra.

|Tulajdonság  |Leírás  |
|---------|---------|
|Engedélyezve     | Meghatározza, hogy a beállítás aktiválva van-e        |
|Elem neve     | A nyomon követendő fájl felhasználóbarát neve        |
|Csoport     | A fájlok logikai csoportosítására szolgáló csoportnév        |
|Windows-beállításkulcs   | Például ellenőrizze a fájl elérési útja: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User rendszerhéj Folders\Common indítási"      |

### <a name="add-a-windows-file"></a>Windows-fájl hozzáadása

1. A **Windows-fájlok** lapon kattintson a **Hozzáadás** gombra. Megnyílik a **Windows-fájl felvétele a Change Tracking megoldásba** ablak.

1. **A Windows beállításjegyzékének hozzáadása a változáskövetéshez** ablakban írja be a követni kívánt fájl vagy címtár adatait, és kattintson a **Mentés** gombra.

|Tulajdonság  |Leírás  |
|---------|---------|
|Engedélyezve     | Meghatározza, hogy a beállítás aktiválva van-e        |
|Elem neve     | A nyomon követendő fájl felhasználóbarát neve        |
|Csoport     | A fájlok logikai csoportosítására szolgáló csoportnév        |
|Elérési út megadása     | A fájl elérési útja, például: „c:\temp\\\*.txt”<br>Környezeti változók is használhatók, például: „%winDir%\System32\\\*.*”         |
|Rekurzió     | Meghatározza, hogy a rendszer rekurziót használjon-e a követni kívánt elem keresésekor.        |
|Fájltartalom feltöltése minden beállításhoz| Be- vagy kikapcsolja a fájltartalom feltöltését a változáskövetés használata esetén. Elérhető lehetőségek: **Igaz** vagy **hamis**.|

### <a name="add-a-linux-file"></a>Linux-fájl hozzáadása

1. A **Linux-fájlok** lapon kattintson a **Hozzáadás** gombra. Megnyílik a **Linux-fájl felvétele a Change Tracking megoldásba** ablak.

1. A **Linux-fájl hozzáadása a változáskövetéshez** ablakban írja be a követni kívánt fájl vagy címtár adatait, és kattintson a **Mentés** gombra.

|Tulajdonság  |Leírás  |
|---------|---------|
|Engedélyezve     | Meghatározza, hogy a beállítás aktiválva van-e        |
|Elem neve     | A nyomon követendő fájl felhasználóbarát neve        |
|Csoport     | A fájlok logikai csoportosítására szolgáló csoportnév        |
|Elérési út megadása     | A fájl elérési útja, például: „/etc/*.conf”       |
|Elérési út típusa     | Írja be a követni kívánt elem típusát. A lehetséges értékek a Fájl és a Címtár        |
|Rekurzió     | Meghatározza, hogy a rendszer rekurziót használjon-e a követni kívánt elem keresésekor.        |
|Sudo használata     | Ez a beállítás határozza meg, hogy a rendszer sudót használjon-e az elem keresésekor.         |
|Hivatkozások     | Ez a beállítás határozza meg a szimbolikus hivatkozások kezelésének módját, amikor áthaladnak a címtárakon.<br> **Mellőzés** – Mellőzi a szimbolikus hivatkozásokat, és nem szerepelteti a hivatkozott fájlokat/címtárakat<br>**Követés** – Követi a szimbolikus hivatkozásokat a rekurzió során, és szerepelteti a hivatkozott fájlokat/címtárakat<br>**Kezelés** – Követi a szimbolikus hivatkozásokat, és lehetővé teszi a visszaadott tartalom kezelésének módosítását      |
|Fájltartalom feltöltése minden beállításhoz| Be- vagy kikapcsolja a fájltartalom feltöltését a változáskövetés használata esetén. Elérhető lehetőségek: **Igaz** vagy **hamis**.|

   > [!NOTE]
   > A „Kezelés” használata nem ajánlott, mert a fájltartalom lekérése nem támogatott.

## <a name="enable-activity-log-connection"></a>Tevékenységnapló csatlakozásának engedélyezése

A virtuális gép **Change Tracking** lapján válassza a **Tevékenységnapló-kapcsolat kezelése** lehetőséget. Ez a feladat megnyitja az **Azure-tevékenységnapló** lapot. A **Csatlakozás** gombra kattintva csatlakoztathatja a Change Tracking megoldást a virtuális gép Azure-tevékenységnaplójához.

A beállítás engedélyezése után lépjen a virtuális gép **Áttekintés** lapjára, majd kattintson a **Leállítás** gombra a virtuális gép leállításához. A megjelenő üzenetben kattintson az **Igen** gombra a virtuális gép leállításához. A felszabadított virtuális gépeket az **Indítás** gombra kattintva indíthatja újra.

A virtuális gép leállítása és elindítása egy-egy eseményt rögzít a tevékenységnaplóban. Lépjen vissza a **Change Tracking** oldalra. Az oldal alján válassza az **Események** lapot. Az események idővel megjelennek a diagramban és a táblázatban. Ahogy az előző lépésben is, az egyes események kijelölésével megjelenítheti a hozzájuk tartozó részletes adatokat.

![Változások részleteinek megtekintése a portálon](./media/automation-tutorial-troubleshoot-changes/viewevents.png)

## <a name="view-changes"></a>Változások megtekintése

A Change Tracking és az Inventory megoldás engedélyezését követően az eredmények a **Change Tracking** lapon tekinthetők meg.

A virtuális gépen kattintson a **Change Tracking** elemre a **MŰVELETEK** területen.

![A virtuális gép módosításainak listáját bemutató képernyőkép](./media/automation-tutorial-troubleshoot-changes/change-tracking-list.png)

A diagram az eltelt idő alatt bekövetkezett változásokat mutatja.
A Tevékenységnapló-kapcsolat hozzáadása után a felül látható vonaldiagramon az Azure tevékenységnapló eseményei jelennek meg.
A sávdiagramok minden egyes sora más-más követhető változástípust jelenít meg.
Ezek a típusok lehetnek Linux-démonok, fájlok, Windows-beállításkulcsok, szoftverek és Windows-szolgáltatások.
A Változások lapon jelennek meg a vizualizációban látható változások részletei a változás bekövetkezte szerinti fordított időrendben (vagyis a legutóbbi van legelöl).
Az **Események** lapon lévő táblában a csatlakoztatott Tevékenységnapló eseményei láthatók a vonatkozó részletekkel. A legutóbbi esemény van legelöl.

Az eredményekből látható, hogy több, a szolgáltatásokra és a szoftverekre vonatkozó változás is bekövetkezett a rendszerben. Az oldal tetején lévő szűrőkkel a **Változás típusa** vagy időtartomány szerint szűrheti az eredményeket.

Ha kijelöli valamelyik **WindowsServices** változást, megnyílik a **Változás részletei** ablak. A Változás részletei ablak a változással kapcsolatos részleteket, valamint a változást megelőző és az azt követő értékeket jeleníti meg. Jelen esetben a Szoftvervédelmi szolgáltatást leállt.

![Változások részleteinek megtekintése a portálon](./media/automation-tutorial-troubleshoot-changes/change-details.png)

## <a name="configure-alerts"></a>Riasztások konfigurálása

Hasznos, ha követni tudja a végrehajtott módosításokat az Azure Portalon, de még hasznosabbnak bizonyulhat, ha a módosítások (például egy szolgáltatás leállítása) esetén riasztást is küld a rendszer.

Ha riasztást szeretne hozzáadni egy szolgáltatás leállításához, az Azure Portalon lépjen a **Figyelés** felületre. Ezt követően a **Megosztott szolgáltatások** területen válassza a **Riasztások** elemet, majd kattintson az **+ Új riasztási szabály** elemre.

Kattintson a **kiválasztása** , válassza ki egy erőforrást. Az a **válasszon ki egy erőforrást** lapra, jelölje be **Log Analytics** a a **szűrés erőforrástípus szerint** listából. Válassza ki a Log Analytics-munkaterületet, és kattintson a **Kész** gombra.

![Erőforrás kiválasztása](./media/automation-tutorial-troubleshoot-changes/select-a-resource.png)

Kattintson a **feltétel hozzáadása**, az a **jellogika konfigurálása** lapon, a tábla, válassza ki a **egyéni naplóbeli keresés**. Adja meg az alábbi lekérdezést a Keresési lekérdezés szövegmezőben:

```loganalytics
ConfigurationChange | where ConfigChangeType == "WindowsServices" and SvcName == "W3SVC" and SvcState == "Stopped" | summarize by Computer
```

A lekérdezés azon számítógépek nevét adja vissza, amelyeken a W3SVC szolgáltatás le lett állítva a megadott időszakban.

A **Riasztási logika** területen a **Küszöbérték** legyen **0**. Ha elkészült, válassza a **Kész** lehetőséget.

![Jellogika konfigurálása](./media/automation-tutorial-troubleshoot-changes/configure-signal-logic.png)

A **Műveletcsoportok**válassza **hozzon létre új**. A műveletcsoport műveletek csoportja, amelyeket több riasztáson is alkalmazhat. Ezek a műveletek a teljesség igénye nélkül a következők lehetnek: e-mail-értesítések, runbookok, webhookok stb. A műveletcsoportokkal kapcsolatban további információt a [műveletcsoportok létrehozásáról és kezeléséről](../azure-monitor/platform/action-groups.md) szóló cikkben talál.

A **riasztás részletei**, adjon meg egy nevet és leírást a riasztás. A **Súlyosság** paraméter értéke legyen **Tájékoztató (Sev 2)**, **Figyelmeztető (Sev 1)** vagy **Kritikus (Sev 0)**.

A **Műveletcsoport neve** mezőben adja meg a riasztás nevét és egy rövid nevet. A rendszer a rövid nevet használja a műveletcsoport teljes neve helyett, amikor értesítéseket küld a csoport használatával.

A **Műveletek** szakaszban adja meg a művelet nevét, például **E-mail küldése a rendszergazdáknak**. A **MŰVELETTÍPUS** területen válassza az **E-mail/SMS/Küldés/Hang** lehetőséget. A **RÉSZLETEK** területen válassza a **Részletek szerkesztése** lehetőséget.

![Műveletcsoport hozzáadása](./media/automation-tutorial-troubleshoot-changes/add-action-group.png)

Az **E-mail/SMS/Küldés/Hang** ablaktáblán adjon meg egy nevet. Jelölje be az **E-mail** jelölőnégyzetet, és adjon meg egy érvényes e-mail-címet. Kattintson az **OK** gombra az **E-mail/SMS/Küldés/Hang**, majd az **OK** gombra a **Műveletcsoport hozzáadása** oldalon.

A riasztási e-mail tárgyának testreszabásához kattintson az **E-mail tárgya** gombra a **Szabály létrehozása** lap **Műveletek testreszabása** területén. Ha végzett, kattintson a **Riasztási szabály létrehozása** gombra. A riasztás figyelmezteti, ha egy frissítés telepítése sikeresen befejeződött, és tájékoztat róla, hogy mely számítógépeket érintett az adott frissítéstelepítés.

Az alábbi képen egy, a W3SVC szolgáltatás leállítása esetén kapott e-mailre látható példa.

![e-mail](./media/automation-tutorial-troubleshoot-changes/email.png)

## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Virtuális gép felvétele a Change Tracking és az Inventory megoldásba
> * Leállított szolgáltatások változásnaplóinak keresése
> * Változáskövetés konfigurálása
> * Tevékenységnapló csatlakozásának engedélyezése
> * Események aktiválása
> * Változások megtekintése
> * Riasztások konfigurálása

Ha többet szeretne megtudni a Change Tracking és az Inventory megoldásról, folytassa az áttekintéssel.

> [!div class="nextstepaction"]
> [Változáskezelés és az Inventory megoldás](automation-change-tracking.md)

