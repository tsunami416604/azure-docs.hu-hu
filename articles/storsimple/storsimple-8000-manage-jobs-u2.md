---
title: A StorSimple 8000 sorozat feladatok megtekintése és kezelése |} A Microsoft Docs
description: A StorSimple-Eszközkezelő szolgáltatás feladatok panelen, és nyomon követheti a legutóbbi, a jelenlegi és az ütemezett biztonsági mentési feladatok ismerteti.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 462f8dafdffa7ee01e6ccf7945a1abfdff90db42
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720693"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>A StorSimple-Eszközkezelő szolgáltatás segítségével (Update 3 és újabb verziók) feladatok megtekintése és kezelése

## <a name="overview"></a>Áttekintés
A **feladatok** panel egyetlen központi portált biztosít a megtekintéshez és az eszközökön az elindított feladatok kezelése a StorSimple-Eszközkezelő szolgáltatáshoz csatlakozik. Ütemezett, futó, befejezett, törölt és sikertelen feladatok több eszközön is megtekintheti. Eredmények táblázatos formátumban jelennek meg.

![Feladatok panelen](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

Gyorsan megtalálhatja a feladatok, például a mezők szűrése érdekli:

* **Állapot** – feladatok lehetnek folyamatban, sikeres, megszakítva, sikertelen, megszakítás alatt álló vagy sikeresen befejeződött, a hibákat.
* **Időtartomány** – feladatok a dátum és idő tartomány alapján szűrhetők. A tartományok van egy 1 óra, az elmúlt 24 óra, 7 nap, az elmúlt 30 napban, elmúlt év vagy egyéni dátuma múltbeli.
* **Típus** – a feladattípus lehet ütemezett biztonsági mentést, a manuális biztonsági mentés, visszaállítás biztonsági mentési, kötet klónozása, kötettárolók feladatainak átvétele, helyileg rögzített kötet létrehozása, a kötet módosításának, telepítse a frissítéseket, támogatási naplók gyűjtése és a felhőalapú berendezés létrehozása.
* **Eszközök** – feladatok kezdeményezett csatlakozik a szolgáltatáshoz egy adott eszközön.
  
A szűrt feladatok majd megjelennének alapján a következő attribútumokat:
  
* **Név** – ütemezett biztonsági mentést, manuális biztonsági mentés, visszaállítás biztonsági mentést, a klónozott kötet, kötettárolók feladatainak átvétele, helyileg rögzített kötet létrehozása, a kötet módosításának, telepítse a frissítéseket, támogatási naplók összegyűjtése vagy felhőalapú berendezés létrehozása.
* **Állapot** – futó, befejeződött, megszakított, sikertelen, megszakítás alatt álló vagy befejeződött, hibákkal.
* **Entitás** – lehet, hogy a feladatok egy köteten, a biztonsági mentési házirend vagy az eszköz társítva. Például egy klónozott feladat társítva egy kötetet, mivel a társított biztonsági mentési szabályzat ütemezett biztonsági mentési feladat. Egy eszköz feladat jön létre a vészhelyreállítás (DR) vagy a visszaállítási művelet miatt.
* **Eszköz** – az a név az eszköz, amelyen a feladat el lett indítva.
* **A lépések** – az idő, amikor a feladat el lett indítva.
* **Időtartam** – a feladat végrehajtásához szükséges időt.

A feladatok 30 másodpercenként frissül.

Ezen a lapon a következő feladatokkal kapcsolatos műveleteket hajthatja végre:

* Feladatok részleteinek megjelenítése
* Feladatok megszakítása

## <a name="view-job-details"></a>Feladatok részleteinek megjelenítése
Hajtsa végre az alábbi lépéseket az összes feladat részleteinek megtekintéséhez.

#### <a name="to-view-job-details"></a>Feladat részleteinek megtekintése
1. Keresse meg a StorSimple-Eszközkezelő szolgáltatást, és kattintson a **feladatok**.

2. Az a **feladatok** panelen egy lekérdezés futtatásával megfelelő szűrőket érdekelné feladat(ok) megjelenítéséhez. Kereshet befejeződött, fut, vagy a feladat megszakítva.

    ![Feladat panel](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Válassza ki, és kattintson egy feladatra.

    ![Feladat panel](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. A feladat részleteit megjelenítő panelen megtekintheti az állapotát, részletei, statisztikája és statisztikai adatok.
   
    ![Feladat részletei](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Feladatok megszakítása
Hajtsa végre az alábbi lépéseket egy futó feladatot megszakítja.

> [!NOTE]
> Nem lehet megszakítani az egyes feladatok, például egy köteten a kötet típusának módosításához módosítsa, vagy egy kötet bővítésében.


### <a name="to-cancel-a-job"></a>A feladat megszakítása
1. Az a **feladatok** lapon, és megjelenítheti a futó feladat, amely megszakítja a lekérdezés futtatásával megfelelő szűrőket. Válassza ki a feladatot.

2. Kattintson a jobb gombbal a kijelölt feladat indítja el a helyi menüt, és kattintson a **Mégse**.

    ![Feladat részletei](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra. Ez a feladat most már meg lett szakítva.

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [a StorSimple biztonsági mentési házirendek kezelése](storsimple-8000-manage-backup-policies-u2.md).
* Ismerje meg, hogyan [a StorSimple-eszköz felügyelete a StorSimple-Eszközkezelő szolgáltatás segítségével](storsimple-8000-manager-service-administration.md).

