---
title: "Azure virtuális gépek biztonsági mentése |} Microsoft Docs"
description: "Ismerje meg, regisztrálja, és a recovery services-tároló az Azure virtuális gépek mentésére."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "virtuális gép biztonsági mentése; Készítsen biztonsági másolatot a virtuális gép; biztonsági mentés és katasztrófa utáni helyreállítás; ARM virtuális gép biztonsági mentése"
ms.assetid: 5c68481d-7be3-4e68-b87c-0961c267053e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/15/2017
ms.author: trinadhk;jimpark;markgal;
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 40983a3de104238d09b976b5fcf2419da42c1bba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-azure-virtual-machines-to-a-recovery-services-vault"></a>Azure-beli virtuális gépek biztonsági mentése Recovery Services-tárolóba
> [!div class="op_single_selector"]
> * [Recovery Services-tároló virtuális gépek mentésére](backup-azure-arm-vms.md)
> * [Biztonsági másolatot a virtuális gépek mentési tárolóba](backup-azure-vms.md)
>
>

Ez a cikk részletesen biztonsági mentése Azure virtuális gépeken (telepített Resource Manager és klasszikus telepített) a Recovery Services-tároló. Virtuális gépek biztonsági mentéséről a munka nagyobb része a előkészítése. Előtt készítsen biztonsági másolatot, vagy egy virtuális gép védelmét, végre kell hajtania a [Előfeltételek](backup-azure-arm-vms-prepare.md) készítse fel a környezetet a virtuális gépek védelmére. Miután befejezte az előfeltételeket, majd is kezdeményezhető a pillanatképek készítése a virtuális gép biztonsági mentési művelet.


[!INCLUDE [learn about backup deployment models](../../includes/backup-deployment-models.md)]

További információkért tekintse meg a cikkek [az Azure virtuális gép biztonsági mentési infrastruktúrájának megtervezésével](backup-azure-vms-introduction.md) és [Azure virtuális gépek](https://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="triggering-the-backup-job"></a>A biztonsági mentési feladat időt.
A Recovery Services-tároló társított biztonsági mentési házirend határozza meg, mikor és milyen gyakran fut-e a biztonsági mentési műveletet. Alapértelmezés szerint az első ütemezett biztonsági mentés a kezdeti biztonsági másolatot. A kezdeti biztonsági mentés végrehajtásáig a **Biztonsági mentési feladatok** panelen az Utolsó biztonsági mentés állapota **Figyelmeztetés (kezdeti biztonsági mentés folyamatban)** állapotú.

![Biztonsági mentés függőben](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

Hacsak a kezdeti biztonsági mentés kezdete nem a nagyon közeli jövőben van, érdemes futtatni a **Biztonsági másolat készítése** lehetőséget. Az alábbi eljárást a tároló irányítópult elindul. Ez az eljárás szolgálja ki a kezdeti biztonsági mentési feladat futtatásához szükséges előfeltételek maradéktalanul befejezését követően. Ha már futtatta a kezdeti biztonsági mentési feladat, ez az eljárás nem érhető el. A társított biztonsági mentési házirend határozza meg a következő biztonsági mentési feladat.  

A kezdeti biztonsági mentési feladat futtatása:

1. A tároló irányítópultján kattintson a **Biztonsági mentési elemek** szakaszban található számra, vagy kattintson a **Biztonsági mentési elemek** csempére. <br/>
  ![Beállítások ikon](./media/backup-azure-vms-first-look-arm/rs-vault-config-vm-back-up-now-1.png)

  Megnyílik a **Biztonsági mentési elemek** panel.

  ![Elemek biztonsági mentése](./media/backup-azure-vms-first-look-arm/back-up-items-list.png)

2. A **Biztonsági mentési elemek** panelen válassza ki az elemet.

  ![Beállítások ikon](./media/backup-azure-vms-first-look-arm/back-up-items-list-selected.png)

  Megnyílik a **Biztonsági mentési elemek** listája. <br/>

  ![Biztonsági mentési feladat elindul](./media/backup-azure-vms-first-look-arm/backup-items-not-run.png)

3. A **Biztonsági mentési elemek** listában kattintson a három pontra **...** a helyi menü megnyitásához.

  ![Helyi menü](./media/backup-azure-vms-first-look-arm/context-menu.png)

  Megjelenik a Helyi menü.

  ![Helyi menü](./media/backup-azure-vms-first-look-arm/context-menu-small.png)

4. A Helyi menüben kattintson a **Biztonsági mentés** elemre.

  ![Helyi menü](./media/backup-azure-vms-first-look-arm/context-menu-small-backup-now.png)

  Megnyílik a Biztonsági mentés panel.

  ![a Biztonsági mentés panel képe](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

5. A Biztonsági mentés panelen kattintson a naptár ikonra, használja a naptárvezérlőt annak kiválasztására, hogy meddig kívánja megőrizni a helyreállítási pontot, majd kattintson a **Biztonsági mentés** elemre.

  ![adja meg az utolsó napot, ameddig Biztonsági mentés helyreállítási pontját meg kívánja őrizni](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  Az üzembehelyezési értesítések értesítik, hogy a biztonsági mentési feladat elindult, és hogy a feladat állapotát a Biztonsági mentési feladatok oldalon figyelheti. A virtuális gép méretétől függően a kezdeti biztonsági mentés létrehozása hosszabb időt vehet igénybe.

6. A kezdeti biztonsági mentés állapotának megtekintéséhez vagy nyomon követéséhez a tároló irányítópultjának **Biztonsági mentési feladatok** csempéjén kattintson a **Folyamatban** elemre.

  ![Biztonsági mentési feladatok csempe](./media/backup-azure-vms-first-look-arm/open-backup-jobs-1.png)

  Megnyílik a Biztonsági mentési feladatok panel.

  ![Biztonsági mentési feladatok csempe](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view-1.png)

  A **Biztonsági mentési feladatok** panelen megtekintheti az összes feladat állapotát. Ellenőrizze, hogy a virtuális gép biztonsági mentése folyamatban van-e, vagy már befejeződött. Amikor a biztonsági mentési feladat befejeződött, az állapota *Befejezve* lesz.

  > [!NOTE]
  > A biztonsági mentési művelet részeként az Azure Backup szolgáltatás egy parancsot ad minden virtuális gépre a biztonsági mentési bővítménynek, hogy ürítsen ki minden írást, és készítsen egy egységes pillanatképet.
  >
  >

## <a name="troubleshooting-errors"></a>Kapcsolatos hibák elhárítása
Ha biztonsági során problémákat tapasztal a virtuális gép, tekintse meg a [VM hibaelhárítási cikke](backup-azure-vms-troubleshoot.md) segítségét.

## <a name="next-steps"></a>Következő lépések
Most, hogy a virtuális gép védetté, tekintse meg a Virtuálisgép-felügyeleti feladatokat, és hogyan lehet visszaállítani a virtuális gépek a következő cikkeket.

* [A virtuális gépek kezelése és figyelése](backup-azure-manage-vms.md)
* [Virtuális gépek visszaállítása](backup-azure-arm-restore-vms.md)
