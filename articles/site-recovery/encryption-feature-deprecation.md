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
ms.openlocfilehash: 79c129fee6d0339eed752abe94059fa566859bc0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086183"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>Site Recovery adattitkosítási funkció elavulása

Ez a dokumentum az elavultság részleteit és a Szervizelési műveletet ismerteti, ha a Site Recovery adattitkosítási funkciót használja, miközben a Hyper-V virtuális gépek vész-helyreállítását az Azure-ba konfigurálja. 

## <a name="deprecation-information"></a>Elavult információ


A Site Recovery adattitkosítási funkció elérhető volt a Hyper-V virtuális gépeket védő ügyfelek számára, így biztosítva, hogy a replikált adatok védve legyenek a biztonsági fenyegetések ellen. Ez a funkció **2022 április 30-ig**elavulttá válik. Az [Storage Service encryption](../storage/common/storage-service-encryption.md) (SSE) szolgáltatást használó [Rest-titkosítást](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/) használja. Az SSE használatával a rendszer titkosítja az adatok titkosítását, mielőtt megőrzi a tárolást és visszafejti a lekérést, és az Azure-ba történő feladatátvétel után a virtuális gépek a titkosított Storage-fiókokból fognak futni, ami lehetővé teszi a helyreállítási időre vonatkozó célkitűzés (RTO) javítását.

Vegye figyelembe, hogy ha Ön már használja ezt a szolgáltatást, az elavult részletekkel és a Szervizelési lépésekkel kapcsolatban kapott kommunikációt. 


## <a name="what-are-the-implications"></a>Mik a következményei?

**2022. április 30**. után minden olyan virtuális gép, amely továbbra is a kivont titkosítási funkciót használja, nem fog tudni feladatátvételt végezni. 

## <a name="required-action"></a>Szükséges művelet
A sikeres feladatátvételi műveletek folytatásához és a replikáláshoz kövesse az alábbi lépéseket:

Kövesse az alábbi lépéseket minden virtuális géphez: 
1.  [Tiltsa le a replikációt](./site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).
2.  [Hozzon létre egy új replikációs házirendet](./hyper-v-azure-tutorial.md#set-up-a-replication-policy).
3.  [Engedélyezze a replikálást](./hyper-v-vmm-azure-tutorial.md#enable-replication) , és válasszon egy olyan Storage-fiókot, amelyen engedélyezve van az SSE.

Miután a kezdeti replikálást az SSE-t használó Storage-fiókokba tette, a virtuális gépek titkosítást fognak használni a Azure Site Recovery.


## <a name="next-steps"></a>További lépések
Tervezze meg a Szervizelési lépések elvégzését, és hajtsa végre azokat a leghamarabb. Ha az elavult lekérdezéssel kapcsolatban bármilyen kérdése van, kérjük, lépjen kapcsolatba Microsoft ügyfélszolgálata. Ha többet szeretne megtudni a Hyper-V – Azure forgatókönyvről, tekintse meg [itt](hyper-v-vmm-architecture.md).
