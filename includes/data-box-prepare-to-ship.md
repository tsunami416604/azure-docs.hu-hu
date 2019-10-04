---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/25/2019
ms.author: alkohli
ms.openlocfilehash: 1d52117440028c75b249f469f2b3576c2ab1c5c5
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "67179783"
---
Az utolsó lépés az eszköz szállításának előkészítése. Ebben a lépésben az összes eszközmegosztást offline állapotba helyezi. A folyamat megkezdése után a megosztásokhoz nem lehet hozzáférni.

> [!IMPORTANT]
> El kell végezni a szállítás előkészítését, mert ez megjelöli az Azure elnevezési konvencióinak nem megfelelő adatokat. A lépés kihagyása a nem megfelelő adatok miatt adatfeltöltési hibákat eredményezhet.

1. Lépjen a **Prepare to ship** (Szállításra való előkészítés) oldalra, majd kattintson a **Start preparation** (Előkészítés indítása) elemre. Alapértelmezés szerint az ellenőrzőösszegek kiszámítására az adatok másolása során kerül sor. A szállítás előkészítése kiszámítja az ellenőrzőösszegeket, és létrehozza a fájlok listáját ( *anyagjegyzékfájlok*). Az ellenőrzőösszeg kiszámítása az adatok mennyiségétől függően órákig vagy napokig is tarthat. 
   
    ![A szállítás előkészítése 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    Ha valamiért le szeretné állítani az eszköz előkészítését, kattintson az **Előkészítés leállítása** gombra. A szállítás előkészítését később folytathatja.
        
    ![A szállítás előkészítése 2](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. A szállítás előkészítése elindul, és az eszközmegosztások offline állapotba kerülnek. Ekkor megjelenik egy üzenet, amely emlékezteti, hogy töltse le a fuvarlevélcímkét, amint az eszköz elkészült.

    ![Fuvarlevélcímke letöltésére vonatkozó emlékeztető](media/data-box-prepare-to-ship/download-shipping-label-reminder.png)

3. Az eszköz előkészítésének befejezésekor az eszköz állapota *Szállításra kész* értékre vált, és a rendszer eszköz zárolja az eszközt.
        
    ![A szállítás előkészítése 3](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    Ha több adatot szeretne átmásolni az eszközre, feloldhatja az eszköz zárolását, további adatokat másolhat, és ismét futtathatja a szállítás előkészítését.

    Ha ebben a lépésben hibák vannak, le kell töltenie a hibanaplót, és meg kell oldania a hibákat. A hibák megoldása után futtassa **A szállítás előkészítését**.

4. A szállítás előkészítésének sikeres (hibák nélküli) befejezése után töltse le az ebben a folyamatban lemásolt fájlok listáját (más néven a jegyzéket). Később ezen lista alapján ellenőrizheti az Azure-ba feltöltött fájlokat. További információ: [Anyagjegyzékfájlok vizsgálata a szállítás előkészítése során](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship).
        
    ![A szállítás előkészítése 1](media/data-box-prepare-to-ship/prepare-to-ship4.png)

5. Állítsa le az eszközt. A **Shut down or restart** (Leállítás vagy újraindítás) lapon kattintson a **Shut down** (Leállítás) elemre. Ha a rendszer megerősítést kér, kattintson az **OK** gombra a folytatáshoz.

6. Távolítsa el a kábeleket. A következő lépés az eszköz elküldése a Microsoftnak.
