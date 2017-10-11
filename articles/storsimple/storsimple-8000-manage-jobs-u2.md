---
title: "Megtekintheti és kezelheti a StorSimple 8000 series feladataihoz |} Microsoft Docs"
description: "Útmutatást nyújt a StorSimple Device Manager szolgáltatás feladatok panelen és nyomon követheti az újabb, aktuális és ütemezett biztonsági mentési feladatok."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: bf8038b7171053b75eeb9aed88bff4246e65a8a8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>A StorSimple Device Manager szolgáltatás segítségével megtekintheti és kezelheti a feladatok (Update 3 és újabb verziók)

## <a name="overview"></a>Áttekintés
A **feladatok** panel egy egyetlen központi portált biztosít a megtekintése, és a StorSimple Device Manager szolgáltatáshoz való kapcsolódás az eszközökön az elindított feladatok kezelése. Több eszköz ütemezett, futó, befejezett, törölt és sikertelen feladatok tekintheti meg. Eredmények táblázatos formában jelennek meg.

![Feladatok panelen](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

Gyorsan megtalálhatja a feladatok olyan mezőkön, például a szűréssel érdeklődik:

* **Állapot** – feladatok lehet folyamatban, sikeresen befejeződött, megszakított, sikertelen, zároló vagy sikeres, a hibák.
* **Időtartománynak** – feladatok a dátum és idő tartomány alapján szűrhetők. A tartomány: 1 óra, az elmúlt 24 óra múlva elmúlt 7 nap elmúlt 30 napban, elmúlt év vagy egyéni dátuma múltbeli.
* **Típus** – a feladattípus lehet ütemezett biztonsági mentést, a manuális biztonsági mentés, a biztonsági mentés visszaállítási, kötet klónozása, kötettárolók feladatátvételt, helyileg rögzített kötet létrehozása, módosítása kötet, telepítse a frissítéseket, támogatási gyűjtését és felhő készülék létrehozása.
* **Eszközök** – feladatok kezdeményezett kapcsolódik a szolgáltatáshoz egy adott eszközön.
  
A szűrt feladatok majd megjelennének alapján a következő attribútumokat:
  
* **Név** – ütemezett biztonsági mentést, manuális biztonsági mentés, visszaállítás biztonsági mentés, klónozás köteten, a feladatátvétel kötettárolók, helyileg rögzített kötet létrehozása, módosítása a kötet, telepítse a frissítéseket, támogatási gyűjtését, vagy hozzon létre felhő készülék.
* **Állapot** – futó, befejeződött, megszakított, sikertelen, zároló vagy befejeződött, hibákkal.
* **Entitás** – a feladat társíthatók a kötet, hogy a biztonsági mentési házirend, vagy eszköz. Például a Klónozás feladat társítva egy köteten, mivel a biztonsági mentési házirend ütemezett biztonsági mentési feladat tartozik. Eszköz feladat jön létre a vészhelyreállítás (DR) vagy a visszaállítási művelet miatt.
* **Eszköz** – a név az eszköz, amelyen a feladat elindult.
* **Megkezdődött a** – az idő, amikor a feladat elindult.
* **Időtartam** – a feladat végrehajtásához szükséges időt.

A feladatok 30 másodpercenként frissül.

Ezen a lapon a következő feladathoz kapcsolódó műveleteket hajthatja végre:

* Feladat részleteinek megtekintése
* Feladatok megszakítása

## <a name="view-job-details"></a>Feladat részleteinek megtekintése
A következő lépésekkel minden feladat részleteinek megtekintéséhez.

#### <a name="to-view-job-details"></a>Feladat részleteinek megtekintése
1. Nyissa meg a StorSimple eszköz Manager szolgáltatáshoz, és kattintson a **feladatok**.

2. Az a **feladatok** panelen, a megfelelő szűrőket és egy lekérdezés futtatásával érdeklődik feladat(ok) megjelenítése. Kereshet befejezése után fut, és nem vonható vissza a feladatokat.

    ![Feladat panelen](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Válassza ki, majd kattintson egy feladatra.

    ![Feladat panelen](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. A feladat részleteit megjelenítő panelen megtekintheti a állapota, részleteit, idő statisztikák és adatok statisztika.
   
    ![Feladat részletei](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Feladatok megszakítása
A következő lépésekkel egy futó feladatot megszakítja.

> [!NOTE]
> Nem lehet megszakítani az egyes feladatok, például a kötet módosításához kötet módosításával, vagy a kötet kiterjesztését.


### <a name="to-cancel-a-job"></a>A feladatok megszakítása
1. Az a **feladatok** lapon, a futó feladat, amely megfelelő szűrőket és egy lekérdezés futtatásával megszakítja jelenít meg. Jelölje ki a feladatot.

2. Kattintson a jobb gombbal a kiválasztott feladat meghívása a helyi menüt, és kattintson a **Mégse**.

    ![Feladat részletei](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra. Ez a feladat most megszakítva.

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [a StorSimple biztonsági mentési házirendek kezelése](storsimple-8000-manage-backup-policies-u2.md).
* Megtudhatja, hogyan [felügyelete a StorSimple eszközt a StorSimple Device Manager szolgáltatással](storsimple-8000-manager-service-administration.md).

