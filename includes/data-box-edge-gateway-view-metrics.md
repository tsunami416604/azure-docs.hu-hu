---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: e02c0b86cd542b3ea12914e35a6577cf4e9b43d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179570"
---
A metrikák at is megtekintheti az eszköz teljesítményének figyeléséhez, és bizonyos esetekben az eszközproblémák elhárításához.

Az alábbi lépéseket az Azure Portalon a kiválasztott eszközmetrikák diagram létrehozásához.

1. Az Azure Portalon az erőforráshoz nyissa meg **> metrikák figyelése,** és válassza **a Metrika hozzáadása**lehetőséget.

    ![Metrika hozzáadása](media/data-box-edge-gateway-view-metrics/view-metrics-1.png)

2. Az erőforrás automatikusan kitöltődik.  

    ![Jelenlegi erőforrás](media/data-box-edge-gateway-view-metrics/view-metrics-2.png)

    Másik erőforrás megadásához jelölje ki az erőforrást. Az Erőforrás panel **kiválasztása** csoportban válassza ki az előfizetést, az erőforráscsoportot, az erőforrástípust és azt az erőforrást, amelynek a metrikákat meg szeretné jelenmutatni, és válassza az **Alkalmaz lehetőséget.**

    ![Másik erőforrás kiválasztása](media/data-box-edge-gateway-view-metrics/view-metrics-3.png)

3. A legördülő listából válasszon ki egy mérőszámot az eszköz figyeléséhez. A mérőszámok lehetnek **kapacitásmérők** vagy **tranzakciós mérőszámok.** A kapacitásmetrikák az eszköz kapacitásához kapcsolódnak. A tranzakciós metrikák az Azure Storage olvasási és írási műveleteihez kapcsolódnak.

    |Kapacitásmetrikák                     |Leírás  |
    |-------------------------------------|-------------|
    |**Rendelkezésre álló kapacitás**               | Az eszközre írható adatok méretére utal. Más szóval ez az eszköz által elérhetővé tehető kapacitás. <br></br>Az eszköz kapacitását felszabadíthatja, ha a számítógépen és a felhőben is másolatot tartalmazó fájlok helyi példányát törlöd.        |
    |**Teljes kapacitás**                   | Az eszközön az adatok írásához az eszköz teljes bájtjaira hivatkozik. Ezt a helyi gyorsítótár teljes méretének is nevezik. <br></br> Most már növelheti egy meglévő virtuális eszköz kapacitását egy adatlemez hozzáadásával. Adjon hozzá egy adatlemezt a virtuális gép hipervizor-kezelésén keresztül, majd indítsa újra a virtuális gép. Az átjáróeszköz helyi tárolókészlete kibővül az újonnan hozzáadott adatlemez befogadására. <br></br>További információ: [Merevlemez hozzáadása a Hyper-V virtuális géphez](https://www.youtube.com/watch?v=EWdqUw9tTe4). |
    
    |Tranzakciómetrikák              | Leírás         |
    |-------------------------------------|---------|
    |**Feltöltött felhőalapú bájtok (eszköz)**    | Az eszköz összes megosztására feltöltött összes bájt összege        |
    |**Feltöltött felhőalapú bájtok (megosztás)**     | Egy részvényre feltöltött bájtok. Ez lehet: <br></br> Átlagos, amely a (Részvényenként feltöltött bájtok összege / részvények száma),  <br></br>Max. <br></br>Min, amely a megosztásból feltöltött bájtok minimális száma      |
    |**Felhőbeli letöltési átviteli -fó (megosztás)**| Egy részvényre letöltött bájt. Ez lehet: <br></br> Átlagos, amely a (Összege az összes bájt olvasni vagy letölteni egy részvény / Részvények száma) <br></br> Max.<br></br> és Min, amely a minimális számú bájtot letöltött egy részvény  |
    |**Felhőbeli olvasási átviteli adatátarány**            | A felhőből a készülék összes megosztásán felolvasott bájtok összege     |
    |**Felhőbeli feltöltési átviteli adatátarány**          | A felhőbe írt összes bájt összege az eszköz összes megosztásán     |
    |**Felhőbeli feltöltési átviteli -átmenő (megosztás)**  | A felhőbe egy részvényből /# részvényekből írt bájtok összege átlagos, max és min részvényenként      |
    |**Olvasási átviteli -átmenő (hálózat)**           | Tartalmazza a rendszer hálózati átviteli a felhőből beolvasott összes bájt. Ez a nézet olyan adatokat is tartalmazhat, amelyek nem korlátozódnak a megosztásra. <br></br>A felosztás megmutatja az eszköz összes hálózati adapterének forgalmát. Ide tartoznak azok az adapterek is, amelyek nincsenek csatlakoztatva vagy engedélyezve.      |
    |**Írási átviteli -átmenő (hálózat)**       | Tartalmazza a rendszer hálózati átviteli a felhőbe írt összes bájtok. Ez a nézet olyan adatokat is tartalmazhat, amelyek nem korlátozódnak a megosztásra. <br></br>A felosztás megmutatja az eszköz összes hálózati adapterének forgalmát. Ide tartoznak azok az adapterek is, amelyek nincsenek csatlakoztatva vagy engedélyezve.          |
    |**Peremhálózati számítás - memóriahasználat**      | Ez a metrika nem alkalmazható a Data Box Gateway, és így nem lakott.          |
    |**Peremhálózati számítás - százalékos CPU**    | Ez a metrika nem alkalmazható a Data Box Gateway, és így nem lakott.         |

4. Ha egy metrika van kiválasztva a legördülő listából, összesítés is definiálható. Az összesítés egy adott időtartamon összesítve lévő tényleges értékre vonatkozik. Az összesített értékek lehetnek átlagok, minimumok vagy maximális értékek. Válassza ki az Összesítést az Átlag, a Max vagy a Min értékből.

    ![Diagram megtekintése](media/data-box-edge-gateway-view-metrics/view-metrics-4.png)

5. Ha a kiválasztott mérőszám több példányt, majd a felosztási lehetőség érhető el. Válassza **a Felosztás alkalmazása lehetőséget,** majd válassza ki azt az értéket, amellyel meg szeretné tekinteni a bontást.

    ![Felosztás alkalmazása](media/data-box-edge-gateway-view-metrics/view-metrics-5.png)

6. Ha most csak néhány példányban szeretné látni a bontást, szűrheti az adatokat. Ebben az esetben például, ha csak az eszköz két csatlakoztatott hálózati kapcsolatának hálózati átviteli csatornáját szeretné látni, szűrheti ezeket az összeköttetéseket. Válassza **a Szűrő hozzáadása lehetőséget,** és adja meg a hálózati adapter nevét a szűréshez.

    ![Szűrő hozzáadása](media/data-box-edge-gateway-view-metrics/view-metrics-6.png)

7. A könnyebb hozzáférés érdekében a diagramot az irányítópultra is rögzítheti.

    ![Rögzítés az irányítópulton](media/data-box-edge-gateway-view-metrics/view-metrics-7.png)

8. Ha diagramadatokat szeretne excel-számolótáblába exportálni, vagy a megosztott diagramra mutató hivatkozást szeretne kapni, válassza a megosztási lehetőséget a parancssávon.

    ![Adatok exportálása](media/data-box-edge-gateway-view-metrics/view-metrics-8.png)