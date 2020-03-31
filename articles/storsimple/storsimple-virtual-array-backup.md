---
title: Microsoft Azure StorSimple virtual array biztonsági mentési oktatóanyag | Microsoft dokumentumok
description: A StorSimple virtual array megosztások és kötetek biztonsági kapcsolatának ismertetése.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: e3cdcd9e-33b1-424d-82aa-b369d934067e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a61dcca1f78b6ba444a2deefcf6b8bb4fd5c5087
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60581299"
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>Megosztások vagy kötetek biztonsági másolatot kell kapnia a StorSimple virtuális tömbön

## <a name="overview"></a>Áttekintés

A StorSimple virtuális tömb egy helyszíni hibrid felhőbeli tárolóeszköz, amely fájlkiszolgálóként vagy iSCSI-kiszolgálóként konfigurálható. A virtuális tömb lehetővé teszi a felhasználó számára, hogy ütemezett és manuális biztonsági mentéseket hozzon létre az eszközön lévő összes megosztásról vagy kötetről. Ha fájlkiszolgálóként van konfigurálva, elemszintű helyreállítást is lehetővé tesz. Ez az oktatóanyag bemutatja, hogyan hozhat létre ütemezett és manuális biztonsági másolatokat, és hogyan hajthatja végre az elemszintű helyreállítást a virtuális tömb törölt fájljának visszaállításához.

Ez az oktatóanyag csak a StorSimple virtuális tömbökre vonatkozik. A 8000-es sorozattal kapcsolatos információkért látogasson el a [8000-es sorozatú eszköz biztonsági másolatának létrehozása című](storsimple-manage-backup-policies-u2.md)

## <a name="back-up-shares-and-volumes"></a>Részvények és mennyiségek biztonsági és biztonsági tartaléka

A biztonsági mentések biztosítják az időponthoz elegendő időt, javítják a helyreállíthatóságot, és minimálisra csökkentik a megosztások és kötetek visszaállítási idejét. A StorSimple-eszközön kétféleképpen lehet biztonsági másolatot és kötetről biztonsági **másolatot** kapni: Ütemezett vagy **Kézi**. A módszerek mindegyikét a következő szakaszok ismertetik.

## <a name="change-the-backup-start-time"></a>A biztonsági mentés kezdési idejének módosítása

> [!NOTE]
> Ebben a kiadásban az ütemezett biztonsági mentések egy alapértelmezett házirend által jönnek létre, amely naponta fut egy megadott időpontban, és biztonsági másolatot készít az eszközön lévő összes megosztásról vagy kötetről. Jelenleg nem lehet egyéni házirendeket létrehozni az ütemezett biztonsági mentések hez.


A StorSimple virtuális tömb rendelkezik egy alapértelmezett biztonsági mentési szabályzattal, amely egy adott napszakban (22:30) kezdődik, és naponta egyszer biztonsági másolatot készít az eszközön lévő összes megosztásról vagy kötetről. Módosíthatja a biztonsági mentés kezdetének idejét, de a gyakoriság és a megőrzési adatszolgáltatás (amely megadja a megőrzendő biztonsági mentések számát) nem módosítható. A biztonsági mentések során a teljes virtuális eszközről biztonsági másolat készül. Ez potenciálisan hatással lehet az eszköz teljesítményére, és hatással lehet az eszközön üzembe helyezett számítási feladatokra. Ezért azt javasoljuk, hogy ezeket a biztonsági mentéseket csúcsidőn kívüli időszakokra ütemezze.

 Az alapértelmezett biztonsági mentési kezdési időpont módosításához hajtsa végre a következő lépéseket az [Azure Portalon.](https://portal.azure.com/)

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Az alapértelmezett biztonsági mentési házirend kezdési idejének módosítása

1. Nyissa meg az **Eszközök**lehetőséget. Megjelenik a StorSimple Eszközkezelő szolgáltatásban regisztrált eszközök listája. 
   
    ![navigálás az eszközökre](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. Válassza ki és kattintson az eszközre. Megjelenik a **Beállítások** panel. Nyissa meg **a > biztonsági mentési házirendek kezelése lehetőséget.**
   
    ![válassza ki a készüléket](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. A **Biztonsági mentési házirendek** panelen az alapértelmezett kezdési időpont 22:30. Megadhatja a napi ütemezés új kezdési idejét az eszköz időzónájában.
   
    ![navigálás a biztonsági mentési házirendekhez](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. Kattintson a **Mentés** gombra.

### <a name="take-a-manual-backup"></a>Kézi biztonsági mentés készítése

Az ütemezett biztonsági mentések mellett bármikor készíthet manuális (igény szerinti) biztonsági másolatot az eszközadatokról.

#### <a name="to-create-a-manual-backup"></a>Manuális biztonsági mentés létrehozása

1. Nyissa meg az **Eszközök**lehetőséget. Válassza ki a készüléket, és kattintson a jobb gombbal **...** a jobb szélen a kiválasztott sorban. A helyi menüben válassza a **Biztonsági mentés végrehajtása parancsot.**
   
    ![navigálással készítsen biztonsági mentést](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. A **Biztonsági mentés készítése** panelen kattintson a **Biztonsági mentés folytatása gombra.** Ezzel biztonsági másolatot készít a fájlkiszolgáló összes megosztásátvagy az iSCSI-kiszolgáló összes kötetét. 
   
    ![biztonsági mentés indítása](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    Egy igény szerinti biztonsági mentés elindul, és láthatja, hogy egy biztonsági mentési feladat elindult.
   
    ![biztonsági mentés indítása](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    A feladat sikeres befejezése után ismét értesítést kap. Ekkor elindul a biztonsági mentési folyamat.
   
    ![biztonsági mentési feladat létrehozva](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. A biztonsági mentések előrehaladásának nyomon követéséhez és a feladat részleteinek megnézetéhez kattintson az értesítésre. Ez **elviszi a Munka részleteit**.
   
     ![biztonsági mentési feladat részletei](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. A biztonsági mentés befejezése után nyissa meg **a Felügyeleti > biztonsági másolat katalógust.** Az eszközön található összes megosztás (vagy kötet) felhőbeli pillanatképe jelenik meg.
   
    ![Befejeződött biztonsági mentés](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>Meglévő biztonsági mentések megtekintése
A meglévő biztonsági mentések megtekintéséhez hajtsa végre a következő lépéseket az Azure Portalon.

#### <a name="to-view-existing-backups"></a>Meglévő biztonsági mentések megtekintése

1. Nyissa meg az **Eszközök** panelt. Válassza ki és kattintson az eszközre. A **Beállítások** panelen nyissa meg **a Felügyeleti > biztonsági másolat katalógusát.**
   
    ![Navigálás a biztonsági másolat katalógusára](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. Adja meg a szűréshez használandó alábbi feltételeket:
   
   - **Időtartomány** – lehet **elmúlt 1 óra**, Elmúlt **24 óra**, Elmúlt 7 **nap**, **Elmúlt 30 nap**, Előző **év**és **Egyéni dátum**.
    
   - **Eszközök** – válasszon a StorSimple Eszközkezelő szolgáltatásban regisztrált fájlkiszolgálók vagy iSCSI-kiszolgálók listájából.
   
   - **Kezdeményezett** – lehet automatikusan **ütemezett** (egy biztonsági házirend) vagy **manuálisan** kezdeményezett (az Ön által).
   
     ![Biztonsági mentések szűrése](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. Kattintson az **Alkalmaz** gombra. A biztonsági mentések szűrt listája megjelenik a **Biztonsági másolat katalógus** panelen. Megjegyzés: egy adott időpontban csak 100 biztonsági mentési elem jeleníthető meg.
   
    ![Frissített biztonsági másolat katalógusa](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>További lépések

További információ [a StorSimple virtuális tömb felügyeletéről.](storsimple-ova-web-ui-admin.md)

