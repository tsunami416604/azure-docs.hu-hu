---
title: Az Azure Data Box Edge-eszköz figyelése |} A Microsoft Docs
description: Az Azure Data Box Edge figyelése az Azure portal és a helyi webes felhasználói felület használatát ismerteti.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 4d4cc8a3eaf2bf806d5b265be226482b23804f82
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59580378"
---
# <a name="monitor-your-azure-data-box-edge"></a>Az Azure Data Box Edge figyelése

Ez a cikk ismerteti az Azure Data Box Edge figyelése. Az eszköz figyeléséhez, használhatja az Azure portal vagy a helyi webes felületén. Az Azure portal használatával eszköz események megtekintése, konfigurálása és a riasztások kezelése és metrikákat tekinthet meg. A fizikai eszköz helyi webes felhasználói felület használatával a különböző eszköz összetevők hardver állapotának megtekintése.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Eszköz-események megtekintése és az ezekkel kapcsolatos riasztások
> * Alkatrészek hardverállapot megtekintése
> * Az eszköz a kapacitás és a tranzakciós metrikáinak megtekintése
> * Konfigurálhatja és kezelheti a riasztásokat

## <a name="view-device-events"></a>Eszköz-események megtekintése

Az alábbi lépéseket egy eszköz esemény megtekintése az Azure Portalon.

1. Az Azure Portalon nyissa meg a Data Box-Edge / Data Box-Gateway-erőforráshoz, és lépjen **figyelés > eszköz események**.
2. Jelöljön ki egy eseményt, és a riasztás részleteinek megtekintéséhez. Hajtsa végre a megfelelő műveletet a riasztási feltétel megoldásához.

    ![Válassza ki az esemény- és nézet részletei](media/data-box-edge-monitor/view-device-events.png)

## <a name="view-hardware-status"></a>Hardverállapot megtekintése

Az alábbi lépéseket a helyi webes felhasználói Felületét, hogy az eszköz-összetevők hardver állapotának megtekintése. Ezek az információk csak a Data Box peremhálózati eszköz érhető el.

1. Csatlakozzon a helyi webes felületén az eszközt.
2. Lépjen a **karbantartási > hardverállapot**. A különböző eszköz összetevők állapotát tekintheti meg.

    ![Hardverállapot megtekintése](media/data-box-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Metrikák megtekintése

A metrikák az eszköz és a hibaelhárítási problémák bizonyos esetekben a teljesítmény figyelésére is megtekintheti.

Az alábbi lépéseket a kiválasztott eszköz metrikák diagram létrehozásához az Azure Portalon.

1. Lépjen az Azure Portalon az erőforrás, **figyelés > metrikák** válassza **metrika hozzáadása**.

    ![Metrika hozzáadása](media/data-box-edge-monitor/view-metrics-1.png)

2. Az erőforrást a rendszer automatikusan kitölti.  

    ![Aktuális erőforrás](media/data-box-edge-monitor/view-metrics-2.png)

    Egy másik erőforrás megadásához válassza ki az erőforrást. A **válasszon ki egy erőforrást** panelen válassza ki az előfizetést, erőforráscsoportot, erőforrás típusa és az adott erőforrás, amelynek a metrikák, és válassza ki a kívánt **alkalmaz**.

    ![Válasszon egy másik erőforrás](media/data-box-edge-monitor/view-metrics-3.png)

3. A legördülő listából válassza ki egy metrikát, az eszköz figyeléséhez. A metrikák lehet **kapacitási** vagy **tranzakció-mérőszámot**. Az eszköz a kapacitás kapcsolódó a kapacitási mérőszámot. A tranzakció-mérőszámot kapcsolódnak, az olvasási és írási műveletek az Azure Storage.

    |A kapacitás-metrikák                     |Leírás  |
    |-------------------------------------|-------------|
    |**Használható kapacitás**               | Az eszköz csak írható adat méretét jelenti. Más szóval ez az a kapacitást, amelyhez tehetők elérhetővé az eszközön. <br></br>Az eszköz kapacitás felszabadítása a fájlra, amely egy másolatot az eszköz és is a felhőben lévő helyi példányának törlése.        |
    |**Teljes kapacitás**                   | Az eszközön, az adatok írása az összes bájt hivatkozik. Ez is nevezzük a helyi gyorsítótár teljes mérete. <br></br> Adatlemez hozzáadása egy meglévő virtuális eszközt kapacitása mostantól növelheti. Adjon hozzá egy adatlemezt a virtuális gép a hipervizor-management szolgáltatáson keresztül, és indítsa újra a virtuális gép. A helyi tárolókészlet az átjáróeszköz bontsa ki az újonnan hozzáadott adatlemez befogadásához. <br></br>További információért ugorjon [hozzáadása egy Hyper-v rendszerű virtuális gép merevlemez](https://www.youtube.com/watch?v=EWdqUw9tTe4). |
    
    |Tranzakció-mérőszámot              | Leírás         |
    |-------------------------------------|---------|
    |**Feltöltött bájtok (eszköz) a felhő**    | Az eszközön a megosztások feltöltött egyezik meg az összes bájt        |
    |**A felhő feltöltött bájtok (megosztás)**     | Bájt jutó töltött fel. Ez lehet: <br></br> Átlagos, amely a (megosztás címenként feltöltött egyezik meg az összes bájt / Number megosztások),  <br></br>Megosztásból feltöltött bájtok maximális száma maximális száma <br></br>Minimum, amely egy meghajtóról feltöltött bájtok minimális száma      |
    |**Felhőalapú letöltési átviteli sebesség (megosztás)**| Bájt letöltve az egy. Ez lehet: <br></br> Átlagos, amely a (összege az összes bájtot olvassa el, vagy egy megosztásra letöltött / Number megosztások) <br></br> Maximális száma, amely legfeljebb hány bájtot letöltött megosztásból<br></br> és a minimális és a minimális száma megosztásból letöltött bájtok száma  |
    |**A felhőalapú olvasási teljesítménye**            | Olvassa el a felhőből az eszközön a megosztások közötti összes bájt összege     |
    |**Felhőbeli feltöltési sebessége**          | Az eszközön minden megosztás között a felhőre írt összes bájt összege     |
    |**Felhőbeli feltöltési sebessége (megosztás)**  | Sum megosztásból a felhőre írt összes bájt / megosztások száma az átlag, maximális és minimális értéke a megosztás      |
    |**Olvasás átviteli sebesség (hálózat)**           | A rendszer hálózati átviteli sebesség olvassa el a felhőből az összes bájtot tartalmaz. Ez a nézet nem korlátozott megosztások adatok tartalmazhatnak. <br></br>Felosztás jelennek meg a forgalmat az összes hálózati adapter az eszközön keresztül. Ez magában foglalja az adaptert, amely nem kapcsolódik vagy nem engedélyezett.      |
    |**Írás az átviteli sebesség (hálózat)**       | Magában foglalja a rendszer hálózati átviteli sebesség a felhő írt bájtok száma. Ez a nézet nem korlátozott megosztások adatok tartalmazhatnak. <br></br>Felosztás jelennek meg a forgalmat az összes hálózati adapter az eszközön keresztül. Ez magában foglalja az adaptert, amely nem kapcsolódik vagy nem engedélyezett.          |
    |**Edge-compute - memória használata**      | Az IoT Edge-eszköz számára a Data Box Edge memóriahasználat. Ha a magas kihasználtságú jelenik meg, és ha az eszköz teljesítményére hatással van az aktuális számítási feladatok üzembe helyezett, forduljon a Microsoft Support meghatározni a következő lépéseket. <br></br>Ez a mérőszám nincs feltöltve az adatátjáró mezőbe.          |
    |**Edge-compute - százalékos Processzorhasználat**    | CPU-használat a Data Box Edge IoT Edge-eszköz számára. Ha a magas kihasználtságú jelenik meg, és ha az eszköz teljesítményére hatással van az aktuális számítási feladatok üzembe helyezett, forduljon a Microsoft Support meghatározni a következő lépéseket. <br></br>Ez a mérőszám nincs feltöltve az adatátjáró mezőbe.        |
1. Ha egy metrika van kijelölve, a legördülő listából, az összesítés is lehet definiálni. A tényleges érték összesített értéket jelenít meg a megadott időn belül összesítést hivatkozik. Az összesített értékekre átlagos, minimális vagy maximális értéke lehet. Válassza ki az összesítést az Avg, Max vagy perc.

    ![Diagram nézet](media/data-box-edge-monitor/view-metrics-4.png)

5. Ha a kiválasztott metrika több példánnyal rendelkezik, majd a felosztási beállítás érhető el. Válassza ki **alkalmazni a felosztás** , és válassza ki az értéket, amelynek meg szeretné tekinteni a bontás.

    ![Felosztás alkalmazása](media/data-box-edge-monitor/view-metrics-5.png)

6. Ha most szeretné tekinteni néhány példányok csak a táblázat összefoglalja, szűrheti az adatokat. Például ebben az esetben, ha szeretné a hálózati átviteli sebesség csak a két csatlakoztatott hálózati adaptereket lát az eszközén, is szűrhetőek felületeken. Válassza ki **szűrő hozzáadása** , és adja meg a szűréshez hálózati adapter neve.

    ![Szűrő hozzáadása](media/data-box-edge-monitor/view-metrics-6.png)

7. Sikerült is rögzíti az irányítópulton egyszerűen hozzáférhetnek a diagramot.

    ![Rögzítés az irányítópulton](media/data-box-edge-monitor/view-metrics-7.png)

8. Diagram-adatok exportálása egy Excel-táblázatban, vagy szerezzen be egy hivatkozást a diagramba megoszthat, válassza a megosztás a parancssávon.

    ![Adatok exportálása](media/data-box-edge-monitor/view-metrics-8.png)

## <a name="manage-alerts"></a>Riasztások kezelése

Riasztási szabályok tájékoztat az eszközén erőforrások felhasználását kapcsolódó riasztási feltételek konfigurálása. Riasztási szabályok az eszközt a riasztási feltételek figyelése konfigurálhatja. Részletes információkat a riasztásokat, Ugrás [létrehozása, megtekintése és kezelése az Azure monitorban metrikákhoz kapcsolódó riasztások](../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>További lépések 

További tudnivalókat a [sávszélesség-kezeléssel foglalkozó részben](data-box-edge-manage-bandwidth-schedules.md) talál.