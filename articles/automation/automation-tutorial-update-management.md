---
title: Azure-beli Windows rendszerű virtuális gépek frissítéseinek és javításainak kezelése
description: Ez a cikk azt mutatja be, hogyan használható az Azure Automation Update Management az Azure-beli Windows rendszerű virtuális gépek frissítéseinek és javításainak kezeléséhez.
services: automation
author: zjalexander
ms.service: automation
ms.component: update-management
ms.topic: tutorial
ms.date: 12/04/2018
ms.author: zachal
ms.custom: mvc
ms.openlocfilehash: 83647dfb0965b8aac8ede5f2e9669ae3d7722c41
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184984"
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

Ebben a lépésben megismerheti riasztást beállításához, hogy tudja a frissítéstelepítés állapotát.

### <a name="alert-conditions"></a>A riasztás feltételei

Az Automation-fiók alatt **figyelés** lépjen a **riasztások**, és kattintson a **+ Új riasztási szabály**.

Az Automation-fiók az erőforrás már ki van választva. Ha módosítani szeretné, kattintson **válassza** és a a **válasszon ki egy erőforrást** lapon válassza ki **Automation-fiókok** a a **szűrőerőforrástípusszerint** legördülő listából. Válassza ki az Automation-fiókját, majd kattintson a **Kész** gombra.

Kattintson a **feltétel hozzáadása** a megfelelő központi telepítési a jel kiválasztásához. Az alábbi táblázat a frissítési telepítések esetén két elérhető jelekkel részleteit jeleníti meg:

|Jel neve|Dimenziók|Leírás|
|---|---|---|
|**Teljes frissítés központi telepítésének futtatásai**|-A frissítéstelepítés nevét</br>– Állapot|A jel használatos általános állapotát, a frissítéstelepítések riasztásra.|
|**Teljes frissítés üzembe helyezési gép futtatások**|-A frissítéstelepítés nevét</br>– Állapot</br>-A cél számítógépen</br>-Telepítés futtatási azonosítója|A jel szolgál egy adott gépek célzó központi telepítésének állapota riasztás|

A dimenzióértékek válasszon ki egy érvényes értéket a listából. Ha a keresett érték nem szerepel a listában, kattintson a **\+** a dimenzió, és írja be az egyéni név melletti jelre. Ezután válassza ki a keresni kívánt érték. Ha azt szeretné, válassza ki az összes értéket egy dimenzió, kattintson a **kiválasztása \***  gombra. Ha nem választ egy dimenzió értékét, a dimenzió figyelmen kívül kiértékelés során.

![Jellogika konfigurálása](./media/automation-tutorial-update-management/signal-logic.png)

A **Riasztási logika** területen a **Küszöbérték** legyen **1**. Ha elkészült, válassza a **Kész** lehetőséget.

### <a name="alert-details"></a>Riasztás részletei

A **2. Riasztás részleteinek megadása** résznél adja meg a riasztás nevét és leírását. Állítsa a **Súlyosság** beállítást sikeres futtatás esetén **Tájékoztató (Sev 2)** értékre, sikertelen futtatás esetén pedig **Tájékoztató (Sev 1)** értékre.

![Jellogika konfigurálása](./media/automation-tutorial-update-management/define-alert-details.png)

A **Műveletcsoportok**válassza **hozzon létre új**. A műveletcsoport műveletek csoportja, amelyeket több riasztáson is alkalmazhat. Ezek a műveletek a teljesség igénye nélkül a következők lehetnek: e-mail-értesítések, runbookok, webhookok stb. A műveletcsoportokkal kapcsolatban további információt a [műveletcsoportok létrehozásáról és kezeléséről](../azure-monitor/platform/action-groups.md) szóló cikkben talál.

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

* **Név**: Adjon meg egy egyedi nevet a frissítéstelepítés számára.

* **Operációs rendszer**: Válassza ki az operációs rendszer központi telepítéséhez a cél.

* **A csoportok frissítése (előzetes verzió)**: Egy előfizetés, erőforráscsoport, helyek és címkék felvenni az üzembe helyezés az Azure-beli virtuális dinamikus csoportot hozhat létre kombinációja alapján lekérdezést határoz meg. További tudnivalókért lásd: [dinamikus csoportok](automation-update-management.md#using-dynamic-groups)

* **Frissítendő gépek**: Válassza ki, mentett keresést, importált csoporthoz, vagy a legördülő listából válassza ki a gépet, és válassza ki az egyes gépek. Ha a **Gépek** lehetőséget választotta, a gép állapota az **ÜGYNÖK KÉSZÜLTSÉGÉNEK FRISSÍTÉSE** oszlopban látható. A számítógépcsoportok Log Analyticsben lévő létrehozásának különböző módszereivel kapcsolatos további információkért tekintse meg a [Log Analytics számítógépcsoportjait](../azure-monitor/platform/computer-groups.md) ismertető részt

* **Frissítési besorolás**: Válassza ki a szoftvertípusokat, amelyeket a frissítéstelepítés az üzemelő példány tartalmazza. Ebben az oktatóanyagban hagyjon minden típust kiválasztva.

  A választható besorolási típusok a következők:

   |Operációs rendszer  |Típus  |
   |---------|---------|
   |Windows     | Kritikus frissítések</br>Biztonsági frissítések</br>Kumulatív frissítések</br>Funkciócsomagok</br>Szervizcsomagok</br>Definíciófrissítések</br>Eszközök</br>Frissítések        |
   |Linux     | Kritikus vagy biztonsági frissítések</br>Egyéb frissítések       |

   A besorolási típusok ismertetését [a frissítések besorolását](automation-update-management.md#update-classifications) leíró szakaszban találja.

* **Belefoglalandó/kizárandó frissítések** – Ez megnyitja a **Belefoglalás/kizárás** lapot. A belefoglalandó vagy kizárandó frissítések külön lapokon jelennek meg. A belefoglalási kezelésének további információkért lásd: [belefoglalási viselkedés](automation-update-management.md#inclusion-behavior)

* **Ütemezési beállítások**: A **ütemezési beállítások** panel nyílik meg. Az alapértelmezett kezdési időpont az aktuális időpontnál 30 perccel későbbi időpont. Bármilyen időpontra beállítható a pillanatnyi időt követő 10. perc után.

   Azt is megadhatja, hogy a telepítés egyszer történjen meg, vagy ismétlődő legyen. Az **Ismétlődés** menüpontban válassza az **Egyszer** lehetőséget. Tartsa meg az 1 napos alapértelmezett beállítást, majd kattintson az **OK** gombra. Ezzel egy ismétlődő ütemezést állít be.

* **Előre parancsfájlok + utáni parancsfájlok**: Válassza ki a parancsfájlok futtatása előtt és után a központi telepítés. További információ: [Előkészítő és utólagos szkriptek kezelése](pre-post-scripts.md).
* **Karbantartási időszak (perc)**: Ne módosítsa az alapértelmezett értéket. Megadhatja azt az időtartamot, amelyen belül szeretne sort keríteni a frissítés telepítésére. Ez a beállítás biztosítja, hogy a módosítások a megadott szolgáltatási időkereten belül menjenek végbe.

* **Indítsa újra a beállítások**: Ez a beállítás határozza meg, hogyan újraindítások kell kezelni. Az elérhető lehetőségek:
  * Újraindítás szükség esetén (alapértelmezett beállítás)
  * Mindig induljon újra
  * Soha ne induljon újra
  * Csak újraindítás – frissítések nem lesznek telepítve

Ha végzett az ütemezés beállításával, kattintson a **Létrehozás** gombra.

![A frissítés ütemezési beállításai ablaktábla](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Ekkor visszalép az állapot-irányítópultra. Kattintson az **Ütemezett frissítéstelepítések** lehetőségre a létrehozott telepítési ütemezés megjelenítéséhez.

> [!NOTE]
> Az Update Management a belső frissítések üzembe helyezését és a javítócsomagok előzetes letöltését támogatja. Ehhez módosításokat kell végrehajtani a javítani kívánt rendszereken. A [belső és előre letöltött frissítéssel](automation-update-management.md#firstparty-predownload) foglalkozó részben tudhatja meg, hogyan konfigurálja ezeket a beállításokat a rendszereken.

## <a name="view-results-of-an-update-deployment"></a>Frissítéstelepítés eredményeinek megtekintése

Miután az ütemezett telepítés elindult, a **Frissítéskezelés** panel **Frissítéstelepítések** lapján láthatóvá válik a telepítés állapota. Ha a telepítés fut, az állapota **Folyamatban** lesz. A telepítés befejezése után, ha az sikeresnek bizonyult, **Sikeres** állapotúra változik. Ha a telepítésben lévő frissítések közül egy vagy több meghiúsul, az állapota **Részben sikertelen**.

Ha kiválasztja a befejezett frissítéstelepítést, az megjelenik az adott frissítéstelepítés irányítópultja.

![Adott telepítés frissítéstelepítési állapotának irányítópultja](./media/automation-tutorial-update-management/manageupdates-view-results.png)

A **Frissítés eredményei** terület összesíti a frissítések teljes számát és az adott virtuális gépre vonatkozó telepítési eredményeket. A jobb oldali táblázat az egyes frissítések részletes áttekintését és a telepítés eredményét tartalmazza.

A következő lista az elérhető értékeket jeleníti meg:

* **Nem lett megkísérelve**: A frissítés telepítése nem volt, mert nincs elég rendelkezésre álló idő a karbantartási időszak alapján meghatározott.
* **Sikeres**: A frissítés sikeres volt.
* **Nem sikerült**: A frissítés sikertelen volt.

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
