---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 4fc92931979aa367bdead435c3d6fd758d66a397
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889448"
---
#### <a name="to-create-a-manual-backup"></a>Manuális biztonsági mentés létrehozása

1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az eszközök táblázatos listájából válassza ki az eszközt. Lépjen a **Beállítások > Kezelés > Biztonsági mentési házirendek** részhez.

2. A **Biztonsági mentési házirendek** panelen táblázatos formában látható az összes biztonsági mentési házirend, beleértve az arra a kötetre vonatkozó házirendet, amelyről a biztonsági mentést el szeretné készíteni. Válassza ki az azon kötethez tartozó házirendet, amelyről biztonsági másolatot szeretne készíteni, majd kattintson a jobb gombbal a helyi menü megnyitásához. A legördülő listában kattintson a **Biztonsági másolat készítése** parancsra.

    ![Manuális biztonsági mentés létrehozása](./media/storsimple-8000-create-manual-backup/createmanualbu1.png)

3. Hajtsa végre a következő lépéseket a **Biztonsági másolat készítése** panelen:

    1. Válassza ki a megfelelő **Pillanatképtípust** a legördülő listából: **Helyi** pillanatkép vagy **felhőalapú** pillanatkép. Gyors biztonsági mentéshez és visszaállításhoz válassza a helyi pillanatképet, az adatrugalmassághoz pedig a felhőbeli pillanatképet.

        ![Manuális biztonsági mentés létrehozása](./media/storsimple-8000-create-manual-backup/createmanualbu2.png)

    2. Kattintson az **OK** gombra a pillanatképet létrehozó feladat elindításához. A feladat sikeres létrejöttét a lap tetején megjelenő értesítés jelzi.

        ![Manuális biztonsági mentés létrehozása](./media/storsimple-8000-create-manual-backup/createmanualbu4.png)

    3. A feladat figyeléséhez kattintson az értesítésre. Ezzel továbblép a **Feladatok** panelre, ahol megtekintheti a feladat állapotát.


5. Ha a biztonsági mentési feladat befejeződött, nyissa meg a **Biztonságimásolat-katalógus** lapot.

6. Állítsa be a szűrőket úgy, hogy a megfelelő eszköz, biztonsági mentési házirend és időtartomány jelenjen meg. Ezután a biztonsági mentés megjelenik a katalógusban szereplő biztonságimentés-készletek listájában.

