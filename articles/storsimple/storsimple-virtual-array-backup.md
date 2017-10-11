---
title: "A Microsoft Azure StorSimple virtuális tömb biztonsági mentési oktatóanyag |} Microsoft Docs"
description: "Ismerteti a StorSimple virtuális tömb megosztások és a kötetek biztonsági mentése."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: e3cdcd9e-33b1-424d-82aa-b369d934067e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c926f0c80ce56cac3106ad97ec3ec2e18a8e2cc6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="back-up-shares-or-volumes-on-your-storsimple-virtual-array"></a>A StorSimple virtuális tömb biztonsági másolatot a megosztások vagy kötetek

## <a name="overview"></a>Áttekintés

A StorSimple virtuális egy hibrid felhő helyszíni virtuális tárolóeszköz, konfigurálhat egy fájl vagy iSCSI-kiszolgáló. A virtuális tömb lehetővé teszi, hogy a felhasználó az eszköz ütemezett és manuális biztonsági mentést a megosztások vagy kötetek létrehozására. Amikor egy fájl-kiszolgálóként konfigurált lehetővé teszi elemszintű helyreállítás. Ez az oktatóanyag ismerteti, hogyan ütemezett és manuális biztonsági mentés létrehozásához, és visszaállítani a virtuális tömb törölt fájlok elemszintű helyreállítás.

Ez az oktatóanyag a StorSimple virtuális tömbök csak vonatkozik. 8000 Series információkért látogasson el [8000 sorozat eszköz biztonsági mentés létrehozása](storsimple-manage-backup-policies-u2.md)

## <a name="back-up-shares-and-volumes"></a>Megosztások és a kötetek biztonsági mentése

Biztonsági mentések időpontban védelmet, javítja a helyreállíthatóságot és megosztásokat és -kötetek visszaállítása idejének minimalizálása érdekében. Biztonsági másolatot készíthet egy fájlmegosztás vagy kötet StorSimple eszközén kétféle módon: **ütemezett** vagy **manuális**. A módszer a következő szakaszban tárgyalt.

## <a name="change-the-backup-start-time"></a>Módosítsa a biztonsági mentés kezdési ideje

> [!NOTE]
> Ebben a kiadásban ütemezett biztonsági mentések hozza létre az alapértelmezett házirend, amely egy megadott időpontban naponta fut, és készít biztonsági másolatot a megosztások vagy kötetek az eszközön. Nincs lehetőség egyéni házirendeket az ütemezett biztonsági mentések jelenleg létrehozásához.


A StorSimple virtuális tömb tartozik egy alapértelmezett biztonsági mentési házirend, amely egy megadott időpontot (22:30) kezdődik, és biztonsági másolatot készít a megosztások vagy kötetek az eszközön naponta egyszer. Módosíthatja az idő, ahol a biztonsági mentés elindul, de a gyakoriságát és a megőrzés (amely azt adja meg a megőrizni kívánt biztonsági mentéseinek számát) nem módosítható. A biztonsági mentése során a virtuális eszköz teljes biztonsági másolat. Ez sikerült potenciálisan hatással lehet az eszköz teljesítményét, és hatással lehet az eszközre telepített munkaterhelésekhez. Ezért azt javasoljuk, hogy ezek a biztonsági mentések ütemezése kevesen.

 Ha módosítani szeretné az alapértelmezett biztonsági mentés kezdési ideje, hajtsa végre a következő lépéseket a [Azure-portálon](https://portal.azure.com/).

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>A kezdési idejét az alapértelmezett biztonsági mentési házirend módosítása

1. Ugrás a **eszközök**. A StorSimple Device Manager szolgáltatásban regisztrált eszközök listája jelenik meg. 
   
    ![Keresse meg az eszközökre](./media/storsimple-virtual-array-backup/changebuschedule1.png)

2. Jelölje ki, majd kattintson az eszköz. A **beállítások** panel fog megjelenni. Ugrás a **kezelése > biztonsági mentési házirendek**.
   
    ![az eszköz kiválasztásához](./media/storsimple-virtual-array-backup/changebuschedule2.png)

3. Az a **biztonsági mentési házirendek** panelen az alapértelmezett kezdő érték 22:30. Eszköz időzóna is megadhat a napi ütemezés új kezdési idejét.
   
    ![Navigáljon a biztonsági mentési házirendek](./media/storsimple-virtual-array-backup/changebuschedule5.png)

4. Kattintson a **Save** (Mentés) gombra.

### <a name="take-a-manual-backup"></a>Manuális biztonsági mentés készítése

Ütemezett biztonsági mentések mellett végre (igény) kézi biztonsági másolatot az eszközön tárolt adatok bármikor.

#### <a name="to-create-a-manual-backup"></a>Manuális biztonsági mentés létrehozása

1. Ugrás a **eszközök**. Válassza ki azt az eszközt, és kattintson a jobb gombbal **...**  a kijelölt sor jobb szélén. Válassza ki a helyi menüből **biztonsági másolatok készítéséhez**.
   
    ![Keresse meg a biztonsági másolatok készítéséhez](./media/storsimple-virtual-array-backup/takebackup1m.png)

2. Az a **biztonsági másolatok készítéséhez** panelen kattintson a **biztonsági másolatok készítéséhez**. Ez biztonsági másolatot készít a fájlkiszolgálón a megosztásokat vagy az iSCSI-kiszolgálón a köteteket. 
   
    ![biztonsági mentés indítása](./media/storsimple-virtual-array-backup/takebackup2m.png)
   
    Egy igény szerinti biztonsági mentés elindul, és láthatja, hogy egy biztonsági mentési feladat elindult.
   
    ![biztonsági mentés indítása](./media/storsimple-virtual-array-backup/takebackup3m.png) 
   
    Ha a feladat sikeresen befejeződött, értesítés jelenik meg újra. Ezután elindítja a biztonsági mentési folyamat.
   
    ![Biztonsági mentési feladat létrehozása](./media/storsimple-virtual-array-backup/takebackup4m.png)

3. A biztonsági másolatokat az előrehaladását úgy követheti nyomon, és nézze meg a feladat részleteit, kattintson az értesítés. Ehhez szükséges, hogy **feladat részletei**.
   
     ![biztonsági mentési feladat részleteit](./media/storsimple-virtual-array-backup/takebackup5m.png)

4. A biztonsági mentés befejezése után, nyissa meg a **felügyeleti > biztonságimásolat-katalógus**. Az eszközön megjelenik egy felhő-pillanatfelvételt összes megosztások (vagy kötet).
   
    ![Befejezett biztonsági mentése](./media/storsimple-virtual-array-backup/takebackup19m.png) 

## <a name="view-existing-backups"></a>Meglévő biztonsági másolatok megtekintése
A meglévő biztonsági másolatok megtekintéséhez a következő lépésekkel az Azure portálon.

#### <a name="to-view-existing-backups"></a>Meglévő biztonsági másolatok megtekintése

1. Ugrás a **eszközök** panelen. Jelölje ki, majd kattintson az eszköz. Az a **beállítások** paneljén lépjen **felügyeleti > biztonságimásolat-katalógus**.
   
    ![Navigáljon a biztonságimásolat-katalógus](./media/storsimple-virtual-array-backup/viewbackups1.png)
2. Adja meg a szűréshez használandó a következő feltételeknek:
   
    - **Időtartománynak** – lehet **elmúlt 1 óra**, **elmúlt 24 óra**, **elmúlt 7 napban**, **az elmúlt 30 napban**, **múltbeli évre** , és **egyéni dátum**.
    
    - **Eszközök** – válassza ki a fájlkiszolgálók vagy iSCSI-kiszolgálókhoz a StorSimple Device Manager szolgáltatásban regisztrált listájáról.
   
    - **Kezdeményezett** – automatikusan lehet **ütemezett** (által a biztonsági mentési házirend) vagy **manuálisan** kezdeményeznie (,).
   
    ![Biztonsági mentések szűrése](./media/storsimple-virtual-array-backup/viewbackups2.png)

3. Kattintson az **Alkalmaz** gombra. A biztonsági mentések szűrt listája megjelenik a **biztonságimásolat-katalógus** panelen. Megjegyzés: csak 100 biztonsági mentési elemek megjelenítése egy adott időpontban.
   
    ![Frissített biztonságimásolat-katalógus](./media/storsimple-virtual-array-backup/viewbackups3.png)

## <a name="next-steps"></a>Következő lépések

További információ [felügyelete a StorSimple virtuális tömb](storsimple-ova-web-ui-admin.md).

