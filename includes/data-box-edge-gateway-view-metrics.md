---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: e02c0b86cd542b3ea12914e35a6577cf4e9b43d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "67179570"
---
A mérőszámokat megtekintheti az eszköz teljesítményének figyeléséhez, illetve egyes példányokban az eszközök problémáinak elhárításához.

Hozzon létre egy diagramot a kiválasztott eszköz metrikáinak létrehozásához a Azure Portal alábbi lépésein.

1. A Azure Portal erőforrásához lépjen a **figyelés > metrikák** elemre, és válassza a **metrika hozzáadása**elemet.

    ![Metrika hozzáadása](media/data-box-edge-gateway-view-metrics/view-metrics-1.png)

2. Az erőforrás automatikusan ki van töltve.  

    ![Aktuális erőforrás](media/data-box-edge-gateway-view-metrics/view-metrics-2.png)

    Egy másik erőforrás megadásához válassza ki az erőforrást. Az **erőforrás kiválasztása** panelen válassza ki az előfizetést, az erőforráscsoportot, az erőforrás típusát és az adott erőforrást, amelynek a metrikáit meg szeretné jeleníteni, majd válassza az **alkalmaz**lehetőséget.

    ![Válasszon másik erőforrást](media/data-box-edge-gateway-view-metrics/view-metrics-3.png)

3. A legördülő listából válassza ki az eszköz figyelésére szolgáló mérőszámot. A metrikák lehetnek **Kapacitási mérőszámok** vagy **tranzakciós metrikák**. A kapacitás metrikái az eszköz kapacitásával kapcsolatosak. A tranzakció metrikái az olvasási és írási műveletekhez kapcsolódnak az Azure Storage-hoz.

    |Kapacitásmetrikák                     |Leírás  |
    |-------------------------------------|-------------|
    |**Rendelkezésre álló kapacitás**               | Az eszközre írható adatméretet jelöli. Ez azt jelenti, hogy ez az a kapacitás, amelyet elérhetővé tehet az eszközön. <br></br>Az eszköz kapacitását az eszközön és a felhőben található másolattal rendelkező fájlok helyi másolatának törlésével szabadíthatja fel.        |
    |**Teljes kapacitás**                   | Az eszközön az adat írására szolgáló összes bájtra hivatkozik. Ezt a helyi gyorsítótár teljes méretének is nevezzük. <br></br> Most már megnövelheti egy meglévő virtuális eszköz kapacitását egy adatlemez hozzáadásával. Adjon hozzá egy adatlemezt a virtuális gép hypervisor-felügyeletén keresztül, majd indítsa újra a virtuális gépet. Az átjáró eszköz helyi tárolóhelye ki lesz bővítve az újonnan hozzáadott adatlemezre. <br></br>További információkért keresse [fel a Hyper-V virtuális gép merevlemezének hozzáadása](https://www.youtube.com/watch?v=EWdqUw9tTe4)című témakört. |
    
    |Tranzakciómetrikák              | Leírás         |
    |-------------------------------------|---------|
    |**Feltöltött Felhőbeli bájtok (eszköz)**    | Az eszközön található összes megosztáson feltöltött bájtok összege        |
    |**Felhőbeli feltöltött bájtok (megosztás)**     | Egy megosztáson feltöltött bájtok száma. Ez lehet: <br></br> AVG, amely (a megosztás/megosztások száma alapján feltöltött bájtok összege),  <br></br>Max, amely a megosztásból feltöltött bájtok maximális számát adja meg. <br></br>Minimum, amely a megosztásból feltöltött bájtok minimális száma      |
    |**Felhőbeli letöltési teljesítmény (megosztás)**| Egy megosztáson letöltött bájtok száma. Ez lehet: <br></br> Az AVG, amely (az összes olvasott vagy letöltött bájt összege egy megosztásba/megosztások száma) <br></br> Max, amely a megosztásból letöltött bájtok maximális számát adja meg.<br></br> és a min, amely a megosztásból letöltött bájtok minimális száma  |
    |**Felhőbeli olvasási sebesség**            | A felhőből beolvasott összes bájt összege az eszközön lévő összes megosztáson keresztül     |
    |**Felhőbeli feltöltési sebesség**          | A felhőbe írt összes bájt összege az eszközön található összes megosztáson keresztül     |
    |**Felhőbeli feltöltési sebesség (megosztás)**  | Egy megosztásból/megosztásból a felhőbe írt összes bájt összege átlagos, maximális és minimális      |
    |**Olvasási sebesség (hálózat)**           | Magában foglalja a felhőből beolvasott bájtok rendszerhálózati átviteli sebességét. Ez a nézet tartalmazhat olyan, a megosztásokra nem korlátozott adatforgalomat. <br></br>A felosztás megjeleníti a forgalmat az eszközön lévő összes hálózati adapteren. Ide tartoznak a nem csatlakoztatott vagy engedélyezett adapterek.      |
    |**Írási átviteli sebesség (hálózat)**       | Magában foglalja a felhőbe írt összes bájt rendszerhálózati átviteli sebességét. Ez a nézet tartalmazhat olyan, a megosztásokra nem korlátozott adatforgalomat. <br></br>A felosztás megjeleníti a forgalmat az eszközön lévő összes hálózati adapteren. Ide tartoznak a nem csatlakoztatott vagy engedélyezett adapterek.          |
    |**Edge-számítás – memóriahasználat**      | Ez a metrika Data Box Gateway esetén nem alkalmazható, ezért nincs feltöltve.          |
    |**Edge-számítás – százalékos CPU**    | Ez a metrika Data Box Gateway esetén nem alkalmazható, ezért nincs feltöltve.         |

4. Ha ki van választva egy metrika a legördülő listából, az Összesítés is meghatározható. Az Összesítés a megadott időtartamon keresztül összesített tényleges értékre hivatkozik. Az összesített értékek lehet átlagos, minimum vagy a maximális érték. Válassza ki az összesítést az AVG, a Max vagy a min értékből.

    ![Diagram megtekintése](media/data-box-edge-gateway-view-metrics/view-metrics-4.png)

5. Ha a kiválasztott metrika több példánnyal rendelkezik, akkor a felosztási lehetőség elérhető. Válassza a **felosztás alkalmazása** lehetőséget, majd válassza ki azt az értéket, amelynek meg szeretné tekinteni a részletezését.

    ![Felosztás alkalmazása](media/data-box-edge-gateway-view-metrics/view-metrics-5.png)

6. Ha most már csak néhány példány esetében szeretné megtekinteni a részletezést, szűrheti az adatszűrést. Például ebben az esetben, ha a hálózati átviteli sebességet csak az eszköz két csatlakoztatott hálózati adapterén szeretné látni, akkor szűrheti ezeket a csatolókat. Válassza a **szűrő hozzáadása** lehetőséget, és adja meg a hálózati adapter nevét a szűréshez.

    ![Szűrő hozzáadása](media/data-box-edge-gateway-view-metrics/view-metrics-6.png)

7. Az egyszerű hozzáférés érdekében a diagramot is rögzítheti az irányítópulton.

    ![Rögzítés az irányítópulton](media/data-box-edge-gateway-view-metrics/view-metrics-7.png)

8. A diagram adatainak Excel-számolótáblába való exportálásához, vagy a megosztható diagramra mutató hivatkozás beszerzéséhez kattintson a megosztás lehetőségre a parancssáv használatával.

    ![Adatok exportálása](media/data-box-edge-gateway-view-metrics/view-metrics-8.png)