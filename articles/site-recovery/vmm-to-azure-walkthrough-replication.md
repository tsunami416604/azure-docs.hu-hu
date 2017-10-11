---
title: "A Hyper-V virtuális gép (a VMM-mel) replikáció az Azure szolgáltatásban az Azure Site Recovery replikációs házirend beállítása |} Microsoft Docs"
description: "Ismerteti, hogyan lehet a Hyper-V virtuális gép (a VMM-mel) replikáció az Azure szolgáltatásban az Azure Site Recovery replikációs házirend beállítása"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ee808b20-324b-4198-b831-edb65b95e8b7
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: 592e1c3f647e5b1f1d9aa776657e8f89b60349e1
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="step-10-set-up-a-replication-policy-for-hyper-v-vm-replication-with-vmm-to-azure"></a>10. lépés: Az Azure-ba (VMM) szolgáltatással Hyper-V Virtuálisgép-replikációt a replikációs házirend beállítása


Beállítása után [hálózatleképezés](vmm-to-azure-walkthrough-network-mapping.md), használja a cikk egy replikációs házirend T\to replikálás konfigurálása a helyszíni Hyper-V virtuális gépek kezelése a System Center Virtual Machine Manager (VMM) felhők az Azure-bA a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás az Azure portálon.

A cikk elolvasása után felmerülő megjegyzéseit alul vagy az [Azure Recovery Services fórumban](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti közzé.



## <a name="create-a-policy"></a>Házirend létrehozása

1. Új replikációs szabályzat létrehozásához kattintson az **Infrastruktúra előkészítése** > **Replikációs beállítások** > **+Létrehozás és társítás** elemre.

    ![Network (Hálózat)](./media/vmm-to-azure-walkthrough-replication/gs-replication.png)
2. A **Házirend létrehozása és társítása** beállításnál adja meg a szabályzat nevét.
3. A **Másolás gyakorisága** elemmel meghatározhatja, hogy milyen gyakran szeretné replikálni a módosult adatokat a kezdeti replikációt követően (ez lehet 30 másodperc, 5 perc vagy 15 perc).

    > [!NOTE]
    >  Prémium szintű tárterületre replikálás esetén nem támogatott a 30 másodperces gyakoriság. A korlátozás a Prémium szintű Storage által támogatott blobonkénti pillanatképek számától (100) függ. [További információ](../storage/common/storage-premium-storage.md#snapshots-and-copy-blob)

4. A **Helyreállítási pont megőrzése** beállításnál azt adhatja meg, hogy hány órás legyen az egyes helyreállítási pontok adatmegőrzési időtartama. A védelemmel ellátott gépeket az időtartamon belüli bármelyik pontra visszaállíthatja.
5. Az **Alkalmazáskonzisztens pillanatkép gyakorisága** beállítás azt határozza meg, hogy milyen gyakran hozzon létre a rendszer alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat (a beállítás értéke 1 és 12 óra között változhat). A Hyper-V két különböző pillanatképtípust használ: az egyik a standard pillanatkép, amely a virtuális gép egészét lefedő növekményes pillanatképet, a másik pedig az alkalmazáskonzisztens pillanatkép, amely a virtuális gépen futó alkalmazások adatairól készült, időponthoz kötött pillanatképet jelent. Az alkalmazáskonzisztens pillanatképek a kötet árnyékmásolása szolgáltatás (VSS) segítségével garantálják, hogy az alkalmazások konzisztens állapotban legyenek a pillanatkép készítésének időpontjában. Ne feledje, hogy az alkalmazáskonzisztens pillanatképek bekapcsolása negatív hatással lesz a forrás virtuális gépeken futó alkalmazások teljesítményére. Ügyeljen rá, hogy az itt megadott érték kisebb legyen a további beállított helyreállítási pontok számánál.
6. A **Kezdeti replikáció kezdési ideje** a kezdeti replikáció kezdésének időpontját határozza meg. A replikálási folyamat az internetes sávszélességet használja, így érdemes a műveletet olyankorra ütemezni, amikor kevesen használják az internetet.
7. **Az Azure-on tárolt adatok titkosítása** beállításnál adhatja meg, hogy szeretné-e titkosítani az Azure-tárfiókban elhelyezett inaktív adatokat. Végül kattintson az **OK** gombra.

    ![Replikációs szabályzat](./media/vmm-to-azure-walkthrough-replication/gs-replication2.png)
8. Az újonnan létrehozott szabályzatokat a rendszer automatikusan társítja a VMM-felhővel. Kattintson az **OK** gombra. A további VMM-felhőket (és a rajtuk futó virtuális gépeket) a **Replikáció** > szabályzat neve > **VMM-felhő társítása** menüpontban társíthatja az adott replikációs szabályzathoz.

    ![Replikációs szabályzat](./media/vmm-to-azure-walkthrough-replication/policy-associate.png)



## <a name="next-steps"></a>Következő lépések

Ugrás a [11. lépés: replikálás engedélyezése](vmm-to-azure-walkthrough-enable-replication.md)
