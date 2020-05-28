---
title: Azure-beli virtuális gépek frissítéseinek és javításának kezelése Azure Automation
description: Ez a cikk azt ismerteti, hogyan használható a Update Management az Azure-beli virtuális gépek frissítéseinek és javításának kezeléséhez.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: 5bc71c5462ed1c721d2c94f889146400a07dd19e
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84119200"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Azure-beli virtuális gépek frissítéseinek és javításának kezelése

Ez a cikk bemutatja, hogyan kezelheti az Azure-beli virtuális gépek frissítéseit és javításait a Azure Automation [Update Management](automation-update-management.md) szolgáltatással. A díjszabással kapcsolatos információkért lásd: [Update Management automatizálási díjszabása](https://azure.microsoft.com/pricing/details/automation/).

> [!NOTE]
> A Update Management támogatja az első féltől származó frissítések telepítését és a javítások előzetes letöltését. Ehhez a támogatáshoz módosításokat kell elvégezni a javított rendszereken. A beállítások a rendszereken való konfigurálásával kapcsolatban lásd: [Windows Update beállításainak konfigurálása Azure Automation Update Management](automation-configure-windows-update.md) .

Az ebben a cikkben ismertetett eljárások használata előtt győződjön meg arról, hogy engedélyezte Update Management a virtuális gépeken a következő módszerek egyikének használatával:

* [Az Update Management engedélyezése Automation-fiókból](automation-onboard-solutions-from-automation-account.md)
* [Update Management engedélyezése a Azure Portal tallózásával](automation-onboard-solutions-from-browse.md)
* [Az Update Management engedélyezése runbookból](automation-onboard-solutions.md)
* [Az Update Management engedélyezése Azure-beli virtuális gépről](automation-onboard-solutions-from-vm.md)

## <a name="view-update-assessment"></a>A frissítésfelmérés megtekintése

Frissítés értékelésének megtekintése:

1. Az Automation-fiókban válassza **a frissítés kezelése** lehetőséget az **Update Management**alatt. 

2. A környezet frissítései a frissítés kezelése oldalon találhatók. Ha a rendszer hiányzóként azonosította a frissítéseket, a hiányzó **frissítések** lapon megjelenik a hiányzó frissítések listája.

3. Az **információ hivatkozás**alatt válassza ki a frissítésre mutató hivatkozást a támogatási cikk megnyitásához, amely fontos információkat nyújt a frissítésről.

    ![Frissítés állapotának megtekintése](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

4. A frissítés gombra kattintva nyissa meg a naplóbeli keresés ablaktáblát. A naplókeresés lekérdezése az adott frissítéshez előre meg van adva. Módosíthatja a lekérdezést, vagy létrehozhat egy saját lekérdezést a részletes információk megtekintéséhez.

    ![Frissítés állapotának megtekintése](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Riasztások konfigurálása

Kövesse az alábbi lépéseket a riasztások beállításához, hogy megismerje a frissítések központi telepítésének állapotát:

1. Az Automation-fiókjában lépjen a **riasztások** elemre a **figyelés**területen, majd kattintson az **új riasztási szabály**elemre.

2. A riasztási szabály létrehozása lapon az Automation-fiók már ki van választva erőforrásként. Ha módosítani szeretné, kattintson az **erőforrás szerkesztése**elemre. 

3. Az erőforrás kiválasztása lapon válassza az Automation- **fiókok** lehetőséget a **szűrés erőforrás típusa** legördülő menüből. 

4. Válassza ki a használni kívánt Automation-fiókot, majd kattintson a **kész**gombra.

5. Kattintson a **feltétel hozzáadása** elemre a frissítés telepítéséhez megfelelő jel kiválasztásához. Az alábbi táblázat a két elérhető jel részleteit mutatja be.

    |Jel neve|Dimenziók|Leírás
    |---|---|---|
    |`Total Update Deployment Runs`|-Központi telepítési név frissítése<br>– Állapot    |Riasztások egy frissítés központi telepítésének általános állapotáról.|
    |`Total Update Deployment Machine Runs`|-Központi telepítési név frissítése</br>– Állapot</br>– Célszámítógép</br>-A központi telepítés futtatási AZONOSÍTÓjának frissítése    |Adott gépekre irányuló frissítési központi telepítés állapotára vonatkozó riasztások.|

6. Egy dimenzió esetében válasszon ki egy érvényes értéket a listából. Ha a kívánt érték nem szerepel a listában, kattintson a dimenzió elemre, **\+** és írja be az egyéni nevet a következőre:. Ezután válassza ki a keresni kívánt értéket. Ha egy dimenzió összes értékét ki szeretné választani, kattintson a **kiválasztás \* ** gombra. Ha nem választja ki a dimenzió értékét, Update Management figyelmen kívül hagyja ezt a dimenziót.

    ![Jellogika konfigurálása](./media/automation-tutorial-update-management/signal-logic.png)

7. A **riasztási logika**szakaszban adja meg az értékeket az **idő összesítése** és a **küszöbérték** mezőkben, majd kattintson a **kész**gombra.

8. A következő ablaktáblán adja meg a riasztás nevét és leírását.

9. Állítsa be a **Súlyosság** mezőt a **tájékoztatási (2** . szint) értékre a sikeres futtatáshoz vagy **tájékoztatáshoz (az 1.** szinthez) a sikertelen futtatáshoz.

    ![Jellogika konfigurálása](./media/automation-tutorial-update-management/define-alert-details.png)

10. Kattintson az **Igen** vagy a **nem**gombra, attól függően, hogyan szeretné engedélyezni a riasztási szabályt.

11. Ha nem szeretne riasztásokat kapni ehhez a szabályhoz, válassza a **riasztások letiltása**lehetőséget.

## <a name="configure-action-groups-for-your-alerts"></a>Műveleti csoportok konfigurálása a riasztásokhoz

Miután beállította a riasztásokat, beállíthat egy műveleti csoportot, amely a több riasztáson keresztül használható műveletek csoportja. A műveletek tartalmazhatnak e-mailes értesítéseket, runbookok, webhookokat és sok más műveletet. A műveletcsoportokkal kapcsolatban további információt a [műveletcsoportok létrehozásáról és kezeléséről](../azure-monitor/platform/action-groups.md) szóló cikkben talál.

1. Válasszon ki egy riasztást, majd válassza az **új létrehozása** a **műveleti csoportok**alatt lehetőséget. 

2. Adjon meg egy teljes nevet és egy rövid nevet a műveleti csoportnak. A Update Management a rövid nevet használja az értesítések megadott csoport használatával történő küldésekor.

3. A **műveletek**területen adjon meg egy nevet, amely megadja a műveletet, például az **e-mail értesítéseket**. 

4. A **Művelettípus**mezőben válassza ki a megfelelő típust, például **e-mail/SMS/leküldéses/hang**. 

5. Kattintson a **részletek szerkesztése**elemre.

6. Adja meg a művelet típusát a ablaktáblán. Ha például az **e-mailek/SMS/leküldés/hang**lehetőséget használja, adja meg a művelet nevét, jelölje be az **e-mail** jelölőnégyzetet, adjon meg egy érvényes e-mail-címet, majd kattintson **az OK**gombra.

    ![E-mail műveletcsoport konfigurálása](./media/automation-tutorial-update-management/configure-email-action-group.png)

7. A műveleti csoport hozzáadása panelen kattintson **az OK**gombra.

8. Riasztási e-mail esetén testreszabhatja az e-mail tárgyát. Válassza a **műveletek testreszabása** a **szabály létrehozása**alatt lehetőséget, majd válassza az **e-mail tárgy**elemet. 

9. Ha elkészült, kattintson a **riasztási szabály létrehozása**elemre. 

## <a name="schedule-an-update-deployment"></a>Frissítéstelepítés ütemezése

A frissítési központi telepítés ütemezése egy olyan [ütemezési](shared-resources/schedules.md) erőforrást hoz létre, amely a **MicrosoftOMSComputers** runbook van társítva, amely kezeli a frissítés központi telepítését a célszámítógépen. A frissítések telepítéséhez be kell ütemeznie egy központi telepítést, amely a kiadási ütemterv és a szolgáltatás ablakát követi. Kiválaszthatja, hogy milyen frissítési típusok szerepeljenek a központi telepítésben. Például hozzáadhatja a kritikus vagy a biztonsági frissítéseket, és kizárhatja a kumulatív frissítéseket.

>[!NOTE]
>Ha törli az ütemezési erőforrást a Azure Portal vagy a PowerShellt a telepítés létrehozása után, a törlés megszakítja az ütemezett frissítés központi telepítését, és hibaüzenetet jelenít meg, amikor megkísérli újrakonfigurálni az ütemezési erőforrást a portálról. Az ütemezett erőforrást csak a megfelelő központi telepítési ütemterv törlésével törölheti.  

Új frissítés központi telepítésének beütemezett időpontja:

1. Az Automation-fiókban **lépjen az Update Management elemre** **, majd**válassza a **frissítés központi telepítése**lehetőséget.

2. Az **új frissítés telepítése**alatt használja a **név** mezőt, és adjon meg egy egyedi nevet a telepítéshez.

3. Válassza ki a frissítés központi telepítéséhez használni kívánt operációs rendszert.

4. A **groups to Update (előzetes verzió)** régióban adjon meg egy olyan lekérdezést, amely kombinálja az előfizetést, az erőforráscsoportot, a helyszíneket és a címkéket, hogy az üzembe helyezéshez felvenni kívánt Azure-beli virtuális gépek dinamikus csoportjait hozza létre További információ: [dinamikus csoportok használata a Update Management használatával](automation-update-management-groups.md).

5. A **frissítendő gépek** területen válasszon ki egy mentett keresést, egy importált csoportot, vagy válasszon ki **gépeket** a legördülő menüből, és válassza az egyes gépek lehetőséget. Ezzel a beállítással megtekintheti az egyes gépek Log Analytics ügynökének készültségét. A számítógépcsoportok Azure Monitor-naplókban való létrehozásának különböző módszereiről további információt a következő témakörben talál: [számítógépcsoportok Azure monitor-naplókban](../azure-monitor/platform/computer-groups.md).

6. A **frissítési** besorolások régió használatával adhatja meg a termékek [frissítési besorolását](automation-view-update-assessments.md#work-with-update-classifications) . Minden termék esetében törölje az összes támogatott frissítési besorolást, de azokat is, amelyeket bele szeretne foglalni a frissítés telepítésére.

7. A **frissítések belefoglalása/kizárása** régió használatával válassza ki a központi telepítés adott frissítéseit. A belefoglalási/kizárási oldal megjeleníti a frissítéseket a TUDÁSBÁZISCIKK által belefoglalni vagy kizárni kívánt AZONOSÍTÓk száma alapján. 
    
   > [!IMPORTANT]
   > Ne feledje, hogy a kizárások felülbírálják a belefoglalásokat. Ha például meghatároz egy kizárási szabályt `*` , Update Management kizárja az összes javítást vagy csomagot a telepítésből. A kizárt javítások továbbra is hiányzóként jelennek meg a gépeken. Linux rendszerű gépek esetén, ha olyan csomagot tartalmaz, amely egy kizárt függő csomaggal rendelkezik, Update Management nem telepíti a fő csomagot.

   > [!NOTE]
   > Nem adhat meg olyan frissítéseket, amelyek felváltották a frissítés központi telepítésbe való felvételét.

8. Válassza az **időzítési beállítások**lehetőséget. Az alapértelmezett kezdési időpont az aktuális időpontnál 30 perccel későbbi időpont. Bármilyen időpontra beállítható a pillanatnyi időt követő 10. perc után.

9. Az **Ismétlődés** mező segítségével megadhatja, hogy a központi telepítés egyszer történjen-e, vagy ismétlődő ütemezéset használ, majd kattintson **az OK**gombra.

10. A **parancsfájl előtti és utáni parancsfájlok (előzetes verzió)** területen válassza ki azokat a parancsfájlokat, amelyeket a telepítés előtt és után szeretne futtatni. További információ: a [parancsfájlok előtti és utáni parancsfájlok kezelése](pre-post-scripts.md).
    
11. A frissítések telepítésének időtartamát a **karbantartási időszak (perc)** mező segítségével adhatja meg. Karbantartási időszak megadásakor vegye figyelembe a következő részleteket:

    * A karbantartási időszakok azt szabályozzák, hogy a rendszer hány frissítést telepítsen.
    * A Update Management nem állítja le az új frissítések telepítését, ha a karbantartási időszak végére közeledik.
    * A Update Management nem szakítja meg a folyamatban lévő frissítéseket, ha a karbantartási időszak túllépve.
    * Ha a karbantartási időszak túllépi a Windowst, gyakran előfordul, hogy a szervizcsomag frissítése hosszú időt vesz igénybe.

    > [!NOTE]
    > Ha el szeretné kerülni, hogy a frissítések ne legyenek alkalmazva az Ubuntu karbantartási időszakán kívül, konfigurálja újra a `Unattended-Upgrade` csomagot az automatikus frissítések letiltásához. A csomag konfigurálásával kapcsolatos további információkért tekintse meg az [Ubuntu Server útmutatójának automatikus frissítések témakörét](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

12. Az újraindítási **Beállítások** mező segítségével megadhatja, hogyan kezelje az újraindítást az üzembe helyezés során. A következő beállítások érhetők el: 
    * Újraindítás szükség esetén (alapértelmezett)
    * Mindig induljon újra
    * Soha ne induljon újra
    * Csak újraindítás; Ez a beállítás nem telepíti a frissítéseket

    > [!NOTE]
    > Az újraindítás [kezeléséhez használt beállításkulcsok](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) alatt felsorolt beállításkulcsok újraindítási eseményt okozhatnak, ha az újraindítási **Beállítások** úgy vannak beállítva, hogy **Soha ne induljon újra**.

13. Amikor befejezte a központi telepítési ütemterv konfigurálását, kattintson a **Létrehozás**gombra.

    ![A frissítés ütemezési beállításai ablaktábla](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

14. Ekkor visszalép az állapot-irányítópultra. A létrehozott központi telepítési ütemezés megjelenítéséhez válassza az **ütemezett frissítések központi telepítése** lehetőséget.

## <a name="schedule-an-update-deployment-programmatically"></a>Frissítési üzembe helyezés programozott módon történő beütemezett

Ha meg szeretné tudni, hogyan hozhat létre frissítési központi telepítést a REST API, tekintse meg a [szoftverfrissítési konfigurációk – létrehozás](/rest/api/automation/softwareupdateconfigurations/create)című témakört. 

A heti frissítések központi telepítésének létrehozásához minta-runbook is használhat. További információ erről a runbook: [heti frissítési telepítés létrehozása egy vagy több virtuális géphez egy erőforráscsoport esetében](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="check-deployment-status"></a>Központi telepítés állapotának keresése

Az ütemezett telepítés megkezdése után az állapot megjelenik a frissítés **telepítések** lapon az **Update Management**alatt. Ha a telepítés fut, az állapota **Folyamatban** lesz. Az üzembe helyezés sikeres befejeződése után az állapot **sikeresre**változik. Ha a központi telepítés egy vagy több frissítésével kapcsolatban hiba történik, az állapot **részben meghiúsult**.

## <a name="view-results-of-a-completed-update-deployment"></a>Befejezett frissítés telepítésének eredményeinek megtekintése

Az üzembe helyezés befejezésekor kiválaszthatja, hogy megjelenjen-e az irányítópult.

![Adott telepítés frissítéstelepítési állapotának irányítópultja](./media/automation-tutorial-update-management/manageupdates-view-results.png)

A **frissítés eredményei**között az összefoglalás a frissítések és a telepítési eredmények teljes számát adja meg a cél virtuális gépeken. A jobb oldali táblázat a frissítések részletes részletezését és a telepítés eredményét jeleníti meg.

Az elérhető értékek a következők:

* **Nem lett megkísérelve** – a frissítés nem lett telepítve, mert a karbantartási időszak meghatározott időtartama alapján nem volt elegendő idő a rendelkezésére.
* **Nincs kiválasztva** – a frissítés nem lett kijelölve telepítésre.
* **Sikeres** – a frissítés sikeres volt.
* **Sikertelen** – a frissítés sikertelen volt.

A **minden napló** lehetőség kiválasztásával megtekintheti az üzemelő példány által létrehozott összes naplóbejegyzést.

Válassza a **kimenet** lehetőséget, hogy megtekintse a runbook a cél virtuális gépeken történő telepítésének kezeléséért felelős folyamat adatfolyamát.

A telepítés közben felmerülő hibák részletes információinak megtekintéséhez válassza a **Hibák** elemet.

## <a name="view-the-deployment-alert"></a>Az üzembe helyezési riasztás megtekintése

A frissítés telepítésének befejezésekor a telepítés során megadott riasztás jelenik meg. Például itt egy e-mail-üzenet megerősíti a javítást.

![E-mail konfigurálása műveletcsoport](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>További lépések

* Ha a telepítésekkel fejeződött be, tekintse [meg a munkaterületek összekapcsolását az Automation-fiókból a Update Management](automation-unlink-workspace-update-management.md).
* A virtuális gépek Update Managementból való törléséről lásd: [virtuális gépek eltávolítása Update Managementról](automation-remove-vms-from-update-management.md).