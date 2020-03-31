---
title: A StorSimple 8000 sorozat feladatainak megtekintése és kezelése | Microsoft dokumentumok
description: Ez a témakör a StorSimple Device Manager szolgáltatás feladatok panel, és hogyan lehet használni a legutóbbi, aktuális és ütemezett biztonsági mentési feladatok nyomon követésére.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254871"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>Feladatok megtekintése és kezelése a StorSimple Eszközkezelő szolgáltatással (3. és újabb verzió)

## <a name="overview"></a>Áttekintés
A **Feladatok** panel egyetlen központi portált biztosít a StorSimple Eszközkezelő szolgáltatáshoz csatlakoztatott eszközökön elindított feladatok megtekintéséhez és kezeléséhez. Több eszköz ütemezett, futó, befejezett, megszakított és sikertelen feladatait tekintheti meg. Az eredmények táblázatos formátumban jelennek meg.

![Jobs penge](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

A következő mezők szűrésével gyorsan megtalálhatja az önt érdeklő feladatokat:

* **Állapot** – A feladatok lehetnek folyamatban, sikeresek, megszakíthatók, sikertelenek, megszakítások vagy hibák lehetnek.
* **Időtartomány** – A feladatok a dátum és az idő tartománya alapján szűrhetők. A tartományok elmúltak 1 óra, elmúlt 24 óra, elmúlt 7 nap, elmúlt 30 nap, elmúlt év vagy egyéni dátum.
* **Típus** – A feladat típusa lehet ütemezett biztonsági mentés, manuális biztonsági mentés, biztonsági mentés visszaállítása, klónozott kötet, feladatátvételi kötettárolók létrehozása, helyileg rögzített kötet létrehozása, kötet módosítása, frissítések telepítése, támogatási naplók gyűjtése és felhőalapú berendezés létrehozása.
* **Eszközök** – A feladatok a szolgáltatáshoz csatlakoztatott bizonyos eszközön jönnek létre.
  
A szűrt feladatok táblázatba foglalása a következő attribútumok alapján történik:
  
* **Név** – ütemezett biztonsági mentés, manuális biztonsági mentés, biztonsági mentés visszaállítása, klónozási kötet, kötettárolók létrehozása, helyileg rögzített kötet létrehozása, kötetek módosítása, frissítések telepítése, támogatási naplók gyűjtése vagy felhőalapú berendezés létrehozása.
* **Állapot** – futás, befejezett, megszakított, sikertelen, megszakítás vagy befejezett hibákkal.
* **Entitás** – A feladatok társíthatók egy kötethez, egy biztonsági mentési szabályzathoz vagy egy eszközhöz. Például egy klónfeladat egy kötethez van társítva, míg egy ütemezett biztonsági mentési feladat egy biztonsági mentési házirendhez van társítva. Az eszközfeladat vész-helyreállítási (DR) vagy visszaállítási művelet eredményeként jön létre.
* **Eszköz** – Annak az eszköznek a neve, amelyen a feladat elindult.
* **Started on** – A feladat elindításának időpontja.
* **Időtartam** – a feladat elvégzéséhez szükséges idő.

A feladatok listája 30 másodpercenként frissül.

Ezen az oldalon a következő, feladattal kapcsolatos műveleteket hajthatja végre:

* Feladatok részleteinek megjelenítése
* Feladat visszavonása

## <a name="view-job-details"></a>Feladatok részleteinek megjelenítése
Hajtsa végre az alábbi lépéseket bármely feladat részleteinek megtekintéséhez.

#### <a name="to-view-job-details"></a>Feladat részleteinek megtekintése
1. Nyissa meg a StorSimple Eszközkezelő szolgáltatást, és kattintson **a Feladatok**gombra.

2. A **Feladatok** panelen jelenítse meg az önt érdeklő feladatot(oka)t a megfelelő szűrőkkel rendelkező lekérdezés futtatásával. A befejezett, futó vagy megszakított feladatokat kereshet.

    ![Munka penge](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Jelöljön ki egy feladatot, és kattintson egy feladatra.

    ![Munka penge](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. A feladat részletei panelen megtekintheti az állapotot, a részleteket, az időstatisztikákat és az adatstatisztikákat.
   
    ![Feladat részletei](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Feladat visszavonása
Futtasa meg a következő lépéseket.

> [!NOTE]
> Egyes feladatok, például a kötet módosítása a kötettípus módosítása vagy a kötet bővítése, nem szakíthatók meg.


### <a name="to-cancel-a-job"></a>Feladat megszakítása
1. A **Feladatok** lapon jelenítse meg a törölni kívánt futó feladat(oka)t a megfelelő szűrőkkel rendelkező lekérdezés futtatásával. Válassza ki a feladatot.

2. Kattintson a jobb gombbal a kijelölt feladatra a helyi menü meghívásához, majd kattintson a **Mégse parancsra.**

    ![Feladat részletei](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra. A feladat most megszakadt.

## <a name="next-steps"></a>További lépések
* További információ [a StorSimple biztonsági mentési házirendjeikezeléséről.](storsimple-8000-manage-backup-policies-u2.md)
* Ismerje meg, hogyan [használhatja a StorSimple Eszközkezelő szolgáltatást a StorSimple-eszköz felügyeletére.](storsimple-8000-manager-service-administration.md)

