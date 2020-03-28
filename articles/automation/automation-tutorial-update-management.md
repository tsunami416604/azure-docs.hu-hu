---
title: Az Azure-beli virtuális gépek frissítéseinek és javításainak kezelése
description: Ez a cikk áttekintést nyújt arról, hogyan használhatja az Azure Automation Update Management et az Azure és a nem Azure-beli virtuális gépek frissítéseinek és javításainak kezeléséhez.
services: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 03/04/2020
ms.custom: mvc
ms.openlocfilehash: f7130f3289ce42ca1481ec14be893c846c9ed55b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80335788"
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

Az Update Management engedélyezése után megnyílik az **Update Management** ablaktábla. Ha a rendszer hiányzóként azonosítja a frissítéseket, a hiányzó frissítések listája megjelenik a **Hiányzó frissítések** lapon.

Az **Információ hivatkozás csoportban**válassza a frissítési hivatkozást a frissítés támogatási cikkének megnyitásához. A frissítéssel kapcsolatos fontos információkat ismerthet.

![Frissítés állapotának megtekintése](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

Kattintson a frissítés tetszőleges pontjára a kijelölt frissítés **Naplókeresési** ablaktáblájának megnyitásához. A naplókeresés lekérdezése az adott frissítéshez előre meg van adva. Ezt a lekérdezést módosíthatja, vagy létrehozhatja saját lekérdezését, amellyel részletes információkat jeleníthet meg a környezetben telepített vagy a hiányzó frissítésekre vonatkozóan.

![Frissítés állapotának megtekintése](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Riasztások konfigurálása

Ebben a lépésben megtudhatja, hogy állítson be egy riasztást, amely tájékoztatja a frissítés központi telepítésének állapotáról.

### <a name="alert-conditions"></a>A riasztás feltételei

Az Automation-fiókfigyelés csoportban válassza a **Figyelés** , majd a + Új riasztási szabály (Figyelés) csoportban a **Figyelés (Figyelés) menüt,** majd kattintson **a + Új riasztási szabály**elemre.

Az Automation-fiók már ki van jelölve erőforrásként. Ha módosítani szeretné, kattintson a **Kijelölés** gombra, és az **Erőforrás kiválasztása** lapon válassza az **Automatizálási fiókok** lehetőséget a Szűrés **erőforrástípus szerint** legördülő menüben. Válassza ki az Automation-fiókját, majd kattintson a **Kész** gombra.

Kattintson a **Feltétel hozzáadása** gombra a frissítés központi telepítéséhez megfelelő jel kiválasztásához. Az alábbi táblázat a frissítési központi telepítések hez rendelkezésre álló két jel részleteit mutatja be:

|Jel neve|Dimenziók|Leírás|
|---|---|---|
|**A frissítés telepítési futásai összesen**|- Frissítés központi telepítési neve</br>- Státusz|Ez a jel a frissítések központi telepítésének általános állapotára vonatkozó riasztásra szolgál.|
|**A frissítési központi telepítési gép teljes futtatása**|- Frissítés központi telepítési neve</br>- Státusz</br>- Cél számítógép</br>- Update Deployment run id|Ez a jel az adott gépekre irányuló frissítési telepítés állapotának riasztására szolgál|

A dimenzióértékekhez válasszon ki egy érvényes értéket a listából. Ha a keresett érték nem szerepel a listában, kattintson a **\+** dimenzió melletti jelre, és írja be az egyéni nevet. Ezután kiválaszthatja a keresett értéket. Ha egy dimenzió összes értékét ki szeretné jelölni, kattintson a **Kijelölés \* ** gombra. Ha nem választ értéket egy dimenzióhoz, a program figyelmen kívül hagyja a dimenziót az értékelés során.

![Jellogika konfigurálása](./media/automation-tutorial-update-management/signal-logic.png)

A **Riasztási logika** területen a **Küszöbérték** legyen **1**. Ha elkészült, válassza a **Kész** lehetőséget.

### <a name="alert-details"></a>Riasztás részletei

**2 alatt. Adja meg a riasztás részleteit,** adja meg a riasztás nevét és leírását. Állítsa a **Súlyosság** beállítást sikeres futtatás esetén **Tájékoztató (Sev 2)** értékre, sikertelen futtatás esetén pedig **Tájékoztató (Sev 1)** értékre.

![Jellogika konfigurálása](./media/automation-tutorial-update-management/define-alert-details.png)

A **Műveletcsoportok csoportban**válassza **az Új létrehozása lehetőséget.** A műveletcsoport műveletek csoportja, amelyeket több riasztáson is alkalmazhat. Ezek a műveletek a teljesség igénye nélkül a következők lehetnek: e-mail-értesítések, runbookok, webhookok stb. A műveletcsoportokkal kapcsolatban további információt a [műveletcsoportok létrehozásáról és kezeléséről](../azure-monitor/platform/action-groups.md) szóló cikkben talál.

A **Műveletcsoport neve** mezőben adja meg a riasztás nevét és egy rövid nevet. A rendszer a rövid nevet használja a műveletcsoport teljes neve helyett, amikor értesítéseket küld a csoport használatával.

A **Műveletek csoportban**adja meg a művelet nevét, **például az E-mail értesítések**et. A **Művelettípusa csoportban**válassza **az E-mail/SMS/Push/Voice**lehetőséget. A **Részletek csoportban**válassza **a Részletek szerkesztése**lehetőséget.

Az **E-mail/SMS/Küldés/Hang** ablaktáblán adjon meg egy nevet. Jelölje be az **E-mail** jelölőnégyzetet, és adjon meg egy érvényes e-mail-címet.

![E-mail műveletcsoport konfigurálása](./media/automation-tutorial-update-management/configure-email-action-group.png)

Az **E-mail/SMS/Push/Voice** ablaktáblán válassza az **Ok lehetőséget.** A **Műveletcsoport hozzáadása** ablaktáblán válassza az **Ok lehetőséget.**

A figyelmeztető e-mail tárgyának testreszabásához a **Szabály létrehozása**csoport **Műveletek testreszabása**csoportban válassza az **E-mail tárgy a lehetőséget.** Ha végzett, kattintson a **Riasztási szabály létrehozása** gombra. A riasztás figyelmezteti, ha egy frissítés telepítése sikeresen befejeződött, és tájékoztat róla, hogy mely számítógépeket érintett az adott frissítéstelepítés.

## <a name="schedule-an-update-deployment"></a>Frissítéstelepítés ütemezése

Következő lépésként ütemezzen egy olyan telepítést a frissítésekhez, amely megfelel a kiadási ütemtervnek és a szolgáltatási időkeretnek. Kiválaszthatja, hogy a telepítés milyen típusú frissítéseket tartalmazzon. Például hozzáadhatja a kritikus vagy a biztonsági frissítéseket, és kizárhatja a kumulatív frissítéseket.

>[!NOTE]
>Frissítési központi telepítés ütemezésekor létrehoz egy [ütemezési](shared-resources/schedules.md) erőforrást a **Patch-MicrosoftOMSComputers** runbookhoz kapcsolódóan, amely kezeli a frissítés központi telepítését a célgépeken. Ha törli az ütemezési erőforrást az Azure Portalról, vagy a Központi telepítés létrehozása után használja a PowerShellt, megszakítja az ütemezett frissítés központi telepítését, és hibát észlel, amikor megpróbálja újrakonfigurálni a portálról. Az ütemezési erőforrás csak a megfelelő telepítési ütemezés törlésével törölhető.  
>

Ütemezzen egy új frissítéstelepítést a virtuális géphez. Ehhez válassza a **Frissítéskezelés**, majd a **Frissítések központi telepítésének ütemezése** elemet.

Az **Új frissítéstelepítés** képernyőn adja meg a következő információkat:

* **Név**: Adjon egyedi nevet a frissítéstelepítésnek.

* **Operációs rendszer**: Válassza ki azt az operációs rendszert, amelyre a frissítéstelepítés vonatkozni fog.

* **Frissítendő csoportok (előzetes verzió)**: Meghatározhat egy előfizetéseken, erőforráscsoportokon, helyeken és címkéken alapuló lekérdezést, amellyel egy dinamikus, Azure-beli virtuális gépekből álló csoportot hozhat létre, majd belefoglalhatja a telepítésbe. További információ: [Dinamikus csoportok](automation-update-management-groups.md)

* **Frissítendő gépek**: Válasszon ki egy Mentett keresést vagy Importált csoportot, vagy válassza a legördülő listában a Gép lehetőséget, és válasszon ki egyes gépeket. Ha a Gépek lehetőséget **választja,** a gép készenléte a **Frissítési ügynök készenléte** oszlopban jelenik meg. A számítógépcsoportok létrehozásának különböző módszereiről az Azure Monitor naplóiban a [Számítógépcsoportok az Azure Monitor naplóiban című témakörben olvashat.](../azure-monitor/platform/computer-groups.md)

* **Frissítési besorolás:** Válassza ki a támogatott frissítési besorolások elérhető minden termék, amely szerepel a frissítés központi telepítését. Ebben az oktatóanyagban hagyjon minden típust kiválasztva.

  A választható besorolási típusok a következők:

   |Operációs rendszer  |Típus  |
   |---------|---------|
   |Windows     | Kritikus frissítések</br>Biztonsági frissítések</br>Kumulatív frissítések</br>Funkciócsomagok</br>Szervizcsomagok</br>Definíciófrissítések</br>Eszközök</br>Frissítések        |
   |Linux     | Kritikus vagy biztonsági frissítések</br>Egyéb frissítések       |

   A besorolási típusok ismertetését [a frissítések besorolását](automation-view-update-assessments.md#update-classifications) leíró szakaszban találja.

* **Belefoglalandó/kizárandó frissítések** – Ez megnyitja a **Belefoglalás/kizárás** lapot. A belefoglalandó vagy kizárandó frissítések külön lapokon jelennek meg.

> [!NOTE]
> Fontos tudni, hogy a kizárások felülbírálják a zárványokat. Ha például a kizárási `*`szabályt a, akkor nem javítások vagy csomagok vannak telepítve, mivel azok mind ki vannak zárva. A kizárt javítások továbbra is hiányzóként jelennek meg a gépből. Linux gépek, ha egy csomag szerepel, de van egy függő csomagot, amely kizárt, a csomag nincs telepítve.

> [!NOTE]
> Nem adhat meg olyan frissítéseket, amelyek et felülítettek a frissítés központi telepítésével való felvételhez.
>

* **Ütemezési beállítások**: Megnyitja az **Ütemezési beállítások** ablaktáblát. Az alapértelmezett kezdési időpont az aktuális időpontnál 30 perccel későbbi időpont. Bármilyen időpontra beállítható a pillanatnyi időt követő 10. perc után.

   Azt is megadhatja, hogy a telepítés egyszer történjen meg, vagy ismétlődő legyen. Az **Ismétlődés** menüpontban válassza az **Egyszer** lehetőséget. Hagyja az alapértelmezett értéket 1 napként, és válassza **az Ok gombot.** Ezzel egy ismétlődő ütemezést állít be.

* **Előkészítő szkriptek és utólagos szkriptek**: Válassza ki, mely szkripteket szeretné futtatni az üzembe helyezés előtt, illetve után. További információ: [Előkészítő és utólagos szkriptek kezelése](pre-post-scripts.md).

* **Karbantartási időszak (perc)**: Fogadja el az alapértelmezett értéket. A karbantartási ablakok szabályozzák a frissítések telepítéséhez szükséges időt. A karbantartási időszak megadásakor vegye figyelembe a következő részleteket.

  * A karbantartási ablakok szabályozzák, hogy hány frissítést kísérelt meg a telepítés.
  * Az Update Management nem állítja le az új frissítések telepítését, ha a karbantartási időszak vége közeledik.
  * Az Update Management nem állítja le a folyamatban lévő frissítéseket, ha túllépi a karbantartási időszakot.
  * Ha a Windows rendszerben túllépi a karbantartási időszakot, annak gyakran az az oka, hogy a szervizcsomag frissítése telepítése hosszú időt vesz igénybe.

  > [!NOTE]
  > Annak elkerülése érdekében, hogy a frissítések az Ubuntu karbantartási időszakán kívül kerüljenek alkalmazásra, konfigurálja újra a Felügyelet nélküli frissítés csomagot az automatikus frissítések letiltásához. A csomag konfigurálásáról az [Ubuntu Server Guide Automatikus frissítések című témakörében olvashat bővebben.](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)

* **Újraindítási beállítások**: Ez a beállítás határozza meg az újraindítások kezelésének módját. Az elérhető lehetőségek:
  * Újraindítás szükség esetén (alapértelmezett beállítás)
  * Mindig induljon újra
  * Soha ne induljon újra
  * Csak újraindítás – frissítések nem lesznek telepítve

> [!NOTE]
> Az újraindítás kezeléséhez [használt beállításkulcsok](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) alatt felsorolt beállításkulcsok újraindítást okozhatnak, ha az **Újraindítás vezérlő** **beállítása Soha újraindítás**.

Ha végzett az ütemezés beállításával, kattintson a **Létrehozás** gombra.

![A frissítés ütemezési beállításai ablaktábla](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Ekkor visszalép az állapot-irányítópultra. Kattintson az **Ütemezett frissítéstelepítések** lehetőségre a létrehozott telepítési ütemezés megjelenítéséhez.

> [!NOTE]
> Az Update Management a belső frissítések üzembe helyezését és a javítócsomagok előzetes letöltését támogatja. Ehhez módosításokat kell végrehajtani a javítani kívánt rendszereken. A [belső és előre letöltött frissítéssel](automation-configure-windows-update.md) foglalkozó részben tudhatja meg, hogyan konfigurálja ezeket a beállításokat a rendszereken.

**A központi telepítések frissítése** programozott módon is létrehozható. Ha tudni szeretné, hogyan hozhat létre **frissítési központi telepítést** a REST API-val, olvassa el a [Szoftverfrissítési konfigurációk – Létrehozás című témakört.](/rest/api/automation/softwareupdateconfigurations/create) Van egy minta runbook is, amely heti **frissítési központi telepítés**létrehozásához használható. A runbookról további információ: [Heti frissítési központi telepítés létrehozása egy erőforráscsoport egy vagy több virtuális gépéhez.](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)

## <a name="view-results-of-an-update-deployment"></a>Frissítéstelepítés eredményeinek megtekintése

Miután az ütemezett telepítés elindult, a **Frissítéskezelés** panel **Frissítéstelepítések** lapján láthatóvá válik a telepítés állapota. Ha a telepítés fut, az állapota **Folyamatban** lesz. A telepítés befejezése után, ha az sikeresnek bizonyult, **Sikeres** állapotúra változik. Ha a telepítésben lévő frissítések közül egy vagy több meghiúsul, az állapota **Részben sikertelen**.

Ha kiválasztja a befejezett frissítéstelepítést, megjelenik az adott frissítéstelepítés irányítópultja.

![Adott telepítés frissítéstelepítési állapotának irányítópultja](./media/automation-tutorial-update-management/manageupdates-view-results.png)

A **Frissítés eredményei** terület összesíti a frissítések teljes számát és az adott virtuális gépre vonatkozó telepítési eredményeket. A jobb oldali táblázat az egyes frissítések részletes áttekintését és a telepítés eredményét tartalmazza.

A következő lista az elérhető értékeket jeleníti meg:

* **Nem lett megkísérelve**: a frissítés nem lett telepítve, mert a megadott karbantartási időszak alapján nem lett volna rá elég idő.
* **Sikeres :** A frissítés sikerült.
* **Nem sikerült**: A frissítés nem sikerült.

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
> [Az Update Management megoldás](automation-update-management.md)

