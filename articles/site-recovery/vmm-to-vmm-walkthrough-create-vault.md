---
title: "A Hyper-V replikáció egy másodlagos helyre tároló létrehozása az Azure Site Recovery szolgáltatással |} Microsoft Docs"
description: "Hozzon létre egy tárolót, ha a Hyper-V virtuális gépek replikálása az Azure Site Recovery System Center VMM másodlagos hely ismerteti."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ff65dbfb-cb26-410e-ab48-76971625db08
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 28cfcf12b2e369f96664c163c0b6f2aa8a6ddcb9
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="step-5-create-a-vault-for-hyper-v-replication-to-a-secondary-site"></a>5. lépés:, Hozzon létre egy tárolót a Hyper-V replikáció egy másodlagos helyre

A helyszíni előkészítését követően [System Center Virtual Machine Manager (VMM) kiszolgáló és a Hyper-V gazdagép vagy fürt](vmm-to-vmm-walkthrough-vmm-hyper-v.md) a Hyper-V replikáció egy másodlagos hely használatával [Azure Site Recovery](site-recovery-overview.md), Recovery Services-tároló létrehozása, és válassza ki a replikációs környezet.

A cikk elolvasása után felmerülő megjegyzéseit alul vagy az [Azure Recovery Services fórumban](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti közzé.


## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>Válassza ki a védelmi cél

Válassza ki, hogy mit szeretne replikálni, és hova.

1. Kattintson a **Site Recovery** > **1. lépés: az infrastruktúra előkészítése** > **védelmi cél**.
2. Válassza ki **helyreállítási hely**, és válassza ki **Igen, a Hyper-V-vel**.
3. Válassza ki **Igen** annak jelzésére, hogy a VMM segítségével a Hyper-V-gazdagépek kezelése.
4. Válassza ki **Igen** Ha egy másodlagos VMM-kiszolgálóval rendelkezik. Ha egy VMM-kiszolgálón felhők közötti replikáció telepít, kattintson a **nem**. Ezután kattintson az **OK** gombra.

    ![Célok megválasztása](./media/vmm-to-vmm-walkthrough-create-vault/choose-goals.png)



## <a name="next-steps"></a>Következő lépések

Ugrás a [6. lépés: a replikáció forrás- és a cél beállítása](vmm-to-vmm-walkthrough-source-target.md).