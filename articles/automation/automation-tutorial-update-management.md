---
title: Azure-beli virtuális gépek frissítéseinek és javításának kezelése Azure Automation
description: Ez a cikk azt ismerteti, hogyan használható a Update Management az Azure-beli virtuális gépek frissítéseinek és javításának kezeléséhez.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: 5b5172df6ed6993742a08d5ac08cf700681dfc6a
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83829154"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Azure-beli virtuális gépek frissítéseinek és javításának kezelése

Ez a cikk bemutatja, hogyan kezelheti az Azure-beli virtuális gépek frissítéseit és javításait a Azure Automation [Update Management](automation-update-management.md) szolgáltatással. 

A díjszabással kapcsolatos információkért lásd: [Update Management automatizálási díjszabása](https://azure.microsoft.com/pricing/details/automation/).

## <a name="prerequisites"></a>Előfeltételek

* A [Update Management](automation-update-management.md) funkció engedélyezve van egy vagy több virtuális gépen. 
* Update Management számára engedélyezett [virtuális gép](../virtual-machines/windows/quick-create-portal.md) .

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="view-update-assessment"></a>A frissítésfelmérés megtekintése

A Update Management engedélyezése után megnyílik az Update Management lap. Ha a rendszer hiányzóként azonosította a frissítéseket, a hiányzó **frissítések** lapon megjelenik a hiányzó frissítések listája.

Az **információ hivatkozás**alatt válassza a frissítés hivatkozást a frissítés támogatási cikkének megnyitásához. A frissítéssel kapcsolatos fontos információkhoz is tájékozódhat.

![Frissítés állapotának megtekintése](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

A frissítésben kattintson a továbbiak elemre, hogy megnyissa a naplóbeli keresés ablaktáblát a kiválasztott frissítéshez. A naplókeresés lekérdezése az adott frissítéshez előre meg van adva. Módosíthatja ezt a lekérdezést, vagy létrehozhat egy saját lekérdezést a környezetében üzembe helyezett vagy hiányzó frissítések részletes adatainak megtekintéséhez.

![Frissítés állapotának megtekintése](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Riasztások konfigurálása

Ebben a lépésben megtudhatja, hogyan állíthat be egy riasztást, amely lehetővé teszi egy frissítés központi telepítésének állapotát.

### <a name="alert-conditions"></a>A riasztás feltételei

Az Automation-fiókjában lépjen a **riasztások** elemre a **figyelés**területen, majd kattintson az **új riasztási szabály**elemre.

Az Automation-fiókja már ki van választva erőforrásként. Ha módosítani kívánja, kattintson a **kiválasztás**gombra. Az erőforrás kiválasztása lapon válassza az Automation- **fiókok** lehetőséget a **szűrés erőforrás típusa** legördülő menüből. Válassza ki az Automation-fiókját, majd kattintson a **kész**gombra.

Kattintson a **feltétel hozzáadása** elemre a frissítés telepítéséhez megfelelő jel kiválasztásához. Az alábbi táblázat a két elérhető jel részleteit mutatja be.

|Jel neve|Dimenziók|Leírás|
|---|---|---|
|`Total Update Deployment Runs`|-Központi telepítési név frissítése<br>– Állapot|Ez a riasztás a frissítés központi telepítésének általános állapotára mutat.|
|`Total Update Deployment Machine Runs`|-Központi telepítési név frissítése</br>– Állapot</br>– Célszámítógép</br>-A központi telepítés futtatási AZONOSÍTÓjának frissítése|Ez a riasztás egy adott gépekre irányuló frissítés központi telepítésének állapotát jelzi.|

Egy dimenzió esetében válasszon ki egy érvényes értéket a listából. Ha a kívánt érték nem szerepel a listában, kattintson a **\+** dimenzió melletti jelre, és írja be az egyéni nevet. Ezután válassza ki a keresni kívánt értéket. Ha egy dimenzió összes értékét ki szeretné választani, kattintson a **kiválasztás \* ** gombra. Ha nem választja ki egy dimenzió értékét, Update Management figyelmen kívül hagyja ezt a dimenziót.

![Jellogika konfigurálása](./media/automation-tutorial-update-management/signal-logic.png)

A **Riasztási logika** területen a **Küszöbérték** legyen **1**. Ha elkészült, válassza a **Kész** lehetőséget.

### <a name="alert-details"></a>Riasztás részletei

A **2. résznél. Adja meg a riasztás részleteit**, adjon meg egy nevet és egy leírást a riasztáshoz. Állítsa a **Súlyosság** beállítást sikeres futtatás esetén **Tájékoztató (Sev 2)** értékre, sikertelen futtatás esetén pedig **Tájékoztató (Sev 1)** értékre.

![Jellogika konfigurálása](./media/automation-tutorial-update-management/define-alert-details.png)

A **műveleti csoportok**területen válassza az **új létrehozása**lehetőséget. A műveletcsoport műveletek csoportja, amelyeket több riasztáson is alkalmazhat. A műveletek tartalmazhatnak e-mailes értesítéseket, runbookok, webhookokat és sok más műveletet. A műveletcsoportokkal kapcsolatban további információt a [műveletcsoportok létrehozásáról és kezeléséről](../azure-monitor/platform/action-groups.md) szóló cikkben talál.

A **műveleti csoport neve** mezőben adja meg a riasztás nevét és egy rövid nevet. A Update Management egy teljes műveleti csoport neve helyett a rövid nevet használja az értesítések a megadott csoport használatával történő küldésekor.

A **műveletek**területen adja meg a művelet nevét, például az **e-mailes értesítést**. A **Művelettípus mezőben**válassza az **e-mail/SMS/leküldés/hang**lehetőséget. **Részletekért**válassza a **részletek szerkesztése**lehetőséget.

Az E-mail/SMS/Küldés/Hang ablaktáblán adjon meg egy nevet. Jelölje be az **e-mail** jelölőnégyzetet, majd adjon meg egy érvényes e-mail-címet.

![E-mail műveletcsoport konfigurálása](./media/automation-tutorial-update-management/configure-email-action-group.png)

Az E-mail/SMS/leküldés/hang ablaktáblán kattintson az **OK**gombra. A műveleti csoport hozzáadása panelen kattintson **az OK**gombra.

A riasztási e-mail tárgyának testreszabásához a **szabály létrehozása**területen a **műveletek testreszabása**területen válassza az **e-mail tárgy**elemet. Ha végzett, kattintson a **Riasztási szabály létrehozása** gombra. A riasztás azt jelzi, hogy a frissítés telepítése sikeres volt-e, és mely gépek voltak a frissítés központi telepítésének része.

## <a name="schedule-an-update-deployment"></a>Frissítéstelepítés ütemezése

A frissítések telepítéséhez ütemezzen egy központi telepítést, amely a kiadási ütemterv és a szolgáltatás ablakát követi. Kiválaszthatja, hogy milyen frissítési típusok szerepeljenek a központi telepítésben. Például hozzáadhatja a kritikus vagy a biztonsági frissítéseket, és kizárhatja a kumulatív frissítéseket.

>[!NOTE]
>A frissítési központi telepítés ütemezése egy olyan [ütemezési](shared-resources/schedules.md) erőforrást hoz létre, amely a **MicrosoftOMSComputers** runbook van társítva, amely kezeli a frissítés központi telepítését a célszámítógépen. Ha törli az ütemezési erőforrást a Azure Portal vagy a PowerShellt a telepítés létrehozása után, a törlés megszakítja az ütemezett frissítés központi telepítését, és hibaüzenetet jelenít meg, amikor megkísérli újrakonfigurálni az ütemezési erőforrást a portálról. Az ütemezett erőforrást csak a megfelelő központi telepítési ütemterv törlésével törölheti.  

Ütemezzen egy új frissítéstelepítést a virtuális géphez. Ehhez válassza a **Frissítéskezelés**, majd a **Frissítések központi telepítésének ütemezése** elemet.

Az **Új frissítéstelepítés** képernyőn adja meg a következő információkat:

* **Név**: Adjon egyedi nevet a frissítéstelepítésnek.

* **Operációs rendszer**: Válassza ki azt az operációs rendszert, amelyre a frissítéstelepítés vonatkozni fog.

* **Frissítendő csoportok (előzetes verzió)**: olyan lekérdezés definiálása, amely kombinálja az előfizetést, az erőforráscsoportot, a helyszíneket és a címkéket, hogy az üzembe helyezéshez felvenni kívánt Azure-beli virtuális gépek dinamikus csoportjait hozza létre. További információ: [dinamikus csoportok](automation-update-management-groups.md).

* **Frissítendő gépek**: válasszon ki egy mentett keresést, importált csoportot, vagy válasszon ki **gépeket** a legördülő menüből, és válassza az egyes gépek lehetőséget. Ha a **gépek**lehetőséget választja, az egyes gépek készültségi állapota az **Update Agent Readiness (frissítési ügynök készültsége** ) oszlopban látható. A számítógépcsoportok Azure Monitor-naplókban való létrehozásának különböző módszereiről további információt a következő témakörben talál: [számítógépcsoportok Azure monitor-naplókban](../azure-monitor/platform/computer-groups.md).

* **Frissítés besorolása**: minden termék esetében törölje az összes támogatott frissítési besorolást, de azokat is, amelyeket bele szeretne foglalni a frissítés telepítésére. A besorolási típusok leírását lásd: [frissítési besorolások](automation-view-update-assessments.md#work-with-update-classifications).

* **Belefoglalási** /kizárási frissítések – megnyitja a Belefoglalás/kizárás lapot. A felvenni vagy kizárni kívánt frissítések külön lapokon találhatók a TUDÁSBÁZISCIKK AZONOSÍTÓinak számának megadásával. Egy vagy több azonosító számának megadásakor el kell távolítania vagy törölnie kell az összes besorolást a frissítés központi telepítésével. Ezzel biztosíthatja, hogy a frissítési csomagok a frissítési azonosítók megadásakor ne tartalmazzanak más frissítéseket.

> [!NOTE]
> Fontos tudni, hogy a kizárások felülbírálják a belefoglalásokat. Ha például meghatároz egy kizárási szabályt `*` , Update Management nem telepít javításokat vagy csomagokat, ahogy az összes ki van zárva. A kizárt javítások továbbra is hiányzóként jelennek meg a gépről. Linux rendszerű gépek esetén, ha olyan csomagot tartalmaz, amely egy kizárt függő csomaggal rendelkezik, Update Management nem telepíti a fő csomagot.

> [!NOTE]
> Nem adhat meg olyan frissítéseket, amelyeket a rendszer a frissítés központi telepítésére való felvételre váltott ki.
>
* **Ütemezési beállítások**: Megnyitja az Ütemezési beállítások ablaktáblát. Az alapértelmezett kezdési időpont az aktuális időpontnál 30 perccel későbbi időpont. Bármilyen időpontra beállítható a pillanatnyi időt követő 10. perc után.

   Azt is megadhatja, hogy a telepítés egyszer történjen meg, vagy ismétlődő legyen. Az **Ismétlődés** menüpontban válassza az **Egyszer** lehetőséget. Hagyja meg az alapértelmezett értéket 1 nap alatt, majd kattintson **az OK**gombra. Ezek a bejegyzések ismétlődő ütemtervet állítanak be.

* **Előkészítő szkriptek és utólagos szkriptek**: Válassza ki, mely szkripteket szeretné futtatni az üzembe helyezés előtt, illetve után. További információ: [Előkészítő és utólagos szkriptek kezelése](pre-post-scripts.md).

* **Karbantartási időszak (perc)**: Fogadja el az alapértelmezett értéket. A karbantartási időszakok a frissítések telepítésének időtartamát szabályozzák. Karbantartási időszak megadásakor vegye figyelembe a következő részleteket:

  * A karbantartási időszakok azt szabályozzák, hogy a rendszer hány frissítést telepítsen.
  * A Update Management nem állítja le az új frissítések telepítését, ha a karbantartási időszak végére közeledik.
  * A Update Management nem szakítja meg a folyamatban lévő frissítéseket, ha a karbantartási időszak túllépve.
  * Ha a karbantartási időszak túllépi a Windowst, gyakran előfordul, hogy a szervizcsomag frissítése hosszú időt vesz igénybe.

  > [!NOTE]
  > Ha el szeretné kerülni, hogy a frissítések ne legyenek alkalmazva az Ubuntu karbantartási időszakán kívül, konfigurálja újra a felügyelet nélküli frissítési csomagot az automatikus frissítések letiltásához. A csomag konfigurálásával kapcsolatos további információkért lásd [az Ubuntu Server útmutatójának automatikus frissítések témakörét](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

* Újraindítási **Beállítások**: az újraindítások kezelésére szolgáló beállítások megadására használható. A következő beállítások érhetők el:
  * Újraindítás szükség esetén (alapértelmezett)
  * Mindig induljon újra
  * Soha ne induljon újra
  * Csak újraindítás – nem telepíti a frissítéseket

> [!NOTE]
> Az újraindítás [kezeléséhez használt beállításkulcsok](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) alatt felsorolt beállításkulcsok újraindítási eseményt okozhatnak, ha az újraindítási **Beállítások** úgy vannak beállítva, hogy **Soha ne induljon újra**.

Ha elkészült az ütemterv konfigurálásával, kattintson a **Létrehozás**gombra.

![A frissítés ütemezési beállításai ablaktábla](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Ekkor visszalép az állapot-irányítópultra. Válassza az **ütemezett frissítések központi telepítése** lehetőséget az imént létrehozott központi telepítési ütemezés megjelenítéséhez.

> [!NOTE]
> Update Management támogatja az első féltől származó frissítések telepítését és a javítások előzetes letöltését. Ehhez a támogatáshoz módosításokat kell elvégezni a javított rendszereken. Tekintse meg az [első fél és a letöltés előtti támogatást](automation-configure-windows-update.md) , amelyből megtudhatja, hogyan konfigurálhatja ezeket a beállításokat a rendszereken.

A frissítési központi telepítéseket programozott módon is létrehozhatja. Ha meg szeretné tudni, hogyan hozhat létre frissítési központi telepítést a REST API, tekintse meg a [szoftverfrissítési konfigurációk – létrehozás](/rest/api/automation/softwareupdateconfigurations/create)című témakört. Létezik egy minta runbook is, amely használatával hetente frissítheti az üzembe helyezést. További információ erről a runbook: [heti frissítési telepítés létrehozása egy vagy több virtuális géphez egy erőforráscsoport esetében](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

## <a name="view-results-of-an-update-deployment"></a>Frissítéstelepítés eredményeinek megtekintése

Miután az ütemezett telepítés elindult, a **Frissítéskezelés** panel **Frissítéstelepítések** lapján láthatóvá válik a telepítés állapota. Ha a telepítés fut, az állapota **Folyamatban** lesz. Az üzembe helyezés sikeres befejeződése után az állapot **sikeresre**változik. Ha a központi telepítés egy vagy több frissítésével kapcsolatban hiba történik, az állapot **részben meghiúsult**.

Válassza ki a befejezett frissítés központi telepítését az irányítópult megtekintéséhez.

![Adott telepítés frissítéstelepítési állapotának irányítópultja](./media/automation-tutorial-update-management/manageupdates-view-results.png)

A **Frissítés eredményei** terület összesíti a frissítések teljes számát és az adott virtuális gépre vonatkozó telepítési eredményeket. A jobb oldali táblázat az egyes frissítések részletes áttekintését és a telepítés eredményét tartalmazza.

Az elérhető értékek a következők:

* **Nem történt kísérlet**: a frissítés nem lett telepítve, mert a karbantartási időszak meghatározott időtartama alapján nem volt elegendő idő a rendelkezésére.
* **Sikeres**: a frissítés sikeresen befejeződött.
* **Sikertelen**: a frissítés nem sikerült.

A **minden napló** lehetőség kiválasztásával megtekintheti az üzemelő példány által létrehozott összes naplóbejegyzést.

Válassza a **Kimenet** lehetőséget azon runbook feladatstreamjének megtekintéséhez, amely a frissítések telepítését kezeli a virtuális célgépen.

A telepítés közben felmerülő hibák részletes információinak megtekintéséhez válassza a **Hibák** elemet.

A frissítés sikeres üzembe helyezése után egy, az alábbihoz hasonló e-mailt kap:

![E-mail konfigurálása műveletcsoport](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>További lépések

* A Update Management részleteiért lásd: [Update Management áttekintése](automation-update-management.md).