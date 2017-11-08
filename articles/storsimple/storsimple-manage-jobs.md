---
title: "Megtekintheti és kezelheti a StorSimple feladatok |} Microsoft Docs"
description: "A StorSimple Manager szolgáltatás feladatok lapján, és nyomon követheti az újabb, aktuális és ütemezett biztonsági mentési feladatok ismerteti."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 55922cd0-d490-48eb-938a-012a67c1c09e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 426fd3ec76157670c9d192f007adacc4284abb42
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-jobs"></a>A StorSimple Manager szolgáltatás segítségével megtekintheti, és a StorSimple-feladatok kezelése
> [!NOTE]
> A klasszikus portál StorSimple elavult. A StorSimple eszköz kezelői automatikusan áthelyezi a érvénytelenítése ütemezés szerint az új Azure-portálon. Kapni fog egy e-mailek és a portál értesítései az áthelyezés. Ez a dokumentum hamarosan is kell vonni. Az áthelyezés kapcsolatos kérdéseivel lásd: [– gyakori kérdések: áthelyezése az Azure-portálon](storsimple-8000-move-azure-portal-faq.md).

[!INCLUDE [storsimple-version-selector-manage-jobs](../../includes/storsimple-version-selector-manage-jobs.md)]

## <a name="overview"></a>Áttekintés
A **feladatok** lap egy egyetlen központi portált biztosít a megtekintése, és a StorSimple Manager szolgáltatás eszközök elindított feladatok kezelése kapcsolódik. Több eszköz ütemezett, futó, befejezett, és a sikertelen feladatok tekintheti meg. Eredmények táblázatos formában jelennek meg.

![Feladatok lap](./media/storsimple-manage-jobs/HCS_JobsPage.png)

Gyorsan megtalálhatja a feladatok olyan mezőkön, például a szűréssel érdeklődik:

* **Állapot** – futtathat feladatokat, ütemezett, sikertelen, befejezett, zároló vagy visszavont.
* **Típus** – feladatok is létrehozható egy ütemezett vagy egy igény szerinti biztonsági mentést (**érvénybe biztonsági mentési**), a klónozás, eszköz-visszaállítási vagy frissítési művelet.
* **Eszközök** – feladatok kezdeményezett kapcsolódik a szolgáltatáshoz egy adott eszközön.
* **A kezdő és a** – feladatok a dátum és idő tartomány alapján szűrhetők.

A szűrt feladatok majd megjelennének alapján a következő attribútumokat:

* **Típus** – biztonsági másolat, klónozás, visszaállítási, feladatátvétel vagy frissítés.
* **Állapot** – futó, ütemezett, sikertelen, befejeződött, megszakítása vagy megszakítva.
* **Entitás** – a feladat társíthatók a kötet, hogy a biztonsági mentési házirend, vagy eszköz. A Klónozás feladat tartozik egy köteten, mivel a biztonsági mentési házirend társítva egy ütemezett biztonsági mentési feladat. Eszköz feladat jön létre a vészhelyreállítás (DR) vagy a visszaállítási művelet miatt.
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

## <a name="cancel-a-job"></a>Feladatok megszakítása
A következő lépésekkel egy futó feladatot megszakítja.

### <a name="to-cancel-a-job"></a>A feladatok megszakítása
1. Az a **feladatok** lapon, a futó feladat, amely megfelelő szűrőket és egy lekérdezés futtatásával megszakítja jelenít meg.
2. Jelölje ki a feladatot.
3. Kattintson a lap alján **Mégse**.
4. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra.

Ez a feladat most megszakítva.

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [a StorSimple biztonsági mentési házirendek kezelése](storsimple-manage-backup-policies.md).
* Megtudhatja, hogyan [felügyelete a StorSimple eszközt a StorSimple Manager szolgáltatás segítségével](storsimple-manager-service-administration.md).

