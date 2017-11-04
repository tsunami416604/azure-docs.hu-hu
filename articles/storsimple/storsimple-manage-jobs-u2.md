---
title: "Megtekintheti és kezelheti a StorSimple feladatok |} Microsoft Docs"
description: "A StorSimple Manager szolgáltatás feladatok lapján, és nyomon követheti az újabb, aktuális és ütemezett biztonsági mentési feladatok ismerteti."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: cdd3e9e8-7ccd-40a3-8c07-0ab1338c0e59
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 6df1b27ce76de7a781ecc40af8430114d80b20d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-jobs-update-2"></a>A StorSimple Manager szolgáltatás segítségével megtekintheti és kezelheti a StorSimple feladatok (2. frissítés)
[!INCLUDE [storsimple-version-selector-manage-jobs](../../includes/storsimple-version-selector-manage-jobs.md)]

## <a name="overview"></a>Áttekintés
A **feladatok** lap egy egyetlen központi portált biztosít a megtekintése, és a StorSimple Manager szolgáltatás eszközök elindított feladatok kezelése kapcsolódik. Több eszköz ütemezett, futó, befejezett, törölt és sikertelen feladatok tekintheti meg. Eredmények táblázatos formában jelennek meg. 

![Feladatok lap](./media/storsimple-manage-jobs-u2/jobs.png)

Gyorsan megtalálhatja a feladatok olyan mezőkön, például a szűréssel érdeklődik:

* **Állapot** – futtathat feladatokat, befejeződött, megszakított, sikertelen, zároló vagy befejeződött, hibákkal.
* **A kezdő és a** – feladatok a dátum és idő tartomány alapján szűrhetők.
* **Típus** – a feladat típusa biztonsági másolat, lehet manuális biztonsági mentés, a visszaállítási, a klónozás, a eszköz feladatátvevő helyileg rögzített kötet létrehozásához, módosításához a kötetet, frissítés, támogatja a csomagot, vagy a virtuális eszköz kiépítése.
* **Eszközök** – feladatok kezdeményezett kapcsolódik a szolgáltatáshoz egy adott eszközön.
  A szűrt feladatok majd megjelennének alapján a következő attribútumokat:
  
  * **Típus** – biztonsági másolat, manuális biztonsági mentés, a visszaállítási, a klónozás, a eszköz feladatátvevő helyileg rögzített kötet létrehozásához, módosításához a kötetet, frissítés, támogatja a csomagot, vagy a virtuális eszköz kiépítése.
  * **Állapot** – futó, befejeződött, megszakított, sikertelen, zároló vagy befejeződött, hibákkal.
  * **Entitás** – a feladat társíthatók a kötet, hogy a biztonsági mentési házirend, vagy eszköz. Például a Klónozás feladat társítva egy köteten, mivel a biztonsági mentési házirend ütemezett biztonsági mentési feladat tartozik. Eszköz feladat jön létre a vészhelyreállítás (DR) vagy a visszaállítási művelet miatt.
  * **Eszköz** – a név az eszköz, amelyen a feladat elindult.
  * **Megkezdődött a** – az idő, amikor a feladat elindult.
  * **Folyamatban lévő** – egy futó feladat befejezésére százalékos aránya. A projekt mindig legyen 100 %.

A feladatok 30 másodpercenként frissül.

Ezen a lapon a következő feladathoz kapcsolódó műveleteket hajthatja végre:

* Feladatok részleteinek megjelenítése
* Feladatok megszakítása

## <a name="view-job-details"></a>Feladatok részleteinek megjelenítése
A következő lépésekkel minden feladat részleteinek megtekintéséhez.

#### <a name="to-view-job-details"></a>Feladat részleteinek megtekintése
1. Az a **feladatok** lapon, a megfelelő szűrőket és egy lekérdezés futtatásával érdeklődik feladat(ok) jelenít meg. Kereshet befejezése után fut, és nem vonható vissza a feladatokat.
2. Jelölje ki a feladatot.
3. Kattintson a lap alján **részletek**.
4. Az a **biztonsági mentési feladat részleteit** párbeszédpanel, megtekintheti az állapot, a részletek, a vonatkozó adatok és a statisztikai adatokat.
   
    ![Feladat részletei lap](./media/storsimple-manage-jobs-u2/JobDetails.png)

## <a name="cancel-a-job"></a>Feladatok megszakítása
A következő lépésekkel egy futó feladatot megszakítja.

> [!NOTE]
> Nem lehet megszakítani az egyes feladatok, például a kötet módosításához kötet módosításával, vagy a kötet kiterjesztését.
> 
> 

### <a name="to-cancel-a-job"></a>A feladatok megszakítása
1. Az a **feladatok** lapon, a futó feladat, amely megfelelő szűrőket és egy lekérdezés futtatásával megszakítja jelenít meg.
2. Jelölje ki a feladatot.
3. Kattintson a lap alján **Mégse**.
4. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra.

Ez a feladat most megszakítva.

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [a StorSimple biztonsági mentési házirendek kezelése](storsimple-manage-backup-policies.md).
* Megtudhatja, hogyan [felügyelete a StorSimple eszközt a StorSimple Manager szolgáltatás segítségével](storsimple-manager-service-administration.md).

