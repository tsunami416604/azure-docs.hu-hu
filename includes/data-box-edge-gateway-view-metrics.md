---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: e02c0b86cd542b3ea12914e35a6577cf4e9b43d8
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59618506"
---
A metrikák az eszköz és a hibaelhárítási problémák bizonyos esetekben a teljesítmény figyelésére is megtekintheti.

Az alábbi lépéseket a kiválasztott eszköz metrikák diagram létrehozásához az Azure Portalon.

1. Lépjen az Azure Portalon az erőforrás, **figyelés > metrikák** válassza **metrika hozzáadása**.

    ![Metrika hozzáadása](media/data-box-edge-gateway-view-metrics/view-metrics-1.png)

2. Az erőforrást a rendszer automatikusan kitölti.  

    ![Aktuális erőforrás](media/data-box-edge-gateway-view-metrics/view-metrics-2.png)

    Egy másik erőforrás megadásához válassza ki az erőforrást. A **válasszon ki egy erőforrást** panelen válassza ki az előfizetést, erőforráscsoportot, erőforrás típusa és az adott erőforrás, amelynek a metrikák, és válassza ki a kívánt **alkalmaz**.

    ![Válasszon egy másik erőforrás](media/data-box-edge-gateway-view-metrics/view-metrics-3.png)

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
    |**Edge-compute - memória használata**      | Ez a mérőszám nincs a Data Box Gateway vonatkozik, és ezért nem feltöltött.          |
    |**Edge-compute - százalékos Processzorhasználat**    | Ez a mérőszám nincs a Data Box Gateway vonatkozik, és ezért nem feltöltött.         |

4. Ha egy metrika van kijelölve, a legördülő listából, az összesítés is lehet definiálni. A tényleges érték összesített értéket jelenít meg a megadott időn belül összesítést hivatkozik. Az összesített értékekre átlagos, minimális vagy maximális értéke lehet. Válassza ki az összesítést az Avg, Max vagy perc.

    ![Diagram nézet](media/data-box-edge-gateway-view-metrics/view-metrics-4.png)

5. Ha a kiválasztott metrika több példánnyal rendelkezik, majd a felosztási beállítás érhető el. Válassza ki **alkalmazni a felosztás** , és válassza ki az értéket, amelynek meg szeretné tekinteni a bontás.

    ![Felosztás alkalmazása](media/data-box-edge-gateway-view-metrics/view-metrics-5.png)

6. Ha most szeretné tekinteni néhány példányok csak a táblázat összefoglalja, szűrheti az adatokat. Például ebben az esetben, ha szeretné a hálózati átviteli sebesség csak a két csatlakoztatott hálózati adaptereket lát az eszközén, is szűrhetőek felületeken. Válassza ki **szűrő hozzáadása** , és adja meg a szűréshez hálózati adapter neve.

    ![Szűrő hozzáadása](media/data-box-edge-gateway-view-metrics/view-metrics-6.png)

7. Sikerült is rögzíti az irányítópulton egyszerűen hozzáférhetnek a diagramot.

    ![Rögzítés az irányítópulton](media/data-box-edge-gateway-view-metrics/view-metrics-7.png)

8. Diagram-adatok exportálása egy Excel-táblázatban, vagy szerezzen be egy hivatkozást a diagramba megoszthat, válassza a megosztás a parancssávon.

    ![Adatok exportálása](media/data-box-edge-gateway-view-metrics/view-metrics-8.png)