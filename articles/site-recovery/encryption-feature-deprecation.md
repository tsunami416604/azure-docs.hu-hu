---
title: Az Azure Site Recovery adattitkosítási szolgáltatásának eprecációja | Microsoft dokumentumok
description: Részletek regarig Azure Site Recovery adattitkosítási funkció
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: rajanaki
ms.openlocfilehash: 5e74466891a5926d8ae8feb3c1c48348ecf3cfe6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74134995"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>A Webhely-helyreállítási adattitkosítási szolgáltatás eprektálása

Ez a dokumentum ismerteti az eprecation részleteket és a javítási műveletet kell végrehajtania, ha a Site Recovery adattitkosítási funkciót használja a Hyper-V virtuális gépek Azure-ba történő vészhelyreállítása konfigurálása során. 

## <a name="deprecation-information"></a>Eprekation információk


A Site Recovery adattitkosítási szolgáltatás elérhető volt a Hyper-V virtuális gépek et védő ügyfelek számára, hogy a replikált adatok védettek legyenek a biztonsági fenyegetésekkel szemben. ez a funkció **2019.** Ez helyébe a fejlettebb [titkosítás i inaktív](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/) funkció, amely a [storage service encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (SSE) . Az SSE-vel az adatok titkosítva vannak, mielőtt a tárolóba kerülne, és visszafejtenék az Azure-ba, és az Azure-ba való feladatátvételkor a virtuális gépek a titkosított tárfiókokból futnak, lehetővé téve a jobb helyreállítási idő célkitűzést (RTO).

Kérjük, vegye figyelembe, hogy ha Ön már meglévő ügyfél, aki ezt a funkciót használja, akkor az eprecációs részletekkel és a javítási lépésekkel kapcsolatos kommunikációt kapott volna. 


## <a name="what-are-the-implications"></a>Milyen következményekkel jár?

**2019. december 30-a**után a kivisszavonult titkosítási szolgáltatást továbbra is használó virtuális gépek nem hajthatnak végre feladatátvételt. 

## <a name="required-action"></a>Szükséges művelet
A sikeres feladatátvételi műveletek folytatásához és a replikációk az alábbi lépéseket hajtsák végre:

Kövesse az alábbi lépéseket az egyes virtuális gépek: 
1.  [A replikáció letiltása](https://docs.microsoft.com/azure/site-recovery/site-recovery-manage-registration-and-protection#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).
2.  [Hozzon létre egy új replikációs házirendet](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-a-replication-policy).
3.  [Engedélyezze a replikációt,](https://docs.microsoft.com/azure/site-recovery/hyper-v-vmm-azure-tutorial#enable-replication) és válasszon ki egy olyan tárfiókot, amelyen az SSE engedélyezve van.

Miután befejezte a kezdeti replikációt a tárfiókok sse engedélyezve van, a virtuális gépek fogja használni titkosítás inaktív azure site recovery.


## <a name="next-steps"></a>További lépések
Tervezze meg a javítási lépések végrehajtását, és legkorábban hajtsa végre azokat. Abban az esetben, ha bármilyen kérdése van ezzel az eprecációval kapcsolatban, kérjük, forduljon a Microsoft támogatási szolgálatához. Ha többet szeretne megtudni a Hyper-V azure-forgatókönyvről, olvassa el [itt.](hyper-v-vmm-architecture.md)

