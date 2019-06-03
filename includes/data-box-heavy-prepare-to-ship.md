---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: f5b60d862be0d71f71f770c47d88ad39f2fc6ac7
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66419572"
---
Az utolsó lépés az eszköz szállításának előkészítése. Ebben a lépésben az összes eszközmegosztást offline állapotba helyezi. Ez a folyamat elindítását követően, a megosztások nem érhetők el.

> [!IMPORTANT]
> Szállításra való szükség, ahogy azt a megőrzendő tartalomként jelöli, amely nem felel meg az Azure elnevezési konvencióinak adatokat. Ez a lépés kihagyása lehetséges adatokat eredményez is feltölthet hibák miatt nem megfelelő adatokat.

1. Lépjen a **Prepare to ship** (Szállításra való előkészítés) oldalra, majd kattintson a **Start preparation** (Előkészítés indítása) elemre. Alapértelmezés szerint ellenőrzőösszegek számítja ki közben az adatok másolásakor. Szállításra való az ellenőrzőösszeg kiszámítása befejeződött, és létrehozza a fájlok listáját (más néven *AJ fájlok* vagy a jegyzékfájl). Az ellenőrzőösszeg kiszámítása órát is igénybe vehet attól függően, hogy az adatok mérete nap.
   
    ![A szállítás előkészítése 1](media/data-box-heavy-prepare-to-ship/prepare-to-ship1.png)

    Ha bármilyen okból az eszköz előkészítése le szeretné, kattintson a **előkészítés leállítása**. A későbbi szállításra való előkészítés folytathatja.
        
    ![2 szállításra való előkészítése](media/data-box-heavy-prepare-to-ship/prepare-to-ship2.png)
    
2. A szállításra való előkészítés elindul, és az eszköz megosztások kapcsolat nélküli módba. Megjelenik egy emlékeztető az levélcímke letöltése, ha az eszköz készen áll.

    ![Töltse le a szállítási címkét emlékeztető](media/data-box-heavy-prepare-to-ship/download-shipping-label-reminder.png)

3. Az eszköz állapota Aktívról *szállításra kész* és az eszköz zárolva van, az eszköz előkészítése befejeződése után.
        
    ![3 szállításra való előkészítése](media/data-box-heavy-prepare-to-ship/prepare-to-ship3.png)

    Ha szeretne további adatok másolása az eszközön, akkor az eszköz feloldásához, további adatok másolása, és futtassa újra a szállításra való előkészítése.

    Ha ebben a lépésben hibák, töltse le a hibanaplóban, és javítsa ki a hibákat. Ha a hibák megoldódnak, futtassa **szállításra való**.

4. A szállításra való előkészítés után van letöltése sikeresen befejeződött (a hibák), a fájlok listáját (más néven *AJ fájlok* vagy a jegyzékfájl) a folyamat másolja. 

    ![Töltse le a fájlokat vagy AJ fájlok listája](media/data-box-heavy-prepare-to-ship/download-list-of-files.png)

   Később ezen lista alapján ellenőrizheti az Azure-ba feltöltött fájlokat. További információkért lásd: [szállításra való előkészítés AJ vizsgálja meg a fájlok](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship).
        
    ![Mintafájl Anyagjegyzék](media/data-box-heavy-prepare-to-ship/sample-bom-file.png)

5. Állítsa le az eszközt. A **Shut down or restart** (Leállítás vagy újraindítás) lapon kattintson a **Shut down** (Leállítás) elemre. Ha a rendszer megerősítést kér, kattintson az **OK** gombra a folytatáshoz.

    ![Állítsa le az első eszköz csomópont](media/data-box-heavy-prepare-to-ship/shut-down-device-node.png)

6. Ismételje meg a fenti lépéseket az eszköz másik csomópontja.
7. Miután az eszköz teljesen le lett állítva, hátulján az eszközön található összes LED lenne van kapcsolva. A következő lépés, hogy távolítsa el az összes a kábelek, és a Microsoft-eszköz szállításra.
