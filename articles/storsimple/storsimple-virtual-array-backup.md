---
title: A Microsoft Azure StorSimple Virtual Array biztonsági mentési oktatóanyag |} A Microsoft Docs
description: Ismerteti, hogyan készíthet biztonsági másolatot a StorSimple Virtual Array megosztások és köteteket.
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
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121978"
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>Biztonsági mentést megosztások vagy kötetek a StorSimple Virtual Array

## <a name="overview"></a>Áttekintés

A StorSimple Virtual Array hibrid felhőalapú tárolási a helyszíni virtuális eszköz egy fájlkiszolgáló vagy iSCSI-kiszolgáló konfigurálható. A virtuális tömb lehetővé teszi a felhasználóknak ütemezett és manuális biztonsági mentéseket a megosztások vagy kötetek az eszközön. Konfigurált egy fájlkiszolgálót, ha lehetővé teszi az elemszintű helyreállítás. Ez az oktatóanyag leírja, hogyan hozhat létre ütemezett és manuális biztonsági mentéseket, és visszaállítani egy törölt fájl a virtuális tömb az elemszintű helyreállítás végrehajtásához.

Ebben az oktatóanyagban a StorSimple Virtual Arrayt csak vonatkozik. Információk a 8000-es sorozat, [8000-es sorozatú eszköz biztonsági másolatának létrehozása](storsimple-manage-backup-policies-u2.md)

## <a name="back-up-shares-and-volumes"></a>Megosztások és kötetek biztonsági mentése

Biztonsági másolatok időponthoz védelmet biztosíthat, javítja a helyreállíthatóságot és -megosztásokat és -kötetek visszaállítása idejét minimalizálása érdekében. A StorSimple eszközén kétféle módon lehet biztonsági másolatot készíteni egy fájlmegosztás vagy kötet: **Ütemezett** vagy **manuális**. A módszer a következő szakaszokban a következő cikkben.

## <a name="change-the-backup-start-time"></a>Módosítsa a biztonsági mentés kezdő időpontja

> [!NOTE]
> Ebben a kiadásban ütemezett biztonsági mentések hoznak létre egy alapértelmezett szabályzatot, amely egy megadott időpontban naponta fut, és készít biztonsági másolatot a megosztások vagy kötetek az eszközön. Nem alkalmas jelenleg az egyéni házirendeket az ütemezett biztonsági mentések létrehozásához.


A StorSimple Virtual Array rendelkezik, amely a megadott időszak (22:30) indul, és biztonsági másolatot készít a megosztások vagy kötetek az eszközön naponta egyszer alapértelmezett biztonsági mentési szabályzat. Módosíthatja az idő, amikor a biztonsági mentés elindul, de a gyakoriságát és a megőrzési ideje (amely a biztonsági másolatok megőrzése ennyi) nem módosítható. Során ezeket a biztonsági másolatokat készíteni a teljes virtuális eszközt. Ez sikerült potenciálisan hatással az eszköz teljesítményére, és hatással az eszközön telepített munkaterhelésekhez. Ezért azt javasoljuk, hogy úgy ütemezze ezeket a biztonsági másolatokat csúcsidőn.

 Ha módosítani szeretné az alapértelmezett biztonsági mentés kezdő időpontja, hajtsa végre a következő lépéseket a [az Azure portal](https://portal.azure.com/).

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>A kezdési időpont az alapértelmezett biztonsági mentési házirend módosítása

1. Lépjen a **eszközök**. A StorSimple-Eszközkezelő szolgáltatásban regisztrált eszközök jelennek meg. 
   
    ![Keresse meg az eszközök](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. Válassza ki, majd kattintson az eszköz. A **beállítások** panelen jelennek meg. Lépjen a **kezelés > biztonsági mentési házirendek**.
   
    ![Válassza ki az eszközt](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. Az a **biztonsági mentési házirendek** panelen, az alapértelmezett kezdési ideje 22:30. Az új kezdési időt a napi ütemezés az eszköz időzónájában is megadhat.
   
    ![Keresse meg a biztonsági mentési házirendek](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. Kattintson a **Save** (Mentés) gombra.

### <a name="take-a-manual-backup"></a>Manuális biztonsági mentés készítése

Ütemezett biztonsági mentések mellett akkor is manuális biztonsági mentést (igény szerinti) eszköz adatok bármikor.

#### <a name="to-create-a-manual-backup"></a>Manuális biztonsági mentés létrehozása

1. Lépjen a **eszközök**. Válassza ki az eszközt, és kattintson a jobb gombbal **...**  a kijelölt sor jobb szélén. A helyi menüből válassza ki a **biztonsági mentés**.
   
    ![Keresse meg a biztonsági mentés](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. Az a **biztonsági mentés** panelen kattintson a **biztonsági mentés**. Ez lesz biztonsági mentés, a fájlkiszolgáló minden megosztás vagy az iSCSI-kiszolgáló minden kötetéről. 
   
    ![biztonsági mentés indítása](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    Egy igény szerinti biztonsági mentés elindul, és láthatja, hogy a biztonsági mentési feladat elindult.
   
    ![biztonsági mentés indítása](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    A feladat sikeres befejezése után, újból értesítést. Ezután elindítja a biztonsági mentési folyamat.
   
    ![biztonsági mentési feladat létrehozása](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. A biztonsági másolatokat az előrehaladását úgy követheti nyomon, és tekintse meg a feladat részleteit, kattintson az értesítésre. Ezzel továbblép **feladat részletei**.
   
     ![biztonsági mentési feladat részleteit](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. A biztonsági mentés befejezése után lépjen **felügyeleti > biztonságimásolat-katalógus**. Az eszközén látni fogja az összes megosztások (vagy kötet) felhőbeli pillanatképet.
   
    ![Befejeződött a biztonsági mentés](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>Meglévő biztonsági másolatok megtekintéséhez
A meglévő biztonsági másolatok megtekintéséhez hajtsa végre az alábbi lépéseket az Azure Portalon.

#### <a name="to-view-existing-backups"></a>A meglévő biztonsági másolatok megtekintéséhez

1. Lépjen a **eszközök** panelen. Válassza ki, majd kattintson az eszköz. Az a **beállítások** panelen lépjen a **felügyeleti > biztonságimásolat-katalógus**.
   
    ![Keresse meg a biztonságimásolat-katalógus](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. Adja meg a szűréshez használandó a következő feltételeknek:
   
   - **Időtartomány** – lehet **elmúlt 1 óra**, **elmúlt 24 óra**, **elmúlt 7 nap**, **az elmúlt 30 napban**, **elmúlt év** , és **egyéni dátum**.
    
   - **Eszközök** – válassza ki a listából a fájlkiszolgálók vagy iSCSI-kiszolgálót regisztrálta a StorSimple-Eszközkezelő szolgáltatásban.
   
   - **Kezdeményezett** – automatikusan lehet **ütemezett** (a biztonsági mentési szabályzat) vagy **manuálisan** kezdeményező (,).
   
     ![Biztonsági mentések szűrése](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. Kattintson az **Alkalmaz** gombra. A biztonsági mentések a szűrt lista jelenik meg a **biztonságimásolat-katalógus** panelen. Megjegyzés csak 100 biztonsági mentési elemek egy adott időben megjeleníthetők.
   
    ![Frissített biztonságimásolat-katalógus](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>További lépések

Tudjon meg többet [felügyelete a StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

