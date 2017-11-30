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
ms.openlocfilehash: 805582d89ee906e21fbe588e895ce0fe3a926a66
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Az Azure Site Recovery Azure-régiók közötti Azure IaaS virtuális gépek áttelepítése

Között használja az ebben a cikkben, ha az áttelepítendő Azure virtuális gépek (VM) Azure-régiók, használja a [Site Recovery](../site-recovery-overview.md) szolgáltatás.

## <a name="prerequisites"></a>Előfeltételek

Infrastruktúra-szolgáltatási virtuális gépeket szeretne áttelepíteni van szüksége.

## <a name="deployment-steps"></a>A központi telepítés lépései

1. [Hozzon létre egy tárolót](azure-to-azure-tutorial-enable-replication.md#create-a-vault).
2. [Engedélyezze a replikálást](azure-to-azure-tutorial-enable-replication.md#enable-replication) a virtuális gépek áttelepítése, és válassza ki a forrásként Azure. Jelenleg natív replikáció Azure virtuális gépek felügyelt lemezek használata nem támogatott.
3. [Feladatátvételt](../site-recovery-failover.md). Kezdeti replikáció befejezését követően a másikra futtathatja a feladatátvétel egy Azure-régiót. Szükség esetén a helyreállítási terv létrehozása, és a feladatátvételt, régiók közötti több virtuális gép áttelepítéséhez. [További](../site-recovery-create-recovery-plans.md) helyreállítási tervek.

## <a name="next-steps"></a>Következő lépések
További tudnivalók a többi replikációs forgatókönyvek [Mi az Azure Site Recovery?](../site-recovery-overview.md)
