---
title: Azure-beli Windows rendszerű virtuális gépek frissítéseinek és javításainak kezelése
description: Ez a cikk azt mutatja be, hogyan használható az Azure Automation – Update Management az Azure-beli Windows rendszerű virtuális gépek frissítéseinek és javításainak kezeléséhez.
services: automation
author: zjalexander
ms.service: automation
ms.component: update-management
ms.topic: tutorial
ms.date: 02/28/2018
ms.author: zachal
ms.custom: mvc
ms.openlocfilehash: 84ec2a5852e6aaeb4b9fe6ef11924209d03fb54b
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="manage-windows-updates-with-azure-automation"></a>Windows-frissítések kezelése az Azure Automationnel

Az Update Management segítségével kezelheti a virtuális gépek frissítéseit és javításait.
Ebben az oktatóanyagban megismerheti, hogyan mérheti fel az elérhető frissítések állapotát, ütemezheti a szükséges frissítések telepítését, tekintheti át a telepítési eredményeket, és állíthat be riasztásokat, hogy ellenőrizze, sikeres volt-e a frissítések telepítése.

Díjszabási információkért tekintse meg az [Automation Update Management-díjszabását](https://azure.microsoft.com/pricing/details/automation/) ismertető cikket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Virtuális gép felvétele az Update Management megoldásba
> * Frissítésfelmérés megtekintése
> * Riasztások konfigurálása
> * Frissítéstelepítés ütemezése
> * Telepítés eredményeinek megtekintése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja havi Visual Studio-előfizetői Azure-kreditjeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](automation-offering-get-started.md) a megfigyelő és műveleti runbookok, valamint a figyelőfeladat tárolásához.
* A szolgáltatásba felvenni kívánt [virtuális gép](../virtual-machines/windows/quick-create-portal.md).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.

## <a name="enable-update-management"></a>Az Update Management engedélyezése

Az oktatóanyag első lépéseként engedélyeznie kell az Update Management megoldást a virtuális gépen.

1. Az Azure Portal bal oldali menüjében kattintson a **Virtuális gépek** elemre, majd válasszon ki egy virtuális gépet a listából.
2. A virtuális gép lapjának **Műveletek** szakaszában kattintson a **Frissítéskezelés** gombra. Megjelenik az **Update Management engedélyezése** lap.

A rendszer ellenőrzi, hogy az Update Management engedélyezve van-e a virtuális gépen. A rendszer eközben azt is ellenőrzi, hogy létezik-e Log Analytics-munkaterület és egy csatlakoztatott Automation-fiók, valamint hogy a Frissítéskezelési megoldás már jelen van-e a munkaterületen.

A [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)-munkaterület az Update Management, valamint a hasonló funkciók és szolgáltatások által létrehozott adatok gyűjtésére szolgál. A munkaterület egyetlen központi helyet biztosít a több forrásból származó adatok áttekintéséhez és elemzéséhez.

Az ellenőrzési folyamat arra is kiterjed, hogy a virtuális gépen működik-e a Microsoft Monitoring Agent (MMA) és az Automation hibrid runbook-feldolgozó.
Ez az ügynök kommunikál az Azure Automationnel, továbbá begyűjti a frissítési állapottal kapcsolatos információkat. Az ügynök számára a 443-as port szükséges, hogy kommunikálhasson az Azure Automation szolgáltatással és letölthesse a frissítéseket.

Ha az előkészítés közben az alábbi előfeltételek bármelyike hiányzik, a rendszer automatikusan hozzáadja azt:

* [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)-munkaterület
* [Automation-fiók](./automation-offering-get-started.md)
* Engedélyezett [hibrid runbook-feldolgozó](./automation-hybrid-runbook-worker.md) a virtuális gépen

Megnyílik az **Update Management** képernyő. Konfigurálja a használni kívánt helyet, Log Analytics-munkaterületet és Automation-fiókot, majd kattintson az **Engedélyezés** gombra. Ha a mezők szürkén jelennek meg, az azt jelenti, hogy egy másik automatizálási megoldás már engedélyezve van a virtuális gépen, ezért az ahhoz tartozó munkaterületet és Automation-fiókot kell használnia.

![Az Update Management megoldás engedélyezése ablak](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

A megoldás engedélyezése néhány percet is igénybe vehet. Ez idő alatt ne zárja be a böngészőablakot.
A megoldás engedélyezését követően a virtuális gép hiányzó frissítéseivel kapcsolatos adatok elkezdenek beérkezni a Log Analytics szolgáltatásba.
Az adatok legalább 30 perc és legfeljebb 6 óra múlva állnak készen az elemzésre.

## <a name="view-update-assessment"></a>A frissítésfelmérés megtekintése

A **Frissítéskezelés** engedélyezése után megjelenik a **Frissítéskezelés** képernyő.
Hiányzó frissítések esetén azok listája a **Hiányzó frissítések** lapon található.

A frissítésekkel kapcsolatos támogatási cikk megnyitásához kattintson a frissítésen található **INFORMÁCIÓS HIVATKOZÁSRA**. Itt fontos információkhoz juthat a frissítéssel kapcsolatban.

![Frissítés állapotának megtekintése](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

A frissítés bármely más részére kattintva megnyílik a kiválasztott frissítésre vonatkozó **Naplók keresése** ablak. A naplókeresés lekérdezése az adott frissítéshez előre meg van adva. Ezt a lekérdezést módosíthatja, vagy létrehozhatja saját lekérdezését, amellyel részletes információkat jeleníthet meg a környezetben telepített vagy a hiányzó frissítésekre vonatkozóan.

![Frissítés állapotának megtekintése](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerting"></a>Riasztások konfigurálása

Ebben a lépésben egy riasztást konfigurálunk, amely értesíti, ha a frissítések sikeresen telepítve lettek. A létrehozandó riasztás egy Log Analytics-lekérdezésen alapul. Bármilyen egyéni lekérdezés alapján létrehozhatók további riasztások, amelyekkel számos különféle forgatókönyv lefedhető. Az Azure Portalon lépjen a **Monitorozás** felületre, és kattintson a **Riasztás létrehozása** gombra. ez megnyitja a **Szabály létrehozása** lapot.

Az **1. Riasztási feltétel megadása** szakaszban kattintson a **+ Cél kiválasztása** gombra. A **Szűrés erőforrástípus alapján** mezőben válassza a **Log Analytics** elemet. Válassza ki a Log Analytics-munkaterületet, és kattintson a **Kész** gombra.

![riasztás létrehozása](./media/automation-tutorial-update-management/create-alert.png)

Kattintson a **+ Feltételek hozzáadása** gombra a **Jellogika konfigurálása** oldal megnyitásához. Válassza az **Egyéni naplókeresés** elemet a táblázatban. Adja meg az alábbi lekérdezést a **Keresési lekérdezés** szövegmezőben. A lekérdezés visszaadja a számítógépek és a frissítési menet nevét a megadott időszakra vonatkozóan.

```loganalytics
UpdateRunProgress
| where InstallationStatus == 'Succeeded'
| where TimeGenerated > now(-10m)
| summarize by UpdateRunName, Computer
```

A Riasztási logika **Küszöb** beállításánál adja meg az **1** értéket. Ha végzett, kattintson a **Kész** gombra.

![Jellogika konfigurálása](./media/automation-tutorial-update-management/signal-logic.png)

A **2. Riasztás részleteinek megadása** szakaszban adja meg a riasztás rövid nevét és leírását. Állítsa a **Súlyosság** beállítást **Tájékoztató (2-es súlyosság)** értékre, mivel a riasztás sikeres futtatásokra vonatkozik.

![Jellogika konfigurálása](./media/automation-tutorial-update-management/define-alert-details.png)

A **3. Műveleti csoport megadása** szakaszban kattintson az **+ Új műveletcsoport** gombra. A műveletcsoport műveletek csoportja, amelyeket több riasztáson is alkalmazhat. Ezek a teljesség igénye nélkül a következők lehetnek: e-mail-értesítések, runbookok, webhookok stb. A műveletcsoportokkal kapcsolatban további információt a [műveletcsoportok létrehozásáról és kezeléséről](../monitoring-and-diagnostics/monitoring-action-groups.md) szóló cikkben talál.

A **Műveletcsoport neve** mezőben adjon meg egy felhasználóbarát és egy rövid nevet. A rendszer a rövid nevet használja a műveletcsoport teljes neve helyett, amikor értesítéseket küld a csoport használatával.

A **Műveletek** szakaszban adja meg a művelet felhasználóbarát nevét (például **E-mail-értesítések**), és a **MŰVELETTÍPUS** alatt válassza az **E-mail/SMS/Leküldéses/Hang** lehetőséget. A **RÉSZLETEK** területen válassza a **Részletek szerkesztése** lehetőséget.

Az **E-mail/SMS/Leküldés/Hang** oldalon adjon meg egy nevet. Jelölje be az **E-mail** jelölőnégyzetet, és adja meg a használni kívánt érvényes e-mail-címet.

![E-mail konfigurálása műveletcsoport](./media/automation-tutorial-update-management/configure-email-action-group.png)

Kattintson az **OK** gombra az **E-mail/SMS/Leküldés/Hang** oldalon az oldal, majd az **OK** gombra a **Műveletcsoport hozzáadása** oldal bezárásához.

A küldött e-mail tárgyának testreszabásához kattintson az **E-mail tárgya** gombra a **Szabály létrehozása** lap **Műveletek testreszabása** területén. Amikor végzett, kattintson a **Riasztási szabály létrehozása** gombra. Ezzel létrehoz egy szabályt, amely riasztást küld, ha egy frissítés telepítése sikeresen befejeződik, és megadja, hogy mely számítógépekre terjedt ki az adott frissítéstelepítés.

## <a name="schedule-an-update-deployment"></a>Frissítéstelepítés ütemezése

Most, hogy a riasztások konfigurálva vannak, ütemezzen egy telepítést a frissítések telepítésére, amely megfelel a kiadási ütemtervnek és a szolgáltatási időkeretnek.
Kiválaszthatja, hogy a telepítés milyen típusú frissítéseket tartalmazzon.
Például hozzáadhatja a kritikus vagy a biztonsági frissítéseket, és kizárhatja a kumulatív frissítéseket.

> [!WARNING]
> Ha a frissítésekhez újraindításra van szükség, a rendszer automatikusan újraindítja a virtuális gépet.

Ütemezzen egy új frissítéstelepítést a virtuális géphez. Ehhez lépjen vissza az **Update Management** párbeszédpanelre, és válassza a képernyő felső részén található **Update Management ütemezése** lehetőséget.

Az **Új frissítéstelepítés** képernyőn adja meg a következő információkat:

* **Név** – Adjon meg egy egyedi nevet a frissítéstelepítés számára.

* **Operációs rendszer** – Válassza ki a frissítéstelepítés által célzott operációs rendszert.

* **Frissítési besorolás** – Válassza ki azokat a szoftvertípusokat, amelyeket a frissítéstelepítés belefoglal a telepítésbe. Ebben az oktatóanyagban hagyjon minden típust kiválasztva.

  A választható besorolási típusok a következők:

   |Operációs rendszer  |Típus  |
   |---------|---------|
   |Windows     | Kritikus frissítések</br>Biztonsági frissítések</br>Kumulatív frissítések</br>Funkciócsomagok</br>Szervizcsomagok</br>Definíciófrissítések</br>Eszközök</br>Frissítések        |
   |Linux     | Kritikus vagy biztonsági frissítések</br>Egyéb frissítések       |

   A besorolási típusok ismertetését [a frissítések besorolását](automation-update-management.md#update-classifications) leíró szakaszban találja.

* **Ütemezési beállítások** – Megnyitja az Ütemezési beállítások oldalt. Az alapértelmezett kezdési időpont az aktuális időpontnál 30 perccel későbbi időpont. Bármilyen időpontra beállítható az aktuális időpontot követő 10. perc után.

   Azt is megadhatja, hogy a telepítés egyszer történjen meg, vagy ismétlődjön.
   Válassza az **Egyszer** lehetőséget az **Ismétlődés** menüpontban. Fogadja el az alapértelmezett 1 napos értékeket, és kattintson az **OK** gombra. Ezzel egy ismétlődő ütemezést állít be.

* **Karbantartási időszak (perc)** – Fogadja el az alapértelmezett értéket. Megadhatja azt az időtartamot, amelyen belül szeretné, hogy a frissítés telepítése megtörténjen. Ez a beállítás biztosítja, hogy a módosítások a megadott szolgáltatási időkereten belül menjenek végbe.

![A frissítés ütemezés beállításai képernyője](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Ha befejezte az ütemezés konfigurálását, kattintson a **Létrehozás** gombra. Ekkor visszalép az állapot-irányítópultra. Kattintson az **Ütemezett frissítéstelepítések** lehetőségre a létrehozott telepítési ütemezés megjelenítéséhez.

## <a name="view-results-of-an-update-deployment"></a>Frissítéstelepítés eredményeinek megtekintése

Miután az ütemezett telepítés elindult, az **Update Management** képernyő **Frissítéstelepítések** lapján láthatóvá válik a telepítés állapota.
Ha éppen fut, az állapota **Folyamatban**.
Ha sikeresen befejeződik, **Sikeres** állapotúra változik.
Ha a telepítésben lévő frissítések közül egy vagy több meghiúsul, az állapota **Részben sikertelen**.
Ha rákattint a befejezett frissítéstelepítésre, megjelenik az adott frissítéstelepítés irányítópultja.

![Adott telepítés frissítéstelepítési állapot-irányítópultja](./media/automation-tutorial-update-management/manageupdates-view-results.png)

A **Frissítés eredményei** csempe összesíti a frissítések teljes számát és az adott virtuális gépre vonatkozó telepítési eredményeket.
A jobb oldali táblázat az egyes frissítések részletes áttekintését és a telepítés eredményét tartalmazza.
A következő lista az elérhető értékeket jeleníti meg:

* **Nem lett megkísérelve** – a frissítés nem lett telepítve, mert a megadott karbantartási időszak alapján nem lett volna rá elég idő.
* **Sikeres** – a frissítés sikeres volt
* **Sikertelen** – a frissítés sikertelen volt

Kattintson a **Minden napló** csempére a telepítés által létrehozott összes naplóbejegyzés megtekintéséhez.

Kattintson a **Kimenet** csempére azon runbook feladatstreamjének megtekintéséhez, amely a frissítések telepítését kezeli a cél virtuális gépen.

Kattintson a **Hibák** csempére a telepítés közben felmerülő hibák részletes információinak megtekintéséhez.

Ha a frissítés telepítése sikeresen befejeződött, egy, a következő képen láthatóhoz hasonló e-mailt kap a sikeres telepítésről.

![E-mail konfigurálása műveletcsoport](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Virtuális gép felvétele az Update Management megoldásba
> * Frissítésfelmérés megtekintése
> * Riasztások konfigurálása
> * Frissítéstelepítés ütemezése
> * Telepítés eredményeinek megtekintése

Folytassa az Update Management megoldás áttekintésével.

> [!div class="nextstepaction"]
> [Az Update Management megoldás](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json)
