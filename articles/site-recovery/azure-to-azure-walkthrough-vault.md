---
title: "Állítson be egy Azure virtuális gép repliction tárolót az Azure Site Recovery régiók közötti |} Microsoft Docs"
description: "Összefoglalja a lépéseket, akkor be kell állítania egy Azure Site Recovery segítségével Azure-régiók közötti Azure replikációs tárolót"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: 40472189-3d80-4963-b175-8bddcbc2f61f
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.openlocfilehash: e03d17992ee0b12049636e40188950bcc4a6f31e
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="step-4-set-up-a-vault-for-azure-to-azure-replication"></a>4. lépés: Állítson be egy Azure az Azure-bA replikációs tárolót

Után [hálózatok tervezési](azure-to-azure-walkthrough-network.md), ez a cikk az használatával állítson be egy tárolót az Azure virtuális gépek (VM) esetében, amelyek egy másik Azure-régió, használja a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás az Azure portálon.

- A cikk befejezése után állítsa be a Recovery Services-tárolónak kell rendelkeznie.
- Megjegyzéseket a cikk alján tehet, kérdéseket pedig az [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti fel.



>[!NOTE]
>
> Az Azure virtuális gép replikációs jelenleg előzetes verzió.




## <a name="create-a-vault"></a>Tároló létrehozása

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

>[!NOTE]
>
> Azt javasoljuk, hogy a helyen, ahol azt szeretné, hogy a virtuális gépek replikálásához hozzon létre a Recovery Services-tároló. Például, ha a cél elérési útja a központi USA, hozzon létre a tárolót, a **USA középső RÉGIÓJA**.


## <a name="next-steps"></a>Következő lépések

Ugrás a [5. lépés: replikálás engedélyezése](azure-to-azure-walkthrough-enable-replication.md)
