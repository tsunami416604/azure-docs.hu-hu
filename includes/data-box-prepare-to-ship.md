---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/07/2019
ms.author: alkohli
ms.openlocfilehash: c7e5231650ec1afb97a72ec0cf26cb8f80088b63
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54440445"
---
Az utolsó lépés az eszköz szállításának előkészítése. Ebben a lépésben az összes eszközmegosztást offline állapotba helyezi. Ez a folyamat elindítását követően, a megosztások nem érhetők el.

> [!IMPORTANT]
> Szállításra való szükség, ahogy azt a megőrzendő tartalomként jelöli, amely nem felel meg az Azure elnevezési konvencióinak adatokat. Ez a lépés kihagyása lehetséges adatokat eredményez is feltölthet hibák miatt nem megerősítése adatokat.

1. Lépjen a **Prepare to ship** (Szállításra való előkészítés) oldalra, majd kattintson a **Start preparation** (Előkészítés indítása) elemre. Alapértelmezés szerint az ellenőrzőösszegek kiszámítására beágyazva, a szállítás előkészítése során kerül sor. Az ellenőrzőösszeg kiszámítása órát is igénybe vehet attól függően, hogy az adatok mérete nap. 
   
    ![A szállítás előkészítése 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    Ha bármilyen okból az eszköz előkészítése le szeretné, kattintson a **előkészítés leállítása**. A későbbi szállításra való előkészítés folytathatja.
        
    ![2 szállításra való előkészítése](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. A szállításra való előkészítés elindul, és az eszköz megosztások kapcsolat nélküli módba. Megjelenik egy emlékeztető az levélcímke letöltése, ha az eszköz készen áll.

    ![Töltse le a szállítási címkét emlékeztető](media/data-box-prepare-to-ship/download-shipping-label-reminder.png)

3. Az eszköz állapota Aktívról *szállításra kész* és az eszköz zárolva van, az eszköz előkészítése befejeződése után.
        
    ![3 szállításra való előkészítése](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    Ha szeretne további adatok másolása az eszközön, akkor az eszköz feloldásához, további adatok másolása, és futtassa újra a szállításra való előkészítése.

    Ha hiba van ebben a lépésben szüksége lesz a hibanapló letöltéséhez, és javítsa ki a hibákat. Ha a hibák megoldódnak, futtassa **szállításra való**.

4. Miután a szállításra való előkészítés sikeresen befejeződött (a hibák), töltse le a fájlok (más néven a jegyzékfájl) a folyamat másolja. Később ezen lista alapján ellenőrizheti az Azure-ba feltöltött fájlokat.
        
    ![A szállítás előkészítése 1](media/data-box-prepare-to-ship/prepare-to-ship4.png)

5. Állítsa le az eszközt. A **Shut down or restart** (Leállítás vagy újraindítás) lapon kattintson a **Shut down** (Leállítás) elemre. Ha a rendszer megerősítést kér, kattintson az **OK** gombra a folytatáshoz.

6. Távolítsa el a kábeleket. A következő lépés az eszköz elküldése a Microsoftnak.
