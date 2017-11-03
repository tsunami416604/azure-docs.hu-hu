---
title: "A StorSimple-kötet visszaállítása biztonsági másolatból |} Microsoft Docs"
description: "A StorSimple Manager szolgáltatás biztonságimásolat-katalógus lapján a StorSimple-kötet visszaállítása egy biztonságimásolat-készlet használatát ismerteti."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: b979782e-3184-4465-ad5f-e4516a5885d2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 12484338f5b4d489604d70a657ef0992b6267297
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>A StorSimple-kötet visszaállítása egy biztonságimásolat-készlet
[!INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Áttekintés
A **biztonságimásolat-katalógus** lap megjeleníti a biztonsági mentés, amely jönnek létre, ha manuális vagy automatikus biztonsági mentés készül. Ezen a lapon a biztonsági mentési házirend, vagy egy kötetet, jelölje be a biztonsági másolatok felsorolása, vagy törölje a biztonsági mentések, vagy a biztonsági másolat használatával állítsa vissza vagy egy kötet klónozása.

 ![Biztonságimásolat-katalógus lap](./media/storsimple-restore-from-backup-set/HCS_BackupCatalog.png)

Ez az oktatóanyag bemutatja, hogyan használja a **biztonságimásolat-katalógus** lapot visszaállítani az eszközön a kötet a biztonságimásolat-készletből.

## <a name="how-to-use-the-backup-catalog"></a>A biztonsági mentési katalógus használata
A **biztonságimásolat-katalógus** lap biztosít egy lekérdezést, amely segít a megadásával szűkíthető, a biztonsági másolat beállítása. A biztonságimásolat-készletek, amelyek a rendszer beolvassa a következő paraméterek alapján szűrheti:

* **Eszköz** – az eszköz, amelyre a biztonságimásolat-készlet létrehozása.
* **Biztonsági mentési házirend** vagy **kötet** – a biztonsági mentési házirend, vagy a kötet a biztonságimásolat-készlet társítva.
* **A** és **való** – a dátum és idő tartomány, a biztonságimásolat-készlet létrehozásakor.

A szűrt biztonsági mentés majd megjelennének a következő attribútumok alapján:

* **Név** – a biztonsági mentési házirend, vagy a kötet a biztonságimásolat-készlet társított nevét.
* **Méret** – a biztonságimásolat-készlet tényleges méretének.
* **A létrehozott** – a dátum és idő, amikor a biztonsági mentés készült. 
* **Típus** – biztonsági másolat beállítása helyi pillanatképeket és felhőalapú pillanatfelvételek. Egy helyi pillanatfelvétel minden kötet adatait az eszközön, helyileg tárolt biztonsági másolatot, mivel egy felhőalapú pillanatfelvétel hivatkozik a felhőben tárolt kötet adatok biztonsági mentését. Helyi pillanatképeket gyorsabb hozzáférést biztosít, mivel felhőalapú pillanatfelvételek az adatrugalmasság közül választ.
* **Által kezdeményezett** – a biztonsági mentések kezdeményezheti automatikusan ütemezés szerint vagy manuálisan egy felhasználó által. (Használhatja a biztonsági mentési házirend ütemezett biztonsági mentéseket. Másik lehetőségként használhatja a **biztonsági másolatok készítéséhez** egy interaktív biztonsági mentést a lehetőség.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>A StorSimple-kötet visszaállítása egy biztonsági másolatból
Használhatja a **biztonságimásolat-katalógus** a StorSimple-kötet biztonsági másolatból történő visszaállítását egy adott oldal. 

> [!WARNING]
> Egy biztonsági másolatból történő visszaállítását a rendszer lecseréli a meglévő kötetet a biztonsági mentésből. Ez után a biztonsági mentés készült írt adatok elvesztésével járhat.
> 
> 

Egy olyan köteten a visszaállítási elindítása előtt győződjön meg arról, hogy a kötet offline állapotban. A kötet offline érvénybe, ha a gazdagép első szüksége lesz, majd az eszközről. Kövesse a [kötet offline állapotba](storsimple-manage-volumes.md#take-a-volume-offline). A következő lépésekkel vissza egy kötet a biztonságimásolat-készletből.

### <a name="to-restore-from-a-backup-set"></a>Vissza a biztonságimásolat-készletből
1. A StorSimple Manager szolgáltatás lapján kattintson a **biztonságimásolat-katalógus** fülre.
   
    ![Biztonságimásolat-katalógus](./media/storsimple-restore-from-backup-set/HCS_Restore.png)
2. Válassza ki a biztonságimásolat-készletet a következőképpen:
   
   1. Válassza ki a megfelelő eszközt.
   2. A legördülő listában válassza ki szeretne biztonsági másolatot az kötet vagy a biztonsági mentési házirend kiválasztásához.
   3. Adja meg azt az időtartományt.
   4. Kattintson a pipa ikonra ![pipa ikon](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png) a lekérdezés végrehajtásához.
      
      A biztonsági mentések társított a kijelölt kötetről, vagy a biztonsági mentési házirend megjelenjen-e a biztonságimásolat-készletek listájában.
3. Bontsa ki a biztonságimásolat-készletet a társított kötetek megtekintéséhez. Ezek a kötetek offline állapotba kell helyezni a gazdagépet és az eszköz visszaállítani őket. Kövesse a [kötet offline állapotba](storsimple-manage-volumes.md#take-a-volume-offline).
   
   > [!IMPORTANT]
   > Győződjön meg arról, hogy elvégezte a kötetek offline állapotba a gazdagépen, mielőtt az eszköz a kötetek offline állapotba. Ha a gazdagép nem hajtja végre a kötetek offline, potenciálisan előfordulhat, hogy sérült adatokat.
   > 
   > 
4. Válassza ki a biztonságimásolat-készletből. Kattintson a **visszaállítása** az oldal alján.
5. Megerősítést kér bekéri. 
   
    ![Jóváhagyás lap](./media/storsimple-restore-from-backup-set/HCS_ConfirmRestore.png)
6. Tekintse át a helyreállítási adatokat, és kattintson a pipa ikonra ![pipa ikon](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png). Ez fogja elindítani egy helyreállítási feladatot, amely elérésével megtekintheti a **feladatok** lap. 
7. A visszaállítás befejezése után ellenőrizheti, hogy a köteteken a biztonsági mentésből kötetek helyett.

![Videó elérhető](./media/storsimple-restore-from-backup-set/Video_icon.png)**Videó elérhető**

Bemutató videó bemutatja, hogyan használhatja a Klónozás és szolgáltatások a StorSimple törölt fájlok visszaállítása, kattintson a [Itt](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [kezelése StorSimple-köteteket](storsimple-manage-volumes.md).
* Megtudhatja, hogyan [felügyelete a StorSimple eszközt a StorSimple Manager szolgáltatás segítségével](storsimple-manager-service-administration.md).

