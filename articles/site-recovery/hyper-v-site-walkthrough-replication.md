---
title: "Az Azure-bA az Azure Site Recovery (a System Center VMM nélkül) a Hyper-V Virtuálisgép-replikációt a replikációs házirend beállítása |} Microsoft Docs"
description: "Összefoglalja a lépéseket kell beállítani a replikációs házirendet, ha a Hyper-V virtuális gépek replikálása az Azure storage"
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 1777e0eb-accb-42b5-a747-11272e131a52
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.openlocfilehash: ca5bec5cf1152e6259b9fe7a869edd2d62b88e1a
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="step-9-set-up-a-replication-policy-for-hyper-v-vm-replication-to-azure"></a>9. lépés: Azure Hyper-V virtuális gép replikációs replikációs házirend beállítása

Ez a cikk ismerteti, hogyan kell beállítani a replikációs házirendet, ha a Hyper-V virtuális gépeket replikál, az Azure (a System Center VMM nélkül) használatával a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás az Azure portálon.


Ebben a cikkben, vagy az alsó megjegyzések és kérdéseket küldje a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="about-snapshots"></a>A pillanatképek kapcsolatos

A Hyper-V két különböző pillanatképtípust használ: az egyik a standard pillanatkép, amely a virtuális gép egészét lefedő növekményes pillanatképet, a másik pedig az alkalmazáskonzisztens pillanatkép, amely a virtuális gépen futó alkalmazások adatairól készült, időponthoz kötött pillanatképet jelent.
    - Az alkalmazáskonzisztens pillanatképek a kötet árnyékmásolása szolgáltatás (VSS) segítségével garantálják, hogy az alkalmazások konzisztens állapotban legyenek a pillanatkép készítésének időpontjában.
    - Ha engedélyezi az alkalmazáskonzisztens pillanatképeket, az hatással a forrás virtuális gépeken futó alkalmazások teljesítményére. Ügyeljen rá, hogy az itt megadott érték kisebb legyen a további beállított helyreállítási pontok számánál.

## <a name="set-up-a-replication-policy"></a>A replikációs házirend beállítása

1. Új replikációs szabályzat létrehozásához kattintson az **Infrastruktúra előkészítése** > **Replikációs beállítások** > **+Létrehozás és társítás** elemre.

    ![Network (Hálózat)](./media/hyper-v-site-walkthrough-replication/gs-replication.png)
2. A **Házirend létrehozása és társítása** beállításnál adja meg a szabályzat nevét.
3. A **Másolás gyakorisága** elemmel meghatározhatja, hogy milyen gyakran szeretné replikálni a módosult adatokat a kezdeti replikációt követően (ez lehet 30 másodperc, 5 perc vagy 15 perc).

    > [!NOTE]
    > Prémium szintű tárterületre replikálás esetén nem támogatott a 30 másodperces gyakoriság. A korlátozás a Prémium szintű Storage által támogatott blobonkénti pillanatképek számától (100) függ. [További információk](../storage/common/storage-premium-storage.md#snapshots-and-copy-blob).

4. A **helyreállításipont-megőrzést**, adja meg az órában mennyi az adatmegőrzési időtartam legyen az egyes helyreállítási pontok. Virtuális gépek ablak belüli bármelyik pontra állíthatók helyre.
5. A **alkalmazáskonzisztens pillanatkép gyakorisága**, adja meg, hogy milyen gyakran (1 – 12 órába) helyreállítási pontokat tartalmazó alkalmazáskonzisztens pillanatképeket jönnek létre.
6. A **kezdeti replikáció kezdési ideje**, adja meg, mikor kell elkezdeni a kezdeti replikálást. A replikálási folyamat az internetes sávszélességet használja, így érdemes a műveletet olyankorra ütemezni, amikor kevesen használják az internetet. Ezután kattintson az **OK** gombra.

    ![Replikációs szabályzat](./media/hyper-v-site-walkthrough-replication/gs-replication2.png)

Ha egy új házirendet hoz létre, akkor automatikusan a Hyper-V hely társítja. A Hyper-V hely (és a benne lévő virtuális gépek) társíthatja a több replikációs házirend **replikációs** > házirend neve > **társítása Hyper-V hely**.



## <a name="next-steps"></a>Következő lépések

Ugrás a [10. lépés: replikálás engedélyezése](hyper-v-site-walkthrough-enable-replication.md)
