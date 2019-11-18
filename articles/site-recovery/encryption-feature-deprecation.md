---
title: A Azure Site Recovery adattitkosítási funkció elavulása | Microsoft Docs
description: Részletek regarig Azure Site Recovery adattitkosítási funkció
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: rajanaki
ms.openlocfilehash: 5e74466891a5926d8ae8feb3c1c48348ecf3cfe6
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134995"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>Site Recovery adattitkosítási funkció elavulása

Ez a dokumentum az elavultság részleteit és a Szervizelési műveletet ismerteti, ha a Site Recovery adattitkosítási funkciót használja, miközben a Hyper-V virtuális gépek vész-helyreállítását az Azure-ba konfigurálja. 

## <a name="deprecation-information"></a>Elavult információ


A Site Recovery adattitkosítási funkció elérhető volt a Hyper-V virtuális gépeket védő ügyfelek számára, így biztosítva, hogy a replikált adatok védve legyenek a biztonsági fenyegetések ellen. Ez a funkció a **2019. december 30-ig**lesz elavult. Az [Storage Service encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (SSE) szolgáltatást használó [Rest-titkosítást](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/) használja. Az SSE használatával a rendszer titkosítja az adatok titkosítását, mielőtt megőrzi a tárolást és visszafejti a lekérést, és az Azure-ba történő feladatátvétel után a virtuális gépek a titkosított Storage-fiókokból fognak futni, ami lehetővé teszi a helyreállítási időre vonatkozó célkitűzés (RTO) javítását.

Vegye figyelembe, hogy ha Ön már használja ezt a szolgáltatást, az elavult részletekkel és a Szervizelési lépésekkel kapcsolatban kapott kommunikációt. 


## <a name="what-are-the-implications"></a>Mik a következményei?

**2019. december 30-ig**minden olyan virtuális gép, amely továbbra is a kivont titkosítási funkciót használja, nem lesz engedélyezve a feladatátvétel. 

## <a name="required-action"></a>Szükséges művelet
A sikeres feladatátvételi műveletek folytatásához és a replikáláshoz kövesse az alábbi lépéseket:

Kövesse az alábbi lépéseket minden virtuális géphez: 
1.  [Tiltsa le a replikációt](https://docs.microsoft.com/azure/site-recovery/site-recovery-manage-registration-and-protection#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).
2.  [Hozzon létre egy új replikációs házirendet](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-a-replication-policy).
3.  [Engedélyezze a replikálást](https://docs.microsoft.com/azure/site-recovery/hyper-v-vmm-azure-tutorial#enable-replication) , és válasszon egy olyan Storage-fiókot, amelyen engedélyezve van az SSE.

Miután a kezdeti replikálást az SSE-t használó Storage-fiókokba tette, a virtuális gépek titkosítást fognak használni a Azure Site Recovery.


## <a name="next-steps"></a>További lépések
Tervezze meg a Szervizelési lépések elvégzését, és hajtsa végre azokat a leghamarabb. Ha az elavult lekérdezéssel kapcsolatban bármilyen kérdése van, kérjük, lépjen kapcsolatba Microsoft ügyfélszolgálata. Ha többet szeretne megtudni a Hyper-V – Azure forgatókönyvről, tekintse meg [itt](hyper-v-vmm-architecture.md).

