---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 07/01/2020
ms.author: alkohli
ms.openlocfilehash: d099e33e7b35381f5404c9f8964d3ea90d4f3908
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85959474"
---
Az utolsó lépés az eszköz szállításának előkészítése. Ebben a lépésben az összes eszközmegosztást offline állapotba helyezi. A folyamat megkezdése után a megosztásokhoz nem lehet hozzáférni.

> [!IMPORTANT]
> El kell végezni a szállítás előkészítését, mert ez megjelöli az Azure elnevezési konvencióinak nem megfelelő adatokat. Ez a lépés megakadályozza a nem megfelelő adatok miatt fellépő adatfeltöltési hibákat.

1. Lépjen a **Prepare to ship** (Szállításra való előkészítés) oldalra, majd kattintson a **Start preparation** (Előkészítés indítása) elemre. Alapértelmezés szerint az ellenőrzőösszegek kiszámítására az adatok másolása során kerül sor. A szállítás előkészítése kiszámítja az ellenőrzőösszegeket, és létrehozza a fájlok listáját ( *anyagjegyzékfájlok*). Az ellenőrzőösszeg kiszámítása az adatok mennyiségétől függően órákig vagy napokig is tarthat. 
   
    ![A szállítás előkészítése 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    Ha valamiért le szeretné állítani az eszköz előkészítését, kattintson az **Előkészítés leállítása** gombra. A szállítás előkészítését később folytathatja.
        
    ![A szállítás előkészítése 2](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. A szállítás előkészítése elindul, és az eszközmegosztások offline állapotba kerülnek. <!--You see a reminder to download the shipping label once the device is ready.--> Az eszköz előkészítésének befejezésekor az eszköz állapota *Szállításra kész* értékre vált, és a rendszer eszköz zárolja az eszközt.
        
    ![A szállítás előkészítése 3](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    Ha több adatot szeretne átmásolni az eszközre, feloldhatja az eszköz zárolását, további adatokat másolhat, és ismét futtathatja a szállítás előkészítését.

    Ha ebben a lépésben hibák léptek fel, az állapot a következőre frissül: *A vizsgálat hibákkal fejeződött be*. Oldja fel az eszköz zárolását, majd lépjen a **Csatlakozás és másolás** oldalra, töltse le a hibalistát, és hárítsa el a hibákat.

    ![A szállítás előkészítése 1](media/data-box-prepare-to-ship/prepare-to-ship4.png)

    A hibák megoldása után futtassa **A szállítás előkészítését**.

4. A szállítás előkészítésének sikeres (hibák nélküli) befejezése után kövesse az alábbi lépéseket:

    1. Jegyezze fel a befejezés hivatkozási számát. Attól függően, hogy melyik országban tartózkodik, szükség lehet erre a számra a különböző műveletekhez.
    2. Töltse le az ebben a folyamatban lemásolt fájlok listáját (más néven a jegyzéket). Később ezen lista alapján ellenőrizheti az Azure-ba feltöltött fájlokat. További információ: [Anyagjegyzékfájlok vizsgálata a szállítás előkészítése során](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship).
        
        ![A szállítás előkészítése 1](media/data-box-prepare-to-ship/prepare-to-ship5.png)
    3. Válassza ki és töltse le az eszköz szállítási utasításait. A szállítási utasítások eltérnek attól függően, hogy melyik országban tartózkodik.
    4. Ha az E-ink kijelzőn nem jelenik meg a fuvarlevélcímke, innen letöltheti a visszaszállítási címkét. 

5. Állítsa le az eszközt. A **Shut down or restart** (Leállítás vagy újraindítás) lapon kattintson a **Shut down** (Leállítás) elemre. Ha a rendszer megerősítést kér, kattintson az **OK** gombra a folytatáshoz.

6. Távolítsa el a kábeleket. A következő lépés az eszköz elküldése a Microsoftnak.
