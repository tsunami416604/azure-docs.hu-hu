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
ms.openlocfilehash: 86806c5dbafc1fd88c434dcee6292683d050cd2a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Az Azure Site Recovery Azure-régiók közötti Azure IaaS virtuális gépek áttelepítése
## <a name="overview"></a>Áttekintés
Az Azure Site Recovery szolgáltatás üdvözli Önt! Ez a cikk használja, ha Azure virtuális gépek Azure-régiók közötti áttelepítés.
>[!NOTE]
>
> Azure virtuális gépek replikálása katasztrófa utáni helyreállítás és áttelepítési kell egy másik régióban, tekintse meg [Ez a dokumentum](site-recovery-azure-to-azure.md). Az Azure virtuális gépek helyreállítási helyreplikálásának jelenleg előzetes verzió.

Mielőtt elkezdené, vegye figyelembe, hogy:

* Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: Azure Resource Manager és klasszikus. Az Azure-ban két különböző portál érhető el: a klasszikus Azure portál, amely a klasszikus üzembe helyezési modellt támogatja, és az Azure portál, amely mindkét modellt támogatja. Áttelepítési lépéseit ugyanazok, hogy a Site Recovery konfigurálja az erőforrás-kezelő vagy a klasszikus. Azonban a felhasználói felület utasítások és a képernyőképeket ebben a cikkben az Azure portál kapcsolódik.



Megjegyzéseit vagy kérdéseit a cikk alján, vagy az [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti fel.

## <a name="prerequisites"></a>Előfeltételek
Ez mit kell ehhez a központi telepítéshez:

* **Infrastruktúra-szolgáltatási virtuális gépeket**: A virtuális gépeket szeretne áttelepíteni. Telepít át virtuális gépeken való kezelésével azokat a fizikai gépként.

## <a name="deployment-steps"></a>A központi telepítés lépései
Ez a szakasz az új Azure-portálon telepítési lépéseit ismerteti.

1. [Hozzon létre egy tárolót](site-recovery-azure-to-azure.md#create-a-recovery-services-vault).
2. [Engedélyezze a replikálást](site-recovery-azure-to-azure.md) a virtuális gépeket szeretne áttelepíteni, majd válassza ki az Azure forrásaként az.
  >[!NOTE]
  >
  > Natív replikációs kezelt lemezeken Azure virtuális gépek jelenleg nem támogatott. A "Fizikai a Azure" kapcsolót [Ez a dokumentum](site-recovery-vmware-to-azure.md) felügyelt lemezzel rendelkező virtuális gépek áttelepítéséhez.
3. [Feladatátvételt](site-recovery-failover.md). Kezdeti replikáció befejezését követően a másikra futtathatja a feladatátvétel egy Azure-régiót. Szükség esetén a helyreállítási terv létrehozása, és a feladatátvételt, régiók közötti több virtuális gép áttelepítéséhez. [További](site-recovery-create-recovery-plans.md) helyreállítási tervek.

## <a name="next-steps"></a>Következő lépések
További információ található más fájlreplikációs forgatókönyvek [Mi az Azure Site Recovery?](site-recovery-overview.md)
