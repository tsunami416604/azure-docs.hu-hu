---
title: "A VMM-felhőkben Hyper-V virtuális gépek replikálása Azure-bA az Azure Site Recovery hálózatleképezés konfigurálása |} Microsoft Docs"
description: "Hálózatleképezés konfigurálása az Azure-bA az Azure Site Recovery VMM-felhőkben Hyper-V virtuális gépek replikálása esetén"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: ed6f73d8baea5af0d2aa5f0ae885f305911ccc82
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2017
---
# <a name="step-9-configure-network-mapping-for-hyper-v-replication-with-vmm-to-azure"></a>9. lépés: Az Azure-ba (VMM) szolgáltatással a Hyper-V replikáció hálózatleképezés konfigurálása

Miután beállította a [forrás és cél replikációs beállítások](vmm-to-azure-walkthrough-source-target.md), ez a cikk segítségével helyszíni VMM-Virtuálisgép-hálózatok és az Azure-hálózatok közötti hálózatleképezés konfigurálása.

Ebben a cikkben, vagy az alsó megjegyzések és kérdéseket küldje a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Előkészületek

- További tudnivalók [hálózatleképezés](vmm-to-azure-walkthrough-network.md#network-mapping-for-replication-to-azure).
- [Hálózatleképezés előkészítése a VMM](vmm-to-azure-walkthrough-network.md#prepare-vmm-for-network-mapping). 
- Ellenőrizze, hogy a VMM-kiszolgálón futó virtuális gépek csatlakoznak-e a virtuálisgép-hálózathoz, illetve, hogy létrehozott-e legalább egy Azure virtuális hálózatot. Egyetlen Azure-hálózatra több virtuálisgép-hálózatot is le lehet képezni.

## <a name="configure-mapping"></a>Leképezés konfigurálása

Konfigurálja az alábbiak szerint a leképezéseket:

1. A **Site Recovery-infrastruktúra** > **Hálózatleképezések** > **Hálózatleképezés** menüpontban kattintson a **+Hálózatleképezés** ikonra.

    ![Hálózatleképezés](./media/vmm-to-azure-walkthrough-network-mapping/network-mapping1.png)
2. A **Hálózatleképezés hozzáadása** részben válassza ki a forrás VMM-kiszolgálót, célként pedig az **Azure-t**.
3. Ellenőrizze az előfizetést, illetve a feladatok átadását követően használatos üzembe helyezési modellt.
4. A **Forráshálózat** panelen a VMM-kiszolgálóhoz társítottak listájából válassza ki azt a helyszíni virtuálisgép-hálózatot, amelyet le kíván képezni.
5. A **Célhálózat** panelen válassza ki azt az Azure-hálózatot, amelyre a létrehozott replika Azure virtuális gépek kerülnek. Ezután kattintson az **OK** gombra.

    ![Hálózatleképezés](./media/vmm-to-azure-walkthrough-network-mapping/network-mapping2.png)

Ez történik a hálózatleképezés megkezdésekor:

* A forrás virtuálisgép-hálózatban található meglévő virtuális gépek a leképezés elkezdésekor csatlakoznak a célhálózathoz. A forrás virtuálisgép-hálózathoz csatlakoztatott új virtuális gépek a replikálás végrehajtásakor csatlakoznak a leképezett Azure-hálózathoz.
* Meglévő hálózatleképezés módosítása esetén a replika virtuális gépek az új beállításokkal fognak csatlakozni.
* Ha a célhálózatban már több alhálózat működik, és ezek egyikének ugyanaz a neve, mint annak, amelyen a forrás virtuális gép is található, a replika virtuális gép a feladatátvételt követően ehhez a cél alhálózathoz fog csatlakozni.
* Ha nem létezik egyező nevű alhálózat, a virtuális gép a hálózat első virtuális alhálózatához csatlakozik.



## <a name="next-steps"></a>Következő lépések

Ugrás [10. lépés: a replikációs házirend létrehozása](vmm-to-azure-walkthrough-replication.md)
