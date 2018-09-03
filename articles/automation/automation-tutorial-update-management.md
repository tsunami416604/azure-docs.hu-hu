---
title: Azure-beli Windows rendszerű virtuális gépek frissítéseinek és javításainak kezelése
description: Ez a cikk azt mutatja be, hogyan használható az Azure Automation Update Management az Azure-beli Windows rendszerű virtuális gépek frissítéseinek és javításainak kezeléséhez.
services: automation
author: zjalexander
ms.service: automation
ms.component: update-management
ms.topic: tutorial
ms.date: 08/29/2018
ms.author: zachal
ms.custom: mvc
ms.openlocfilehash: 8458aaee9f8d328d959fb47fb3e32af176d545b1
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43247368"
---
# <a name="manage-windows-updates-by-using-azure-automation"></a>Windows-frissítések kezelése az Azure Automationnel

A virtuális gépek frissítéseit és javításait az Update Management megoldás segítségével kezelheti. Ez az oktatóanyag azt ismerteti, hogyan mérheti fel legkönnyebben az elérhető frissítések állapotát, ütemezheti a szükséges frissítések telepítését, tekintheti át a telepítési eredményeket, és állíthat be riasztásokat, hogy ellenőrizze, sikeres volt-e a frissítések telepítése.

Díjszabási információkért tekintse meg az [Automation Update Management-díjszabását](https://azure.microsoft.com/pricing/details/automation/) ismertető cikket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Virtuális gép felvétele az Update Managementbe
> * Frissítésfelmérés megtekintése
> * Riasztások konfigurálása
> * Frissítéstelepítés ütemezése
> * Telepítés eredményeinek megtekintése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja havi Visual Studio-előfizetői Azure-kreditjeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Azure Automation-fiók](automation-offering-get-started.md) a megfigyelő és műveleti runbookok, valamint a figyelőfeladat tárolásához.
* A szolgáltatásba felvenni kívánt [virtuális gép](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="enable-update-management"></a>Az Update Management engedélyezése

Az oktatóanyag első lépéseként engedélyezze az Update Management megoldást a virtuális gépen:

1. Az Azure Portal bal oldali menüjében válassza a **Virtuális gépek** elemet. Válasszon ki egy virtuális gépet a listából.
2. A virtuális gép lapjának **MŰVELETEK** szakaszában válassza a **Frissítéskezelés** elemet. Megjelenik **Az Update Management engedélyezése** ablaktábla.

A rendszer ellenőrzi, hogy az Update Management engedélyezve van-e a virtuális gépen, illetve hogy létezik-e Azure Log Analytics-munkaterület és egy csatlakoztatott Automation-fiók, és az Update Management már jelen van-e a munkaterületen.

A [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)-munkaterület az Update Management, valamint a hasonló funkciók és szolgáltatások által létrehozott adatok gyűjtésére szolgál. A munkaterület egyetlen központi helyet biztosít a több forrásból származó adatok áttekintéséhez és elemzéséhez.

Az ellenőrzési folyamat arra is kiterjed, hogy a virtuális gépen működik-e a Microsoft Monitoring Agent (MMA) és az Automation hibrid runbook-feldolgozó. Ez az ügynök kommunikál az Azure Automationnel, és begyűjti a frissítési állapottal kapcsolatos információkat. Az ügynök számára a 443-as port szükséges, hogy kommunikálhasson az Azure Automation szolgáltatással és letölthesse a frissítéseket.

Ha az előkészítés közben az alábbi előfeltételek bármelyike hiányzik, a rendszer automatikusan hozzáadja azt:

* [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json)-munkaterület
* Egy [Automation-fiók](./automation-offering-get-started.md)
* Egy [hibrid runbook-feldolgozó](./automation-hybrid-runbook-worker.md) (a virtuális gépen engedélyezve)

Az **Update Management** adja meg a használni kívánt helyet, Log Analytics-munkaterületet és Automation-fiókot. Ezt követően válassza az **Engedélyezés** lehetőséget. Ha ezek a beállítások nem érhetők el, az azt jelenti, hogy egy másik automatizálási megoldás már engedélyezve van a virtuális gépen. Ebben az esetben az ahhoz tartozó munkaterületet és Automation-fiókot kell használnia.

![Az Update Management megoldás engedélyezése ablak](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

A megoldás engedélyezése néhány percet is igénybe vehet. Ez idő alatt ne zárja be a böngészőablakot. A megoldás engedélyezését követően a virtuális gép hiányzó frissítéseivel kapcsolatos adatok elkezdenek beérkezni a Log Analytics szolgáltatásba. Az adatok legalább 30 perc és legfeljebb 6 óra múlva állnak készen az elemzésre.

## <a name="view-update-assessment"></a>A frissítésfelmérés megtekintése

Az Update Management engedélyezése után megnyílik az **Update Management** ablaktábla. Hiányzó frissítések esetén azok listája a **Hiányzó frissítések** lapon található.

A frissítésekkel kapcsolatos támogatási cikk új ablakban történő megnyitásához kattintson a frissítés **INFORMÁCIÓS HIVATKOZÁSÁRA**. Ebben az ablakban fontos információkhoz juthat a frissítéssel kapcsolatban.

![Frissítés állapotának megtekintése](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

A frissítés bármely más részére kattintva megnyílik a kiválasztott frissítésre vonatkozó **Naplók keresése** ablaktábla. A naplókeresés lekérdezése az adott frissítéshez előre meg van adva. Ezt a lekérdezést módosíthatja, vagy létrehozhatja saját lekérdezését, amellyel részletes információkat jeleníthet meg a környezetben telepített vagy a hiányzó frissítésekre vonatkozóan.

![Frissítés állapotának megtekintése](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Riasztások konfigurálása

Ebben a lépésben egy riasztást állíthat be, amely értesíti, ha a frissítések sikeresen telepítve lettek egy Log Analytics lekérdezésen keresztül, vagy a sikertelen telepítések követésével az Update Management mesterrunbookjának segítségével.

### <a name="alert-conditions"></a>A riasztás feltételei

Különböző riasztástípusok esetén különböző riasztási feltételeket kell meghatározni.

#### <a name="log-analytics-query-alert"></a>Log Analytics-lekérdezés riasztása

Létrehozhat egy Log Analytics-lekérdezésen alapuló riasztást a sikeres telepítések esetére. Sikertelen telepítések esetén használhatja a [Runbook-riasztás](#runbook-alert) lépéseit, hogy tudomást szerezzen a vezénylők frissítésének sikertelen telepítéséről. Bármilyen egyéni lekérdezés alapján létrehozhat további riasztásokat, amelyekkel számos különféle forgatókönyv lefedhető.

Az Azure Portalon lépjen a **Figyelés** felületre, és válassza a **Riasztás létrehozása** lehetőséget.

Az **1. Riasztási feltétel megadása** szakaszban kattintson a **Cél kiválasztása** gombra. A **Szűrés erőforrástípus alapján** mezőben válassza a **Log Analytics** elemet. Válassza ki a Log Analytics-munkaterületet, és kattintson a **Kész** gombra.

![Riasztás létrehozása](./media/automation-tutorial-update-management/create-alert.png)

Kattintson a **Feltételek hozzáadása** elemre.

A **Jellogika konfigurálása** területen válassza ki az **Egyéni naplókeresés** elemet a táblázatban. Adja meg az alábbi lekérdezést a **Keresési lekérdezés** szövegmezőben:

```loganalytics
UpdateRunProgress
| where InstallationStatus == 'Succeeded'
| where TimeGenerated > now(-10m)
| summarize by UpdateRunName, Computer
```
A lekérdezés visszaadja a számítógépek és a frissítési menet nevét a megadott időszakra vonatkozóan.

A **Riasztási logika** területen a **Küszöbérték** legyen **1**. Ha elkészült, válassza a **Kész** lehetőséget.

![Jellogika konfigurálása](./media/automation-tutorial-update-management/signal-logic.png)

#### <a name="runbook-alert"></a>Runbook-riasztás

A sikertelen telepítésekkor értesülnie kell a mesterpéldány futtatási hibájáról. Az Azure Portalon lépjen a **Figyelés** felületre, és válassza a **Riasztás létrehozása** lehetőséget.

Az **1. Riasztási feltétel megadása** szakaszban kattintson a **Cél kiválasztása** gombra. A **Szűrés erőforrástípus alapján** mezőben válassza az **Automation-fiókok** elemet. Válassza ki az Automation-fiókját, majd kattintson a **Kész** gombra.

A **Runbook neve** mezőben kattintson a **\+** jelre, majd egyéni névnek írja be a következőt: **Patch-MicrosoftOMSComputers**. **Állapotnak** válassza a **Sikertelen** lehetőséget, vagy kattintson a **\+** jelre, hogy **Sikertelenként** jelölhesse meg a telepítést.

![Runbookok jellogikájának konfigurálása](./media/automation-tutorial-update-management/signal-logic-runbook.png)

A **Riasztási logika** területen a **Küszöbérték** legyen **1**. Ha elkészült, válassza a **Kész** lehetőséget.

### <a name="alert-details"></a>Riasztás részletei

A **2. Riasztás részleteinek megadása** résznél adja meg a riasztás nevét és leírását. Állítsa a **Súlyosság** beállítást sikeres futtatás esetén **Tájékoztató (Sev 2)** értékre, sikertelen futtatás esetén pedig **Tájékoztató (Sev 1)** értékre.

![Jellogika konfigurálása](./media/automation-tutorial-update-management/define-alert-details.png)

A **3. Műveleti csoport megadása** szakaszban kattintson az **Új műveletcsoport** gombra. A műveletcsoport műveletek csoportja, amelyeket több riasztáson is alkalmazhat. Ezek a műveletek a teljesség igénye nélkül a következők lehetnek: e-mail-értesítések, runbookok, webhookok stb. A műveletcsoportokkal kapcsolatban további információt a [műveletcsoportok létrehozásáról és kezeléséről](../monitoring-and-diagnostics/monitoring-action-groups.md) szóló cikkben talál.

A **Műveletcsoport neve** mezőben adja meg a riasztás nevét és egy rövid nevet. A rendszer a rövid nevet használja a műveletcsoport teljes neve helyett, amikor értesítéseket küld a csoport használatával.

A **Műveletek** szakaszban adja meg a művelet nevét, például **E-mail-értesítések**. A **MŰVELETTÍPUS** területen válassza az **E-mail/SMS/Küldés/Hang** lehetőséget. A **RÉSZLETEK** területen válassza a **Részletek szerkesztése** lehetőséget.

Az **E-mail/SMS/Küldés/Hang** ablaktáblán adjon meg egy nevet. Jelölje be az **E-mail** jelölőnégyzetet, és adjon meg egy érvényes e-mail-címet.

![E-mail műveletcsoport konfigurálása](./media/automation-tutorial-update-management/configure-email-action-group.png)

Az **E-mail/SMS/Küldés/Hang** ablaktáblán kattintson az **OK** gombra. A **Műveletcsoport hozzáadása** ablaktáblán kattintson az **OK** gombra.

A riasztási e-mail tárgyának testreszabásához kattintson az **E-mail tárgya** gombra a **Szabály létrehozása** lap **Műveletek testreszabása** területén. Ha végzett, kattintson a **Riasztási szabály létrehozása** gombra. A riasztás figyelmezteti, ha egy frissítés telepítése sikeresen befejeződött, és tájékoztat róla, hogy mely számítógépeket érintett az adott frissítéstelepítés.

## <a name="schedule-an-update-deployment"></a>Frissítéstelepítés ütemezése

Következő lépésként ütemezzen egy olyan telepítést a frissítésekhez, amely megfelel a kiadási ütemtervnek és a szolgáltatási időkeretnek. Kiválaszthatja, hogy a telepítés milyen típusú frissítéseket tartalmazzon. Például hozzáadhatja a kritikus vagy a biztonsági frissítéseket, és kizárhatja a kumulatív frissítéseket.

Ütemezzen egy új frissítéstelepítést a virtuális géphez. Ehhez válassza a **Frissítéskezelés**, majd a **Frissítések központi telepítésének ütemezése** elemet.

Az **Új frissítéstelepítés** képernyőn adja meg a következő információkat:

* **Név**: Adjon egyedi nevet a frissítéstelepítésnek.

* **Operációs rendszer**: Válassza ki azt az operációs rendszert, amelyre a frissítéstelepítés vonatkozni fog.

* **Frissítendő gépek**: Válasszon ki egy Mentett keresést vagy Importált csoportot, vagy válassza a legördülő listában a Gép lehetőséget, és válasszon ki egyes gépeket. Ha a **Gépek** lehetőséget választotta, a gép állapota az **ÜGYNÖK KÉSZÜLTSÉGÉNEK FRISSÍTÉSE** oszlopban látható. A számítógépcsoportok Log Analyticsben lévő létrehozásának különböző módszereivel kapcsolatos további információkért tekintse meg a [Log Analytics számítógépcsoportjait](../log-analytics/log-analytics-computer-groups.md) ismertető részt

* **Frissítési besorolás**: Válassza ki azokat a szoftvertípusokat, amelyeket a frissítéstelepítés belefoglal a telepítésbe. Ebben az oktatóanyagban hagyjon minden típust kiválasztva.

  A választható besorolási típusok a következők:

   |Operációs rendszer  |Típus  |
   |---------|---------|
   |Windows     | Kritikus frissítések</br>Biztonsági frissítések</br>Kumulatív frissítések</br>Funkciócsomagok</br>Szervizcsomagok</br>Definíciófrissítések</br>Eszközök</br>Frissítések        |
   |Linux     | Kritikus vagy biztonsági frissítések</br>Egyéb frissítések       |

   A besorolási típusok ismertetését [a frissítések besorolását](automation-update-management.md#update-classifications) leíró szakaszban találja.

* **Ütemezési beállítások**: Megnyitja az **Ütemezési beállítások** ablaktáblát. Az alapértelmezett kezdési időpont az aktuális időpontnál 30 perccel későbbi időpont. Bármilyen időpontra beállítható a pillanatnyi időt követő 10. perc után.

   Azt is megadhatja, hogy a telepítés egyszer történjen meg, vagy ismétlődő legyen. Az **Ismétlődés** menüpontban válassza az **Egyszer** lehetőséget. Tartsa meg az 1 napos alapértelmezett beállítást, majd kattintson az **OK** gombra. Ezzel egy ismétlődő ütemezést állít be.

* **Karbantartási időszak (perc)**: Fogadja el az alapértelmezett értéket. Megadhatja azt az időtartamot, amelyen belül szeretne sort keríteni a frissítés telepítésére. Ez a beállítás biztosítja, hogy a módosítások a megadott szolgáltatási időkereten belül menjenek végbe.

* **Újraindítási beállítások**: Ez a beállítás határozza meg az újraindítások kezelésének módját. Az elérhető lehetőségek:
  * Újraindítás szükség esetén (alapértelmezett beállítás)
  * Mindig induljon újra
  * Soha ne induljon újra
  * Csak újraindítás – frissítések nem lesznek telepítve

Ha végzett az ütemezés beállításával, kattintson a **Létrehozás** gombra.

![A frissítés ütemezési beállításai ablaktábla](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Ekkor visszalép az állapot-irányítópultra. Kattintson az **Ütemezett frissítéstelepítések** lehetőségre a létrehozott telepítési ütemezés megjelenítéséhez.

## <a name="view-results-of-an-update-deployment"></a>Frissítéstelepítés eredményeinek megtekintése

Miután az ütemezett telepítés elindult, a **Frissítéskezelés** panel **Frissítéstelepítések** lapján láthatóvá válik a telepítés állapota. Ha a telepítés fut, az állapota **Folyamatban** lesz. A telepítés befejezése után, ha az sikeresnek bizonyult, **Sikeres** állapotúra változik. Ha a telepítésben lévő frissítések közül egy vagy több meghiúsul, az állapota **Részben sikertelen**.

Ha kiválasztja a befejezett frissítéstelepítést, az megjelenik az adott frissítéstelepítés irányítópultja.

![Adott telepítés frissítéstelepítési állapotának irányítópultja](./media/automation-tutorial-update-management/manageupdates-view-results.png)

A **Frissítés eredményei** terület összesíti a frissítések teljes számát és az adott virtuális gépre vonatkozó telepítési eredményeket. A jobb oldali táblázat az egyes frissítések részletes áttekintését és a telepítés eredményét tartalmazza.

A következő lista az elérhető értékeket jeleníti meg:

* **Nem lett megkísérelve**: a frissítés nem lett telepítve, mert a megadott karbantartási időszak alapján nem lett volna rá elég idő.
* **Sikeres**: A frissítés sikeres volt.
* **Sikertelen**: A frissítés sikertelen volt.

A telepítés által létrehozott összes naplóbejegyzés megtekintéséhez válassza **Az összes napló** elemet.

Válassza a **Kimenet** lehetőséget azon runbook feladatstreamjének megtekintéséhez, amely a frissítések telepítését kezeli a virtuális célgépen.

A telepítés közben felmerülő hibák részletes információinak megtekintéséhez válassza a **Hibák** elemet.

Ha a frissítés telepítése sikeresen befejeződött, az alábbihoz hasonló e-mailt kap a sikeres telepítésről:

![E-mail konfigurálása műveletcsoport](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Virtuális gép felvétele az Update Managementbe
> * Frissítésfelmérés megtekintése
> * Riasztások konfigurálása
> * Frissítéstelepítés ütemezése
> * Telepítés eredményeinek megtekintése

Folytassa az Update Management megoldás áttekintésével.

> [!div class="nextstepaction"]
> [Az Update Management megoldás](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json)
