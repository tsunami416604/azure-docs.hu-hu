---
title: "Azure IaaS virtuális gépek Azure-régiók közötti áttelepítés |} Microsoft Docs"
description: "Azure Site Recovery segítségével Azure IaaS virtuális gépeket áttelepíteni egy Azure-régió, egy másikra."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8a29e0d9-0010-4739-972f-02b8bdf360f6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: raynew
ms.openlocfilehash: 02f68b68491250f89e8b0e3057f2363b177ab32e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Az Azure Site Recovery Azure-régiók közötti Azure IaaS virtuális gépek áttelepítése
## <a name="overview"></a>Áttekintés
Az Azure Site Recovery szolgáltatás üdvözli Önt! Ez a cikk használja, ha Azure virtuális gépek Azure-régiók közötti áttelepítés.
>[!NOTE]
>
> Azure virtuális gépek replikálása katasztrófa utáni helyreállítás és áttelepítési kell egy másik régióban, tekintse meg [Ez a dokumentum](site-recovery-azure-to-azure.md). Az Azure virtuális gépek helyreállítási helyreplikálásának jelenleg előzetes verzió.

Mielőtt elkezdené, vegye figyelembe, hogy:

* Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: Azure Resource Manager és klasszikus. Az Azure portálon két üzembe helyezési modell támogatja. Áttelepítési lépéseit ugyanazok, hogy a Site Recovery konfigurálja az erőforrás-kezelő vagy a klasszikus. 

Megjegyzéseit vagy kérdéseit a cikk alján, vagy az [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti fel.

## <a name="prerequisites"></a>Előfeltételek
Ez mit kell ehhez a központi telepítéshez:

* **Infrastruktúra-szolgáltatási virtuális gépeket**: A virtuális gépeket szeretne áttelepíteni. Telepít át virtuális gépeken való kezelésével azokat a fizikai gépként.

## <a name="deployment-steps"></a>A központi telepítés lépései
Ez a szakasz az Azure-portál telepítési lépéseit ismerteti.

1. [Hozzon létre egy tárolót](site-recovery-azure-to-azure.md#create-a-recovery-services-vault).
2. [Engedélyezze a replikálást](site-recovery-azure-to-azure.md) a virtuális gépeket szeretne áttelepíteni, majd válassza ki az Azure forrásaként az. Natív replikációs kezelt lemezeken Azure virtuális gépek jelenleg nem támogatott. A "Fizikai a Azure" kapcsolót [Ez a dokumentum](site-recovery-vmware-to-azure.md) felügyelt lemezzel rendelkező virtuális gépek áttelepítéséhez.
1. [Feladatátvételt](site-recovery-failover.md). Kezdeti replikáció befejezését követően a másikra futtathatja a feladatátvétel egy Azure-régiót. Szükség esetén a helyreállítási terv létrehozása, és a feladatátvételt, régiók közötti több virtuális gép áttelepítéséhez. [További](site-recovery-create-recovery-plans.md) helyreállítási tervek.

## <a name="next-steps"></a>Következő lépések
További információ található más fájlreplikációs forgatókönyvek [Mi az Azure Site Recovery?](site-recovery-overview.md)
