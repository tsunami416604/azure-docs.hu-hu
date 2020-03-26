---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: f5b60d862be0d71f71f770c47d88ad39f2fc6ac7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "66419572"
---
Az utolsó lépés az eszköz szállításának előkészítése. Ebben a lépésben az összes eszközmegosztást offline állapotba helyezi. A folyamat megkezdése után a megosztásokhoz nem lehet hozzáférni.

> [!IMPORTANT]
> El kell végezni a szállítás előkészítését, mert ez megjelöli az Azure elnevezési konvencióinak nem megfelelő adatokat. A lépés kihagyása a nem megfelelő adatok miatt adatfeltöltési hibákat eredményezhet.

1. Lépjen a **Prepare to ship** (Szállításra való előkészítés) oldalra, majd kattintson a **Start preparation** (Előkészítés indítása) elemre. Alapértelmezés szerint az ellenőrzőösszegek kiszámítására az adatok másolása során kerül sor. A szállítás előkészítése kiszámítja az ellenőrzőösszeget, és létrehozza a fájlok listáját (más néven az *anyagjegyzékfájlokat* vagy a jegyzéket). Az ellenőrzőösszeg kiszámítása az adatok mennyiségétől függően órákig vagy napokig is tarthat.
   
    ![A szállítás előkészítése 1](media/data-box-heavy-prepare-to-ship/prepare-to-ship1.png)

    Ha valamiért le szeretné állítani az eszköz előkészítését, kattintson az **Előkészítés leállítása** gombra. A szállítás előkészítését később folytathatja.
        
    ![A szállítás előkészítése 2](media/data-box-heavy-prepare-to-ship/prepare-to-ship2.png)
    
2. A szállítás előkészítése elindul, és az eszközmegosztások offline állapotba kerülnek. Ekkor megjelenik egy üzenet, amely emlékezteti, hogy töltse le a fuvarlevélcímkét, amint az eszköz elkészült.

    ![Fuvarlevélcímke letöltésére vonatkozó emlékeztető](media/data-box-heavy-prepare-to-ship/download-shipping-label-reminder.png)

3. Az eszköz előkészítésének befejezésekor az eszköz állapota *Szállításra kész* értékre vált, és a rendszer eszköz zárolja az eszközt.
        
    ![A szállítás előkészítése 3](media/data-box-heavy-prepare-to-ship/prepare-to-ship3.png)

    Ha több adatot szeretne átmásolni az eszközre, feloldhatja az eszköz zárolását, további adatokat másolhat, és ismét futtathatja a szállítás előkészítését.

    Ha ebben a lépésben hibák léptek fel, töltse le a naplófájlt, és hárítsa el a hibákat. A hibák megoldása után futtassa **A szállítás előkészítését**.

4. A szállítás előkészítésének sikeres (hibák nélküli) elvégzése után töltse le a folyamat során másolt fájlok listáját (más néven az *anyagjegyzékfájlokat* vagy a jegyzéket). 

    ![A fájllista vagy az anyagjegyzékfájlok letöltése](media/data-box-heavy-prepare-to-ship/download-list-of-files.png)

   Később ezen lista alapján ellenőrizheti az Azure-ba feltöltött fájlokat. További információ: [Anyagjegyzékfájlok vizsgálata a szállítás előkészítése során](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship).
        
    ![Minta anyagjegyzékfájl](media/data-box-heavy-prepare-to-ship/sample-bom-file.png)

5. Állítsa le az eszközt. A **Shut down or restart** (Leállítás vagy újraindítás) lapon kattintson a **Shut down** (Leállítás) elemre. Ha a rendszer megerősítést kér, kattintson az **OK** gombra a folytatáshoz.

    ![Az első eszközcsomópont leállítása](media/data-box-heavy-prepare-to-ship/shut-down-device-node.png)

6. Ismételje meg az összes fenti lépést a többi eszközcsomóponton.
7. Az eszköz teljes leállítása után az eszköz hátulján lévő összes LED-nek kikapcsolva kell lennie. A következő lépés az összes kábel eltávolítása és az eszköz elküldése a Microsoftnak.
