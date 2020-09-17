---
title: A vész-helyreállítás kezelése Azure NetApp Files régiók közötti replikációval | Microsoft Docs
description: Ismerteti, hogyan lehet a vész-helyreállítást Azure NetApp Files régiók közötti replikáció használatával kezelni.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: ad006279a656758ba856cd3f39c17b0410e525e6
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708788"
---
# <a name="manage-disaster-recovery-using-cross-region-replication"></a>Vész-helyreállítás kezelése a régiók közötti replikációval 

A forrás és a cél kötetek közötti folyamatos replikáció (lásd: [replikálási társítás létrehozása](cross-region-replication-create-peering.md)) előkészíti a vész-helyreállítási eseményre. 

Ilyen esemény esetén feladatátvételt hajthat végre [a cél kötetre](#break-replication-peering-to-activate-the-destination-volume), így az ügyfél olvasási és írási lehetőséget biztosít a célként megadott kötetnek. 

A vész-helyreállítás után visszatérhet a forrás kötethez egy [újraszinkronizálási művelettel](#resync-replication-to-reactivate-the-source-volume) , amely felülírja a forrás kötet adatát a cél kötet adataival.  Ezután újra [létre kell hoznia a forrás-cél replikálást](#reestablish-source-to-destination-replication) , és újra kell csatlakoztatnia az ügyfél számára a forrás-kötetet. 

A részleteket az alábbiakban ismertetjük. 

## <a name="break-replication-peering-to-activate-the-destination-volume"></a>Replikációs társítás megszakítása a cél kötet aktiválásához

Ha aktiválni kell a célként megadott kötetet (például ha feladatátvételt szeretne végezni a célhelyre), meg kell szüntetnie a replikálási társítást, majd csatlakoztatnia kell a célként megadott kötetet.  

1. A replikálási társítás megszakításához válassza ki a cél kötetet. Kattintson a **replikálás** lehetőségre a Storage szolgáltatás alatt.  

2.  A folytatás előtt keresse meg a következő mezőket:  
    * Győződjön meg arról, hogy a tükrözési állapot ***tükrözöttként***jelenik meg.   
        Ne kísérelje meg a replikálási társítások megszakítását, ha a tükrözés állapota nem *inicializált*.
    * Győződjön meg arról, hogy a kapcsolat állapota ***inaktív***.   
        Ne kísérelje meg a replikálási társítások megszakítását, ha a kapcsolat állapota *átadást*mutat.   

    Lásd: [a replikációs kapcsolat](cross-region-replication-display-health-status.md)állapotának megjelenítése. 

3.  Kattintson a társítás **megszakítása**elemre.  

4.  Ha a rendszer kéri, írja be az **Igen** értéket, és kattintson a **töréspont** gombra. 

    ![Replikációs társítás megszakítása](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)

5.  Csatlakoztassa a célként megadott kötetet a [Windows vagy Linux rendszerű virtuális gépekhez való csatlakoztatás vagy a kötet leválasztása](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)című témakör lépéseit követve.   
    Ez a lépés lehetővé teszi, hogy az ügyfél hozzáférjen a célként megadott kötethez.

## <a name="resync-replication-to-reactivate-the-source-volume"></a>Replikálás újraszinkronizálása a forrás kötet újraaktiválásához   

A vész-helyreállítás után újraaktiválhatja a forrás kötetet egy újraszinkronizálási művelet elvégezve.  Az újraszinkronizálási művelet megfordítja a replikációs folyamatot, és szinkronizálja az adatokat a cél kötetről a forrás kötetre.  

> [!IMPORTANT] 
> Az újraszinkronizálási művelet felülírja a forrás kötetre vonatkozó adatmennyiséget a célként megadott mennyiségű adattal.  A felhasználói felület figyelmezteti az adatvesztés lehetőségére. A rendszer kérni fogja, hogy erősítse meg az újraszinkronizálási műveletet a művelet elkezdése előtt.

1. A replikáció újraszinkronizálásához válassza ki a *forrás* kötetet. Kattintson a **replikálás** lehetőségre a Storage szolgáltatás alatt. Ezután kattintson az **Újraszinkronizálás**elemre.  

2. Ha a rendszer kéri, írja be az **Igen** értéket, és kattintson az **Újraszinkronizálás** gombra. 
 
    ![Replikáció újraszinkronizálása](../media/azure-netapp-files/cross-region-replication-resync-replication.png)

3. Figyelje a forrás kötet állapotát a [replikációs kapcsolat](cross-region-replication-display-health-status.md)állapotának megjelenítése a következő lépésekkel:.   
    Ha a forrás kötet állapota a következő értékeket jeleníti meg, az újraszinkronizálási művelet befejeződött, és a célként megadott köteten végrehajtott módosítások már a forrás köteten vannak rögzítve:   

    * Tükrözött állapot: *tükrözött*  
    * Átvitel állapota: *tétlen*  

## <a name="reestablish-source-to-destination-replication"></a>Forrás – cél replikálás helyreállítása

Miután befejeződött az újraszinkronizálási művelet a célhelyről a forrásra, meg kell szüntetnie a replikálási társítást a forrás – cél replikáció újbóli létrehozásához. A forrás kötetet is újra csatlakoztatnia kell, hogy az ügyfél hozzáférhessen.  

1. A replikálási társítás megszüntetése:  
    a. Válassza ki a *cél* kötetet. Kattintson a **replikálás** lehetőségre a Storage szolgáltatás alatt.  
    b. A folytatás előtt keresse meg a következő mezőket:   
    * Győződjön meg arról, hogy a tükrözési állapot ***tükrözöttként***jelenik meg.   
    Ne kísérelje meg a replikálási társítások megszakítását, ha a tükrözés állapota nem *inicializált*.  
    * Győződjön meg arról, hogy a kapcsolat állapota ***inaktív***.   
    Ne kísérelje meg a replikálási társítások megszakítását, ha a kapcsolat állapota *átadást*mutat.    

        Lásd: [a replikációs kapcsolat](cross-region-replication-display-health-status.md)állapotának megjelenítése. 

    c. Kattintson a társítás **megszakítása**elemre.   
    d. Ha a rendszer kéri, írja be az **Igen** értéket, és kattintson a **töréspont** gombra.  

2. Szinkronizálja újra a forrás kötetet a célként megadott kötettel:  
    a. Válassza ki a *cél* kötetet. Kattintson a **replikálás** lehetőségre a Storage szolgáltatás alatt. Ezután kattintson az **Újraszinkronizálás**elemre.   
    b. Ha a rendszer kéri, írja be az **Igen** értéket, és kattintson az **Újraszinkronizálás** gombra.

3. Csatlakoztassa újra a forrás kötetet a [Windows vagy Linux rendszerű virtuális gépekhez való csatlakoztatás vagy a kötet leválasztása](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)című témakör lépéseit követve.  
    Ez a lépés lehetővé teszi, hogy az ügyfél hozzáférhessen a forrás kötethez.

## <a name="next-steps"></a>Következő lépések  

* [Régiók közötti replikáció](cross-region-replication-introduction.md)
* [A régiók közötti replikáció használatára vonatkozó követelmények és szempontok](cross-region-replication-requirements-considerations.md)
* [Replikációs kapcsolat állapotának megjelenítése](cross-region-replication-display-health-status.md)
* [Kötet replikációs metrikái](azure-netapp-files-metrics.md#replication)
* [Régiók közötti replikáció – problémamegoldás](troubleshoot-cross-region-replication.md)

