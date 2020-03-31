---
title: A StorSimple biztonsági másolat katalógusának kezelése | Microsoft dokumentumok
description: A cikk a StorSimple Eszközkezelő szolgáltatás biztonsági másolata lapjának használatával a biztonságimásolat-készletek felsorolását, kijelölését és törlését ismerteti.
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
ms.openlocfilehash: 07d9e03f1631ebce88a7a7c2e33be62f21dda522
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60319671"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>A Biztonságimásolat-katalógus kezelése a StorSimple Eszközkezelő szolgáltatással
## <a name="overview"></a>Áttekintés
A StorSimple Eszközkezelő szolgáltatás **Biztonsági másolat katalógus** panel megjeleníti az összes biztonsági mentési készletek, amelyek akkor jönnek létre, amikor a kézi vagy ütemezett biztonsági mentések készülnek. Ezen a lapon felsorolhatja a biztonsági mentési házirend vagy kötet összes biztonsági mentését, kijelölheti vagy törölheti a biztonsági másolatokat, illetve biztonsági másolatot készíthet a kötet visszaállításához vagy klónozásához.

Ez az oktatóanyag bemutatja, hogyan lehet felsorolni, kiválasztani és törölni egy biztonságimentési készletet. Ha meg szeretné tudni, hogyan állíthatja vissza az eszközt a biztonsági mentésből, olvassa el [az Eszköz visszaállítása biztonsági másolatból lehetőséget.](storsimple-8000-restore-from-backup-set-u2.md) Ha meg szeretné tudni, hogyan klónozhat egy kötetet, olvassa el [a StorSimple-kötet klónozása](storsimple-8000-clone-volume-u2.md)című területet.

![Biztonsági másolat katalógusa](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

A **Biztonsági másolat katalógus** panel egy lekérdezést biztosít a biztonságimásolat-készlet kiválasztásának szűkítéséhez. A beolvasott biztonságimásolat-készletek a következő paraméterek alapján szűrhetők:

* **Eszköz** – Az az eszköz, amelyen a biztonsági másolat készlet készült.
* **Biztonsági mentési házirend vagy kötet** – A biztonsági mentési házirend vagy a biztonsági másolathoz társított kötet.
* **Kezdő és záró** – a biztonsági másolat készlet létrehozásának dátum- és időtartománya.

A szűrt biztonsági másolat-készletek táblázatba foglalása a következő attribútumok alapján lesznek táblázatba foglalva:

* **Név** – A biztonsági másolat házirendjének vagy kötetének neve a biztonságimásolat-készlethez társítva.
* **Méret** – A biztonságimásolat-készlet tényleges mérete.
* **Létrehozva –** A biztonsági mentések létrehozásának dátuma és időpontja. 
* **Típus** – Biztonsági másolat készletek lehetnek helyi pillanatképek vagy felhőbeli pillanatképek. A helyi pillanatkép az eszközön helyileg tárolt összes kötetadat biztonsági másolata, míg a felhőbeli pillanatkép a felhőben található kötetadatok biztonsági mentésére vonatkozik. A helyi pillanatképek gyorsabb hozzáférést biztosítanak, míg a felhőbeli pillanatképek az adatok rugalmasságához vannak kiválasztva.
* **Kezdeményezte** - A biztonsági mentések lehet automatikusan kezdeményezi az ütemezés vagy manuálisan a felhasználó. A biztonsági mentési házirend segítségével ütemezheti a biztonsági mentéseket. Másik lehetőségként használhatja a **Biztonsági mentés készítése** lehetőséget a manuális biztonsági mentéshez.

## <a name="list-backup-sets-for-a-backup-policy"></a>Biztonságimásolat-készítési házirend biztonságimásolat-készleteinek listázása
Hajtsa végre az alábbi lépéseket a biztonsági mentési házirend összes biztonsági mentésének listázásához.

#### <a name="to-list-backup-sets"></a>Biztonsági másolatkészítő készletek listázása
1. Nyissa meg a StorSimple Eszközkezelő szolgáltatást, és kattintson **a Katalógus biztonsági másolata**gombra.

2. A beállítások szűrése a következőképpen:
   
   1. Adja meg az időtartományt.
   2. Válassza ki a megfelelő eszközt.
   3. Szűrés **biztonsági mentési házirend szerint** a megfelelő biztonsági mentések megtekintéséhez.
   3. A biztonsági mentési házirend legördülő listájában válassza az **Összes** lehetőséget a kijelölt eszközön lévő összes biztonsági másolat megtekintéséhez.
   4. A lekérdezés végrehajtásához kattintson az **Alkalmaz** gombra.
      
      A kijelölt biztonsági mentési házirendhez társított biztonsági másolatnak meg kell jelennie a biztonságimásolat-készletek listájában.

      ![Ugrás a biztonsági másolat katalógusára](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>Biztonságimásolat-készlet kiválasztása
Hajtsa végre az alábbi lépéseket a kötet vagy biztonsági mentési házirend biztonsági másolatkészletének kiválasztásához.

#### <a name="to-select-a-backup-set"></a>Biztonságimásolat-készlet kiválasztása
1. Nyissa meg a StorSimple Eszközkezelő szolgáltatást, és kattintson **a Katalógus biztonsági másolata**gombra.
2. A beállítások szűrése a következőképpen:
   
   1. Adja meg az időtartományt. 
   2. Válassza ki a megfelelő eszközt. 
   3. A kijelölni kívánt biztonsági másolat kötet- vagy biztonságimentési házirendje szerint szűrje.
   4. A lekérdezés végrehajtásához kattintson az **Alkalmaz** gombra.
      
      A kijelölt kötethez vagy biztonsági mentési házirendhez társított biztonsági másolatnak meg kell jelennie a biztonságimásolat-készletek listájában.

      ![Ugrás a biztonsági másolat katalógusára](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Jelöljön ki és bontson ki egy biztonságimásolat-készletet. Most már láthatja a biztonsági mentési készleteket a benne lévő kötetek szerinti bontásban. A **Visszaállítás** és **a Törlés** beállítások a biztonságimásolat-készlet helyi menüjében (jobb gombbal) érhetők el. A kiválasztott biztonságimásolat-készleten a műveletek bármelyikét végrehajthatja.

    ![Ugrás a biztonsági másolat katalógusára](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>Biztonságimásolat-készlet törlése
Törölje a biztonsági másolatot, ha már nem kívánja megőrizni a hozzá társított adatokat. A biztonságimásolat-készlet törléséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-delete-a-backup-set"></a>Biztonságimásolat-készlet törlése
 Nyissa meg a StorSimple Eszközkezelő szolgáltatást, és kattintson **a Katalógus biztonsági másolata**gombra.
1. A beállítások szűrése a következőképpen:
   
   1. Adja meg az időtartományt. 
   2. Válassza ki a megfelelő eszközt. 
   3. A kijelölni kívánt biztonsági másolat kötet- vagy biztonságimentési házirendje szerint szűrje.
   4. A lekérdezés végrehajtásához kattintson az **Alkalmaz** gombra.
      
      A kijelölt kötethez vagy biztonsági mentési házirendhez társított biztonsági másolatnak meg kell jelennie a biztonságimásolat-készletek listájában.

      ![Ugrás a biztonsági másolat katalógusára](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

1. Jelöljön ki és bontson ki egy biztonságimásolat-készletet. Most már láthatja a biztonsági mentési készleteket a benne lévő kötetek szerinti bontásban. A **Visszaállítás** és **a Törlés** beállítások a biztonságimásolat-készlet helyi menüjében (jobb gombbal) érhetők el. Kattintson a jobb gombbal a kijelölt biztonságimásolat-készletre, és a helyi menüben válassza a **Törlés parancsot.**

    ![Ugrás a biztonsági másolat katalógusára](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

1. Amikor megerősítést kér, tekintse át a megjelenített információkat, és kattintson a **Törlés gombra.** A kijelölt biztonsági másolat véglegesen törlődik.

    ![Ugrás a biztonsági másolat katalógusára](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

1. Értesítést kap, ha a törlés folyamatban van, és ha sikeresen befejeződött. A törlés befejezése után frissítse a lekérdezést ezen a lapon. A törölt biztonságimásolat-készlet a továbbiakban nem jelenik meg a biztonságimásolat-készletek listájában.

    ![Ugrás a biztonsági másolat katalógusára](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>További lépések
* További információ arról, hogyan [állíthatja vissza az eszközt a biztonságimásolat-készítő készletből a biztonságimásolat-katalógus](storsimple-8000-restore-from-backup-set-u2.md)segítségével.
* Ismerje meg, hogyan [használhatja a StorSimple Eszközkezelő szolgáltatást a StorSimple-eszköz felügyeletére.](storsimple-8000-manager-service-administration.md)

