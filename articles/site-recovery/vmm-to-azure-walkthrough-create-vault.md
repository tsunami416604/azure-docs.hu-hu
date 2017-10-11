---
title: "Állítson be egy tárolót a Hyper-V replikáció (a System Center VMM) az Azure Site Recovery segítségével Azure-bA |} Microsoft Docs"
description: "Összefoglalja a lépéseket, akkor be kell állítania egy tárolót az Azure Site Recovery segítségével Azure-ba (VMM) szolgáltatással a Hyper-V replikáció"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: b3cd6f03-c33c-406d-91d4-5cba69f79abd
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: af453ec27ba15ad8c59cf9f544584ad18dc0f74a
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2017
---
# <a name="step-7-set-up-a-vault-for-hyper-v-replication"></a>7. lépés: Egy tárolót a Hyper-V replikáció beállítása

Ez a cikk ismerteti, hogyan állítson be egy tárolót, és adja meg, mi az Azure használatát a helyszíni helyről replikálja a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás az Azure portálon.


Ebben a cikkben, vagy az alsó megjegyzések és kérdéseket küldje a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]



## <a name="select-a-protection-goal"></a>Válassza ki a védelmi cél

Válassza ki, hogy mit szeretne replikálni, és hová.

1. Kattintson a **Recovery Services-tárolók** > tárolóban.
2. Az erőforrás menüjében kattintson **Site Recovery** > **infrastruktúra előkészítése** > **védelmi cél**.
3. A **védelmi cél**, jelölje be **az Azure-bA** > **Igen, a Hyper-V-vel**. Válassza ki **Igen** győződjön meg arról, hogy a VMM nhasználják van. 

     ![Célok megválasztása](./media/vmm-to-azure-walkthrough-create-vault/choose-goals.png)



## <a name="next-steps"></a>Következő lépések

Ugrás a [8. lépés: állítson be forrása és célja](vmm-to-azure-walkthrough-source-target.md)
