---
title: Az Azure portal használata az Azure Data Box Edge hibaelhárítása |} A Microsoft Docs
description: Ismerteti, hogyan lehet Azure Data Box Edge kapcsolatos problémák elhárítása.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/15/2019
ms.author: alkohli
ms.openlocfilehash: af72bc4ced556b8ea7493e4100b0ab06931e8e2d
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58405986"
---
# <a name="troubleshoot-your-azure-data-box-edge-issues"></a>Az Azure Data Box Edge hibáinak elhárítása 

Ez a cikk ismerteti az Azure Data Box Edge kapcsolatos problémák elhárítása. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Diagnosztika futtatása
> * Támogatási csomag összeállítása
> * Hibaelhárítás naplók használatával


## <a name="run-diagnostics"></a>Diagnosztika futtatása

Az eszközök diagnosztizálásához és a hibák elhárításához futtassa le a diagnosztikai teszteket. Eszközének helyi webes felhasználói felületen kövesse az alábbi lépéseket a diagnosztikai tesztek futtatásához.

1. A helyi webes felhasználói felületen válassza a **Hibaelhárítás > Diagnosztikai tesztek** lehetőséget. Válassza ki a tesztet a futtatáshoz, és kattintson a **Teszt futtatása** gombra. Megtörténik a hálózat, az eszköz, a webalkalmazás-proxy, az idő és a felhő beállításaival kapcsolatos lehetséges problémák diagnosztizálása. Értesítést kap, hogy az eszköz teszteket futtat.

    ![Válassza ki a tesztek](media/data-box-edge-troubleshoot/run-diag-1.png)
 
2. A tesztek lefutása után megjelennek az eredmények. 

    ![Vizsgálati eredmények áttekintése](media/data-box-edge-troubleshoot/run-diag-2.png)

    Ha egy teszt sikertelen, megjelenik a javasolt művelet URL-címe. Az URL-címre kattintva tekintse meg a javasolt műveletet.
 
    ![Tekintse át a figyelmeztetéseket sikertelen tesztek](media/data-box-edge-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>Támogatási csomag összeállítása

A naplócsomag tartalmazza az összes naplót, amely segít a Microsoft ügyfélszolgálatának az eszközproblémák elhárításában. A naplócsomagot a helyi webes felhasználói felületen hozhatja létre.

Kövesse az alábbi lépéseket a támogatási csomag összeállításához. 

1. A helyi webes felhasználói felületen válassza a **Hibaelhárítás > Támogatás** lehetőséget. Kattintson a **Támogatási csomag létrehozása** elemre. A rendszer megkezdi a támogatási csomag összeállítását. A csomagok gyűjteményének létrehozása eltarthat néhány percig.

    ![Kattintson a Felhasználó hozzáadása gombra](media/data-box-edge-troubleshoot/collect-logs-1.png)
 
2. A támogatási csomag létrehozásának befejezését követően kattintson a **Támogatási csomag letöltése** elemre. A rendszer a tömörített csomagot letölti a megadott helyre. Csomagolja ki a csomagot, és tekintse meg a rendszernapló fájljait.

    ![Kattintson a Felhasználó hozzáadása gombra](media/data-box-edge-troubleshoot/collect-logs-2.png)

## <a name="use-logs-to-troubleshoot"></a>Hibaelhárítás naplók használatával

A feltöltési és frissítési folyamat során észlelt hibákat a megfelelő hibafájlok tartalmazzák.

1. A hibafájlok megtekintéséhez nyissa meg saját megosztását, majd megosztásra kattintva tekintse meg tartalmát. 

      ![Csatlakozás és megosztás tartalmának megtekintése](media/data-box-edge-troubleshoot/troubleshoot-logs-1.png)

2. Kattintson a _Microsoft Data Box Edge mappa_. Ebben a mappában két almappát talál:

    - A feltöltési mappa, amely a feltöltési hibákat tartalmazó naplófájlokat tárolja.
    - A frissítési mappa a frissítés során bekövetkezett hibák számára.

    Íme egy minta naplófájl, amely a frissítési hibákat tartalmazza.

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. Ha ebben a fájlban hibát talál (a mintában kiemelve látható), jegyezze fel a hibakódot, amely ebben az esetben 16001. Keresse meg a hibakód leírását a következő hibaadatok alapján.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]


## <a name="next-steps"></a>További lépések

- További információk a [jelen kiadás ismert problémáiról](data-box-gateway-release-notes.md).
