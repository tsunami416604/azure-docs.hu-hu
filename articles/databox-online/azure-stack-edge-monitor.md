---
title: A Azure Stack Edge Pro-eszköz monitorozása | Microsoft Docs
description: Ismerteti, hogyan használható a Azure Portal és a helyi webes felhasználói felület a Azure Stack Edge Pro figyelésére.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: cd75eb0f7de602979f2233a873c01ef742471e37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90904396"
---
# <a name="monitor-your-azure-stack-edge-pro"></a>A Azure Stack Edge Pro figyelése

Ez a cikk ismerteti, hogyan figyelheti Azure Stack Edge Pro-t. Az eszköz figyeléséhez Azure Portal vagy a helyi webes KEZELŐFELÜLETet használhatja. A Azure Portal használatával megtekintheti az eszközök eseményeit, konfigurálhatja és kezelheti a riasztásokat, és megtekintheti a metrikákat. A fizikai eszköz helyi webes felhasználói felületének használatával megtekintheti a különböző eszközök összetevőinek hardveres állapotát.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> * Az eszközök eseményeinek és a kapcsolódó riasztások megtekintése
> * Eszköz-összetevők hardveres állapotának megtekintése
> * Az eszköz kapacitásának és tranzakciós metrikáinak megtekintése
> * Riasztások konfigurálása és kezelése

## <a name="view-device-events"></a>Eszköz eseményeinek megtekintése

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Hardver állapotának megtekintése

Hajtsa végre a következő lépéseket a helyi webes felhasználói felületen az eszközösszetevők hardverállapotának megtekintéséhez.

1. Csatlakozzon az eszköz helyi webes felhasználói felületéhez.
2. Válassza a **karbantartás > hardver állapota**lehetőséget. Megtekintheti az egyes eszközösszetevők állapotát.

    ![Hardver állapotának megtekintése](media/azure-stack-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Metrikák megtekintése

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

### <a name="metrics-on-your-device"></a>Mérőszámok az eszközön

Ez a szakasz az eszköz figyelési mérőszámait ismerteti. A metrikák a következőket tehetik:

* Kapacitási mérőszámok. A kapacitás metrikái az eszköz kapacitásával kapcsolatosak.

* Tranzakciós metrikák. A tranzakció metrikái az olvasási és írási műveletekhez kapcsolódnak az Azure Storage-hoz.

* Peremhálózati számítási mérőszámok. A peremhálózati számítási mérőszámok az eszköz peremhálózati számítási feladataihoz kapcsolódnak.

A metrikák teljes listája a következő táblázatban látható:

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

| Edge számítási metrikák              | Leírás         |
|-------------------------------------|---------|
|**Edge-számítás – memóriahasználat**      |           |
|**Edge-számítás – százalékos CPU**    |         |

## <a name="manage-alerts"></a>Riasztások kezelése

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Következő lépések

További tudnivalókat a [sávszélesség-kezeléssel foglalkozó részben](azure-stack-edge-manage-bandwidth-schedules.md) talál.
