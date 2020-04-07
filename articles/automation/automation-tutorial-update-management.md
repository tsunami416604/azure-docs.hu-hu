---
title: Az Azure-beli virtuális gépek frissítéseinek és javításainak kezelése
description: Ez a cikk áttekintést nyújt arról, hogyan használhatja az Azure Automation Update Management et az Azure és a nem Azure-beli virtuális gépek frissítéseinek és javításainak kezeléséhez.
services: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: 888dc99162551482afc715f1a793614d2c866384
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677036"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Az Azure-beli virtuális gépek frissítéseinek és javításainak kezelése

A virtuális gépek frissítéseit és javításait az Update Management megoldás segítségével kezelheti. Ez az oktatóanyag azt ismerteti, hogyan mérheti fel legkönnyebben az elérhető frissítések állapotát, ütemezheti a szükséges frissítések telepítését, tekintheti át a telepítési eredményeket, és állíthat be riasztásokat, hogy ellenőrizze, sikeres volt-e a frissítések telepítése.

Az árképzéssel kapcsolatos információkért lásd: [Automation árképzési frissítés-kezelés](https://azure.microsoft.com/pricing/details/automation/).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Frissítésfelmérés megtekintése
> * Riasztások konfigurálása
> * Frissítéstelepítés ütemezése
> * Telepítés eredményeinek megtekintése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Az [Update Management](automation-update-management.md) megoldás engedélyezve van egy vagy több virtuális géphez.
* A szolgáltatásba felvenni kívánt [virtuális gép](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="view-update-assessment"></a>A frissítésfelmérés megtekintése

Az Update Management engedélyezése után megnyílik a **Frissítéskezelés** lap. Ha a rendszer hiányzóként azonosítja a frissítéseket, a hiányzó frissítések listája megjelenik a **Hiányzó frissítések** lapon.

Az **Információ hivatkozás csoportban**válassza a frissítési hivatkozást a frissítés támogatási cikkének megnyitásához. A frissítéssel kapcsolatos fontos információkat ismerthet.

![Frissítés állapotának megtekintése](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Kattintson a frissítés tetszőleges pontjára a kijelölt frissítés **Naplókeresési** ablaktáblájának megnyitásához. A naplókeresés lekérdezése az adott frissítéshez előre meg van adva. Módosíthatja ezt a lekérdezést, vagy létrehozhat saját lekérdezést a környezetben telepített vagy hiányzó frissítésekrészletes adatainak megtekintéséhez.

![Frissítés állapotának megtekintése](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Riasztások konfigurálása

Ebben a lépésben megtudhatja, hogy állítson be egy riasztást, amely tájékoztatja a frissítés központi telepítésének állapotáról.

### <a name="alert-conditions"></a>A riasztás feltételei

Az Automation-fiókban nyissa meg **a Figyelés** **csoport**riasztások , majd kattintson az **Új riasztási szabály**elemre .

Az Automation-fiók már ki van jelölve erőforrásként. Ha módosítani szeretné, kattintson a **Kijelölés gombra.** Az **Erőforrás kiválasztása** lapon válassza az **Erőforrástípus** szerint legördülő menü **Automation-fiókok** parancsát. Válassza ki az Automation-fiókot, majd kattintson **a Kész gombra.**

Kattintson a **Feltétel hozzáadása** gombra a frissítés központi telepítéséhez megfelelő jel kiválasztásához. Az alábbi táblázat a két rendelkezésre álló jel részleteit mutatja be.

|Jel neve|Dimenziók|Leírás|
|---|---|---|
|`Total Update Deployment Runs`|- Frissítés központi telepítési neve<br>- Státusz|Ez a jelzés a frissítés központi telepítésének általános állapotára figyelmeztet.|
|`Total Update Deployment Machine Runs`|- Frissítés központi telepítési neve</br>- Státusz</br>- Cél számítógép</br>- Update Deployment run id|Ez a jelzés riasztást a frissítés központi telepítésének adott gépekre irányuló állapotára vonatkozóan.|

Dimenzióhoz válasszon ki egy érvényes értéket a listából. Ha a kívánt érték nem szerepel a **\+** listában, kattintson a dimenzió melletti jelre, és írja be az egyéni nevet. Ezután válassza ki a keresett értéket. Ha egy dimenzió összes értékét ki szeretné jelölni, kattintson a **Kijelölés \* ** gombra. Ha nem választ értéket egy dimenzióhoz, az Update Management figyelmen kívül hagyja az adott dimenziót.

![Jellogika konfigurálása](./media/automation-tutorial-update-management/signal-logic.png)

A **Riasztási logika** területen a **Küszöbérték** legyen **1**. Ha elkészült, válassza a **Kész** lehetőséget.

### <a name="alert-details"></a>Riasztás részletei

**2 alatt. Adja meg a riasztás részleteit,** adja meg a riasztás nevét és leírását. Állítsa a **Súlyosság** beállítást sikeres futtatás esetén **Tájékoztató (Sev 2)** értékre, sikertelen futtatás esetén pedig **Tájékoztató (Sev 1)** értékre.

![Jellogika konfigurálása](./media/automation-tutorial-update-management/define-alert-details.png)

A **Műveletcsoportok csoportban**válassza **az Új létrehozása lehetőséget.** A műveletcsoport műveletek csoportja, amelyeket több riasztáson is alkalmazhat. A műveletek közé tartozhatnak az e-mail értesítések, runbookok, webhookok és még sok más. A műveletcsoportokkal kapcsolatban további információt a [műveletcsoportok létrehozásáról és kezeléséről](../azure-monitor/platform/action-groups.md) szóló cikkben talál.

A **Műveletcsoport neve** mezőben adja meg a riasztás nevét és egy rövid nevet. Az Update Management a teljes műveletcsoport neve helyett a rövid nevet használja, amikor a megadott csoport használatával küld értesítéseket.

A **Műveletek csoportban**adja meg a művelet nevét, **például az E-mail értesítés .** A **Művelettípus mezőben**válassza **az E-mail/SMS/Push/Voice**lehetőséget. A **Részletek lehetőséget**a Részletek **szerkesztése lehetőségkiválasztásában**válassza a Részletek szerkesztése lehetőségért.

Az **E-mail/SMS/Küldés/Hang** ablaktáblán adjon meg egy nevet. Jelölje be az **E-mail** jelölőnégyzetet, és adjon meg egy érvényes e-mail-címet.

![E-mail műveletcsoport konfigurálása](./media/automation-tutorial-update-management/configure-email-action-group.png)

Az **E-mail/SMS/Push/Voice** ablaktáblán kattintson az **OK**gombra. A **Műveletcsoport hozzáadása** ablaktáblán kattintson az **OK**gombra.

A figyelmeztető e-mail tárgyának testreszabásához a **Szabály létrehozása**csoport **Műveletek testreszabása**csoportban válassza az **E-mail tárgy a lehetőséget.** Ha végzett, kattintson a **Riasztási szabály létrehozása** gombra. A riasztás jelzi, ha egy frissítés központi telepítése sikeres, és mely gépek voltak részei a frissítés központi telepítésének futtatásához.

## <a name="schedule-an-update-deployment"></a>Frissítéstelepítés ütemezése

Következő lépésként ütemezzen egy olyan telepítést a frissítésekhez, amely megfelel a kiadási ütemtervnek és a szolgáltatási időkeretnek. Kiválaszthatja a központi telepítésben szerepelni kívánt frissítéstípusokat. Például hozzáadhatja a kritikus vagy a biztonsági frissítéseket, és kizárhatja a kumulatív frissítéseket.

>[!NOTE]
>A frissítés központi telepítésének ütemezése létrehoz egy [ütemezési](shared-resources/schedules.md) erőforrást, amely a **Patch-MicrosoftOMSComputers** runbookhoz kapcsolódik, amely kezeli a frissítés központi telepítését a célgépeken. Ha törli az ütemezési erőforrást az Azure Portalról, vagy a Központi telepítés létrehozása után használja a PowerShellt, a törlés megszakítja az ütemezett frissítés központi telepítését, és hibát észlel, amikor megpróbálja újrakonfigurálni az ütemezési erőforrást a portálról. Az ütemezési erőforrás csak a megfelelő telepítési ütemezés törlésével törölhető.  

Ütemezzen egy új frissítéstelepítést a virtuális géphez. Ehhez válassza a **Frissítéskezelés**, majd a **Frissítések központi telepítésének ütemezése** elemet.

Az **Új frissítéstelepítés** képernyőn adja meg a következő információkat:

* **Név**: Adjon egyedi nevet a frissítéstelepítésnek.

* **Operációs rendszer**: Válassza ki azt az operációs rendszert, amelyre a frissítéstelepítés vonatkozni fog.

* **Frissítandó csoportok (előzetes verzió)**: Olyan lekérdezést határozhat meg, amely előfizetést, erőforráscsoportokat, helyeket és címkéket egyesít az Azure-beli virtuális gépek dinamikus csoportjának létrehozásához, amelyet a központi telepítésbe foglalhat. További információ: [Dinamikus csoportok](automation-update-management-groups.md).

* **Frissítandó gépek**: Válasszon egy mentett keresést, importált csoportot, vagy válassza a **Gépek** lehetőséget a legördülő menüből, és válassza ki az egyes gépeket. Ha a **Gépek**lehetőséget választja, az egyes gépek készenléte az **Ügynök készültségi frissítése** oszlopban jelenik meg. Ha többet szeretne megtudni a számítógépcsoportok létrehozásának különböző módszereiről az Azure Monitor naplóiban, olvassa [el a Számítógépcsoportok az Azure Monitor naplóiban című témakört.](../azure-monitor/platform/computer-groups.md)

* **Besorolás frissítése:** Minden egyes termék esetében törölje az összes támogatott frissítési besorolás kijelölését, de azokat, amelyeket a frissítés központi telepítésébe fel szeretne felvenni. Ebben az oktatóanyagban hagyja az összes típust az összes termékhez kiválasztva.

  A választható besorolási típusok a következők:

   |Operációs rendszer  |Típus  |
   |---------|---------|
   |Windows     | Kritikus frissítések</br>Biztonsági frissítések</br>Kumulatív frissítések</br>Funkciócsomagok</br>Szervizcsomagok</br>Definíciófrissítések</br>Eszközök</br>Frissítések<br>Illesztőprogram        |
   |Linux     | Kritikus vagy biztonsági frissítések</br>Egyéb frissítések       |

   A besorolási típusok leírását lásd: [Besorolások frissítése](automation-view-update-assessments.md#update-classifications).

* **A belefoglaláshoz/kizáráshoz szükséges frissítések** – Megnyitja a Belefoglalás/kizárás lapot. A beveendő vagy kizárandó frissítések külön lapokon találhatók a tudásbáziscikk-azonosítószámok megadásával. Egy vagy több azonosítószám megadásakor el kell távolítania, vagy vissza kell vonnia az összes besorolás t a frissítés központi telepítésével. Ez biztosítja, hogy a frissítési azonosítók megadásakor a frissítési csomag ne tartalmazzon más frissítéseket.

> [!NOTE]
> Fontos tudni, hogy a kizárások felülbírálják a zárványokat. Ha például a kizárási `*`szabályt a , Update Management telepíti nem javítások vagy csomagok, mivel ezek mindegyike ki van zárva. A kizárt javítások továbbra is hiányzóként jelennek meg a gépből. Linux gépek esetén, ha olyan csomagot tartalmaz, amely egy kizárt függő csomagot tartalmaz, az Update Management nem telepíti a fő csomagot.

> [!NOTE]
> Nem adhat meg olyan frissítéseket, amelyek et felülítettek a frissítés központi telepítésével való felvételhez.
>

* **Ütemezési beállítások**: Megnyitja az **Ütemezési beállítások** ablaktáblát. Az alapértelmezett kezdési időpont az aktuális időpontnál 30 perccel későbbi időpont. Bármilyen időpontra beállítható a pillanatnyi időt követő 10. perc után.

   Azt is megadhatja, hogy a telepítés egyszer történjen meg, vagy ismétlődő legyen. Az **Ismétlődés** menüpontban válassza az **Egyszer** lehetőséget. Hagyja az alapértelmezett értéket 1 napként, majd kattintson az **OK**gombra. Ezek a tételek ismétlődő ütemezést állítanak be.

* **Előkészítő szkriptek és utólagos szkriptek**: Válassza ki, mely szkripteket szeretné futtatni az üzembe helyezés előtt, illetve után. További információ: [Előkészítő és utólagos szkriptek kezelése](pre-post-scripts.md).

* **Karbantartási időszak (perc)**: Fogadja el az alapértelmezett értéket. A karbantartási ablakok szabályozzák a frissítések telepítéséhez szükséges időt. A karbantartási időszak megadásakor vegye figyelembe a következő részleteket:

  * A karbantartási ablakok szabályozzák, hogy hány frissítés van telepítve.
  * Az Update Management nem állítja le az új frissítések telepítését, ha a karbantartási időszak vége közeledik.
  * Az Update Management nem állítja le a folyamatban lévő frissítéseket, ha túllépi a karbantartási időszakot.
  * Ha a Windows rendszerben túllépi a karbantartási időszakot, annak gyakran az az oka, hogy a szervizcsomag frissítése telepítése hosszú időt vesz igénybe.

  > [!NOTE]
  > Annak elkerülése érdekében, hogy a frissítések az Ubuntu karbantartási időszakán kívül kerüljenek alkalmazásra, konfigurálja újra a Felügyelet nélküli frissítés csomagot az automatikus frissítések letiltásához. A csomag konfigurálásáról az [Ubuntu Server Guide Automatikus frissítések című témakörében olvashat bővebben.](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)

* **Újraindítási beállítások**: Az újraindítások kezelésének beállításait adhatja meg. A következő beállítások érhetők el:
  * Újraindítás, ha szükséges (alapértelmezett)
  * Mindig induljon újra
  * Soha ne induljon újra
  * Csak újraindítás - nem telepíti a frissítéseket

> [!NOTE]
> Az újraindítás kezeléséhez [használt beállításkulcsok](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) alatt felsorolt beállításkulcsok újraindítást okozhatnak, ha az **újraindítási beállítások** soha nem **újraindulnak**.

Ha befejezte az ütemezés konfigurálását, kattintson a **Létrehozás gombra.**

![A frissítés ütemezési beállításai ablaktábla](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Ekkor visszalép az állapot-irányítópultra. Válassza **az Ütemezett frissítés központi telepítések** lehetőséget a most létrehozott telepítési ütemezés megjelenítéséhez.

> [!NOTE]
> Az Update Management támogatja a külső frissítések telepítését és a javítások letöltését. Ez a támogatás a javítás alatt lévő rendszerek en módosításokat igényel. A [Rendszerint és a letöltés előtti támogatás](automation-configure-windows-update.md) című témakörből megtudhatja, hogyan konfigurálhatja ezeket a beállításokat a rendszereken.

Programozott módon is létrehozhat frissítési központi telepítéseket. Ha tudni szeretné, hogyan hozhat létre frissítési központi telepítést a REST API-val, olvassa el a [Szoftverfrissítési konfigurációk – Létrehozás című témakört.](/rest/api/automation/softwareupdateconfigurations/create) Van is egy minta runbook, amely segítségével hozzon létre egy heti frissítés központi telepítése. A runbookról további információ: [Heti frissítési központi telepítés létrehozása egy erőforráscsoport egy vagy több virtuális gépéhez.](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)

## <a name="view-results-of-an-update-deployment"></a>Frissítéstelepítés eredményeinek megtekintése

Miután az ütemezett telepítés elindult, a **Frissítéskezelés** panel **Frissítéstelepítések** lapján láthatóvá válik a telepítés állapota. Ha a telepítés fut, az állapota **Folyamatban** lesz. Ha a központi telepítés sikeresen befejeződik, az állapot **sikeresre változik.** Ha a központi telepítésegy vagy több frissítésével hibák vannak, az állapot **részben sikertelen.**

Válassza ki a befejezett frissítés központi telepítését az irányítópult megtekintéséhez.

![Adott telepítés frissítéstelepítési állapotának irányítópultja](./media/automation-tutorial-update-management/manageupdates-view-results.png)

A **Frissítés eredményei** terület összesíti a frissítések teljes számát és az adott virtuális gépre vonatkozó telepítési eredményeket. A jobb oldali táblázat az egyes frissítések részletes áttekintését és a telepítés eredményét tartalmazza.

A rendelkezésre álló értékek a következők:

* **Nem kísérlet :** A frissítés nem lett telepítve, mert nem volt elegendő idő a megadott karbantartási időszak időtartama alapján.
* **Sikeres :** A frissítés sikerült.
* **Nem sikerült**: A frissítés nem sikerült.

Válassza az **Összes napló lehetőséget** a központi telepítés által létrehozott összes naplóbejegyzés megtekintéséhez.

Válassza a **Kimenet** lehetőséget azon runbook feladatstreamjének megtekintéséhez, amely a frissítések telepítését kezeli a virtuális célgépen.

A telepítés közben felmerülő hibák részletes információinak megtekintéséhez válassza a **Hibák** elemet.

Ha a frissítés telepítése sikeres, az alábbihoz hasonló megerősítő e-mailt kap:

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
> [Az Update Management megoldás](automation-update-management.md)