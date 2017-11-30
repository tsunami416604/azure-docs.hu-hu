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
ms.date: 11/27/2017
ms.author: raynew
ms.openlocfilehash: f645150aa7b93ebbd98899cc8fdd495dfeaf3076
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Az Azure Site Recovery Azure-régiók közötti Azure IaaS virtuális gépek áttelepítése

Az Azure Site Recovery szolgáltatás üdvözli Önt! Ez a cikk használja, ha Azure virtuális gépek Azure-régiók közötti áttelepítés.
>[!NOTE]
>
> Azure virtuális gépek replikálása katasztrófa utáni helyreállítás és áttelepítési kell egy másik régióban, tekintse meg [Ez a dokumentum](../site-recovery-azure-to-azure.md). Az Azure virtuális gépek helyreállítási helyreplikálásának jelenleg előzetes verzió.


## <a name="prerequisites"></a>Előfeltételek
Ez mit kell ehhez a központi telepítéshez:

* **Infrastruktúra-szolgáltatási virtuális gépeket**: A virtuális gépeket szeretne áttelepíteni. Telepít át virtuális gépeken való kezelésével azokat a fizikai gépként.

## <a name="deployment-steps"></a>A központi telepítés lépései
Ez a szakasz az új Azure-portálon telepítési lépéseit ismerteti.

1. [Hozzon létre egy tárolót](../site-recovery-azure-to-azure.md#create-a-recovery-services-vault).
2. [A replikáció engedélyezése]... a virtuális gépek áttelepítése, és válassza ki a forrásként Azure /(Site-Recovery-Azure-to-Azure.MD).
  >[!NOTE]
  >
  > Natív replikációs kezelt lemezeken Azure virtuális gépek jelenleg nem támogatott. A "Fizikai a Azure" kapcsolót [Ez a dokumentum](../site-recovery-vmware-to-azure.md) felügyelt lemezzel rendelkező virtuális gépek áttelepítéséhez.
3. [Feladatátvételt](../site-recovery-failover.md). Kezdeti replikáció befejezését követően a másikra futtathatja a feladatátvétel egy Azure-régiót. Szükség esetén a helyreállítási terv létrehozása, és a feladatátvételt, régiók közötti több virtuális gép áttelepítéséhez. [További](../site-recovery-create-recovery-plans.md) helyreállítási tervek.

## <a name="next-steps"></a>Következő lépések
További információ található más fájlreplikációs forgatókönyvek [Mi az Azure Site Recovery?](../site-recovery-overview.md)
