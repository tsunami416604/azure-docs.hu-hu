---
title: "Állítson be egy tárolót a Hyper-V replikáció (a System Center VMM nélkül) az Azure Site Recovery segítségével Azure-bA |} Microsoft Docs"
description: "Összefoglalja a lépéseket, akkor be kell állítania egy tárolót a Hyper-V replikát az Azure Site Recovery segítségével Azure-bA"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a936b3e2-0dbe-47ac-a98e-5285d96dc786
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.openlocfilehash: 8212ff011633c3a89d3310e828b6d5f1cda6ce3f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
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
3. A **védelmi cél**, jelölje be **az Azure-bA** > **Igen, a Hyper-V-vel**. Válassza ki **nem** annak ellenőrzéséhez, hogy a VMM nem használja. 

    ![Célok megválasztása](./media/hyper-v-site-walkthrough-create-vault/choose-goals2.png)



## <a name="next-steps"></a>Következő lépések

Ugrás a [8. lépés: állítson be forrása és célja](hyper-v-site-walkthrough-source-target.md)
