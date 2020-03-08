---
title: Azure-beli virtuális gépek frissítéseinek és javításának kezelése
description: Ez a cikk áttekintést nyújt a Azure Automation Update Management használatáról az Azure-beli és nem Azure-beli virtuális gépek frissítéseinek és javításának kezeléséhez.
services: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 03/04/2020
ms.custom: mvc
ms.openlocfilehash: 347f2fbc0f12aa775c42dbb14a4625dc509a20ed
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372825"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Azure-beli virtuális gépek frissítéseinek és javításának kezelése

A virtuális gépek frissítéseit és javításait az Update Management megoldás segítségével kezelheti. Ez az oktatóanyag azt ismerteti, hogyan mérheti fel legkönnyebben az elérhető frissítések állapotát, ütemezheti a szükséges frissítések telepítését, tekintheti át a telepítési eredményeket, és állíthat be riasztásokat, hogy ellenőrizze, sikeres volt-e a frissítések telepítése.

Díjszabási információkért tekintse meg az [Automation Update Management-díjszabását](https://azure.microsoft.com/pricing/details/automation/) ismertető cikket.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Frissítésfelmérés megtekintése
> * Riasztások konfigurálása
> * Frissítéstelepítés ütemezése
> * Telepítés eredményeinek megtekintése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* A [Update Management](automation-update-management.md) -megoldás engedélyezve van egy vagy több virtuális gépen.
* A szolgáltatásba felvenni kívánt [virtuális gép](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="view-update-assessment"></a>A frissítésfelmérés megtekintése

Az Update Management engedélyezése után megnyílik az **Update Management** ablaktábla. Ha a rendszer hiányzóként azonosította a frissítéseket, a hiányzó **frissítések** lapon megjelenik a hiányzó frissítések listája.

Az **információ hivatkozás**alatt válassza a frissítés hivatkozást a frissítés támogatási cikkének megnyitásához. A frissítéssel kapcsolatos fontos információkhoz is tájékozódhat.

![Frissítés állapotának megtekintése](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

A frissítésben kattintson a továbbiak elemre, hogy megnyissa a **naplóbeli keresés** ablaktáblát a kiválasztott frissítéshez. A naplókeresés lekérdezése az adott frissítéshez előre meg van adva. Ezt a lekérdezést módosíthatja, vagy létrehozhatja saját lekérdezését, amellyel részletes információkat jeleníthet meg a környezetben telepített vagy a hiányzó frissítésekre vonatkozóan.

![Frissítés állapotának megtekintése](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>Riasztások konfigurálása

Ebben a lépésben megtudhatja, hogyan állíthat be egy riasztást, amely lehetővé teszi egy frissítés központi telepítésének állapotát.

### <a name="alert-conditions"></a>A riasztás feltételei

Az Automation-fiókjában válassza a **figyelés** ugrás **riasztások**lehetőséget, majd kattintson az **+ új riasztási szabály**elemre.

Az Automation-fiókja már ki van választva erőforrásként. Ha módosítani kívánja, kattintson a **kiválasztás** lehetőségre, majd az **erőforrás kiválasztása** lapon válassza az **Automation-fiókok** lehetőséget a **szűrés erőforrás típusa** legördülő listából. Válassza ki az Automation-fiókját, majd kattintson a **Kész** gombra.

Kattintson a **feltétel hozzáadása** elemre a frissítés telepítéséhez megfelelő jel kiválasztásához. A következő táblázat a frissítések központi telepítésének két elérhető jeleit mutatja be:

|Jel neve|Dimenziók|Leírás|
|---|---|---|
|**Összes frissítés központi telepítési futtatása**|-Központi telepítési név frissítése</br>– Állapot|Ez a jel a frissítések központi telepítésének általános állapotára való riasztásra szolgál.|
|**Összes frissítés központi telepítési gép futtatása**|-Központi telepítési név frissítése</br>– Állapot</br>– Célszámítógép</br>-A központi telepítés futtatási AZONOSÍTÓjának frissítése|Ez a jel egy adott gépen megcélzott frissítési központi telepítés állapotára való riasztásra szolgál.|

A dimenzió értékeihez válasszon ki egy érvényes értéket a listából. Ha a keresett érték nem szerepel a listában, kattintson a dimenzió melletti **\+** jelre, és írja be az egyéni nevet. Ezután kiválaszthatja a keresni kívánt értéket. Ha az összes értéket ki szeretné jelölni egy dimenzióból, kattintson a **\*kiválasztása** gombra. Ha nem választja ki a dimenzió értékét, a rendszer figyelmen kívül hagyja a dimenziót a kiértékelés során.

![Jellogika konfigurálása](./media/automation-tutorial-update-management/signal-logic.png)

A **Riasztási logika** területen a **Küszöbérték** legyen **1**. Ha elkészült, válassza a **Kész** lehetőséget.

### <a name="alert-details"></a>Riasztás részletei

A **2. résznél. Adja meg a riasztás részleteit**, adjon meg egy nevet és egy leírást a riasztáshoz. Állítsa a **Súlyosság** beállítást sikeres futtatás esetén **Tájékoztató (Sev 2)** értékre, sikertelen futtatás esetén pedig **Tájékoztató (Sev 1)** értékre.

![Jellogika konfigurálása](./media/automation-tutorial-update-management/define-alert-details.png)

A **műveleti csoportok**területen válassza az **új létrehozása**lehetőséget. A műveletcsoport műveletek csoportja, amelyeket több riasztáson is alkalmazhat. Ezek a műveletek a teljesség igénye nélkül a következők lehetnek: e-mail-értesítések, runbookok, webhookok stb. A műveletcsoportokkal kapcsolatban további információt a [műveletcsoportok létrehozásáról és kezeléséről](../azure-monitor/platform/action-groups.md) szóló cikkben talál.

A **Műveletcsoport neve** mezőben adja meg a riasztás nevét és egy rövid nevet. A rendszer a rövid nevet használja a műveletcsoport teljes neve helyett, amikor értesítéseket küld a csoport használatával.

A **műveletek**területen adja meg a művelet nevét, például az **e-mailes értesítéseket**. A **művelet típusa**területen válassza az **e-mail/SMS/leküldés/hang**lehetőséget. A **részletek**területen válassza a **részletek szerkesztése**lehetőséget.

Az **E-mail/SMS/Küldés/Hang** ablaktáblán adjon meg egy nevet. Jelölje be az **E-mail** jelölőnégyzetet, és adjon meg egy érvényes e-mail-címet.

![E-mail műveletcsoport konfigurálása](./media/automation-tutorial-update-management/configure-email-action-group.png)

Az **e-mail/SMS/leküldés/hang** ablaktáblán kattintson az **OK gombra**. A **műveleti csoport hozzáadása** panelen kattintson **az OK gombra**.

A riasztási e-mail tárgyának testreszabásához a **szabály létrehozása**területen a **műveletek testreszabása**területen válassza az **e-mail tárgy**elemet. Ha végzett, kattintson a **Riasztási szabály létrehozása** gombra. A riasztás figyelmezteti, ha egy frissítés telepítése sikeresen befejeződött, és tájékoztat róla, hogy mely számítógépeket érintett az adott frissítéstelepítés.

## <a name="schedule-an-update-deployment"></a>Frissítéstelepítés ütemezése

Következő lépésként ütemezzen egy olyan telepítést a frissítésekhez, amely megfelel a kiadási ütemtervnek és a szolgáltatási időkeretnek. Kiválaszthatja, hogy a telepítés milyen típusú frissítéseket tartalmazzon. Például hozzáadhatja a kritikus vagy a biztonsági frissítéseket, és kizárhatja a kumulatív frissítéseket.

>[!NOTE]
>A frissítések központi telepítésének megkezdése után egy olyan [ütemezett](shared-resources/schedules.md) erőforrást hoz létre, amely kapcsolódik a **MicrosoftOMSComputers** runbook, amely kezeli a frissítés központi telepítését a célszámítógépen. Ha törli az ütemezési erőforrást a Azure Portal vagy a PowerShellt a központi telepítés létrehozása után, megszakítja az ütemezett frissítést, és hibaüzenetet jelenít meg, amikor megkísérli az újrakonfigurálást a portálról. Az ütemezett erőforrást csak a megfelelő központi telepítési ütemterv törlésével törölheti.  
>

Ütemezzen egy új frissítéstelepítést a virtuális géphez. Ehhez válassza a **Frissítéskezelés**, majd a **Frissítések központi telepítésének ütemezése** elemet.

Az **Új frissítéstelepítés** képernyőn adja meg a következő információkat:

* **Név**: Adjon egyedi nevet a frissítéstelepítésnek.

* **Operációs rendszer**: Válassza ki azt az operációs rendszert, amelyre a frissítéstelepítés vonatkozni fog.

* **Frissítendő csoportok (előzetes verzió)** : Meghatározhat egy előfizetéseken, erőforráscsoportokon, helyeken és címkéken alapuló lekérdezést, amellyel egy dinamikus, Azure-beli virtuális gépekből álló csoportot hozhat létre, majd belefoglalhatja a telepítésbe. További információ: [dinamikus csoportok](automation-update-management-groups.md)

* **Frissítendő gépek**: Válasszon ki egy Mentett keresést vagy Importált csoportot, vagy válassza a legördülő listában a Gép lehetőséget, és válasszon ki egyes gépeket. Ha a **gépeket**választja, a gép készültsége megjelenik a **frissítési ügynök készültsége** oszlopban. A számítógépcsoportok Azure Monitor-naplókban való létrehozásának különböző módszereiről a következő témakörben talál további információt: [számítógépcsoportok Azure monitor-naplókban](../azure-monitor/platform/computer-groups.md)

* **Frissítés besorolása**: válassza ki az egyes termékek számára elérhető, támogatott frissítési besorolásokat, amelyek a frissítés központi telepítésében is szerepelhetnek. Ebben az oktatóanyagban hagyjon minden típust kiválasztva.

  A választható besorolási típusok a következők:

   |Operációs rendszer  |Típus  |
   |---------|---------|
   |Windows     | Kritikus frissítések</br>Biztonsági frissítések</br>Kumulatív frissítések</br>Funkciócsomagok</br>Szervizcsomagok</br>Definíciófrissítések</br>Eszközök</br>Frissítések        |
   |Linux     | Kritikus vagy biztonsági frissítések</br>Egyéb frissítések       |

   A besorolási típusok ismertetését [a frissítések besorolását](automation-view-update-assessments.md#update-classifications) leíró szakaszban találja.

* **Belefoglalandó/kizárandó frissítések** – Ez megnyitja a **Belefoglalás/kizárás** lapot. A belefoglalandó vagy kizárandó frissítések külön lapokon jelennek meg.

> [!NOTE]
> Fontos tudni, hogy a kizárások felülbírálják a belefoglalásokat. Ha például meghatároz egy `*`kizárási szabályát, akkor a rendszer nem telepíti a javításokat és a csomagokat, mivel azok ki vannak zárva. A kizárt javítások továbbra is hiányzóként jelennek meg a gépről. Linux rendszerű gépek esetén, ha csomag szerepel a csomagban, de egy kizárt függő csomaggal rendelkezik, a csomag nincs telepítve.

> [!NOTE]
> Nem adhat meg olyan frissítéseket, amelyek felváltották a frissítés központi telepítésére való felvételt.
>

* **Ütemezési beállítások**: Megnyitja az **Ütemezési beállítások** ablaktáblát. Az alapértelmezett kezdési időpont az aktuális időpontnál 30 perccel későbbi időpont. Bármilyen időpontra beállítható a pillanatnyi időt követő 10. perc után.

   Azt is megadhatja, hogy a telepítés egyszer történjen meg, vagy ismétlődő legyen. Az **Ismétlődés** menüpontban válassza az **Egyszer** lehetőséget. Hagyja meg az alapértelmezett értéket 1 nap alatt, majd kattintson **az OK gombra**. Ezzel egy ismétlődő ütemezést állít be.

* **Előkészítő szkriptek és utólagos szkriptek**: Válassza ki, mely szkripteket szeretné futtatni az üzembe helyezés előtt, illetve után. További információ: [Előkészítő és utólagos szkriptek kezelése](pre-post-scripts.md).

* **Karbantartási időszak (perc)** : Fogadja el az alapértelmezett értéket. A karbantartási időszakok a frissítések telepítésének időtartamát szabályozzák. Karbantartási időszak megadásakor vegye figyelembe a következő adatokat.

  * A karbantartási időszakok azt szabályozzák, hogy a rendszer hány frissítést próbáljon telepíteni.
  * A Update Management nem állítja le az új frissítések telepítését, ha a karbantartási időszak végére közeledik.
  * A Update Management nem szakítja meg a folyamatban lévő frissítéseket, ha a karbantartási időszak túllépve.
  * Ha a karbantartási időszakot túllépik a Windowson, gyakran egy szervizcsomag-frissítés miatt sok időt vesz igénybe a telepítés.

  > [!NOTE]
  > Ha el szeretné kerülni, hogy a frissítések ne legyenek alkalmazva az Ubuntu karbantartási időszakán kívül, konfigurálja újra a felügyelet nélküli frissítési csomagot az automatikus frissítések letiltásához. A csomag konfigurálásával kapcsolatos további információkért lásd [az Ubuntu Server útmutatójának automatikus frissítések témakörét](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

* **Újraindítási beállítások**: Ez a beállítás határozza meg az újraindítások kezelésének módját. Az elérhető lehetőségek:
  * Újraindítás szükség esetén (alapértelmezett beállítás)
  * Mindig induljon újra
  * Soha ne induljon újra
  * Csak újraindítás – frissítések nem lesznek telepítve

> [!NOTE]
> Az újraindítás [kezeléséhez használt beállításkulcsok](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) alatt felsorolt beállításkulcsok újraindítási eseményt okozhatnak, ha a **reboot Control** értéke **soha nem indul újra**.

Ha végzett az ütemezés beállításával, kattintson a **Létrehozás** gombra.

![A frissítés ütemezési beállításai ablaktábla](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

Ekkor visszalép az állapot-irányítópultra. Kattintson az **Ütemezett frissítéstelepítések** lehetőségre a létrehozott telepítési ütemezés megjelenítéséhez.

> [!NOTE]
> Az Update Management a belső frissítések üzembe helyezését és a javítócsomagok előzetes letöltését támogatja. Ehhez módosításokat kell végrehajtani a javítani kívánt rendszereken. A [belső és előre letöltött frissítéssel](automation-configure-windows-update.md) foglalkozó részben tudhatja meg, hogyan konfigurálja ezeket a beállításokat a rendszereken.

A **frissítési központi telepítések** programozott módon is létrehozhatók. Ha meg szeretné tudni, hogyan hozhat létre **frissítési központi telepítést** a REST API, tekintse meg a [szoftverfrissítési konfigurációk – létrehozás](/rest/api/automation/softwareupdateconfigurations/create)című témakört. Létezik egy minta runbook is, amely a heti **frissítés központi telepítésének**létrehozására használható. További információ erről a runbook: [heti frissítési telepítés létrehozása egy vagy több virtuális géphez egy erőforráscsoport esetében](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1).

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

## <a name="next-steps"></a>Következő lépések

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

