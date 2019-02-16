---
title: Az Azure virtuális gépek migrálása felügyelt lemezekre |} A Microsoft Docs
description: A storage-fiókok nem felügyelt lemezek használatával felügyelt lemezeket használni létrehozott Azure virtuális gépeket áttelepíteni.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: e9c3e10f9b48bfe2efa5396c9e64d3d87be3d826
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56330647"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Az Azure virtuális gépek migrálása felügyelt lemezekre az Azure-ban

Az Azure Managed Disks leegyszerűsíti a tárolókezelési külön felügyelnie tárfiókokat eltávolításával.  Is áttelepítheti a meglévő Azure virtuális gépek számára, hogy nagyobb megbízhatóságot, rendelkezésre állási csoportban lévő virtuális gépek felügyelt lemezekre. Ez biztosítja, hogy a rendelkezésre állási csoport különböző virtuális lemezek hibaérzékeny pont hibák elkerülése érdekében hibapontok. A rendelkezésre állási csoport a különböző tárolási skálázási egységeket (stampek), amely korlátozza a egyetlen skálázási egységek hibáival oka a hardver és szoftver hibák különböző virtuális lemezek automatikusan helyezi.
Igényei alapján választhat a négy típusú tárolási lehetőségeket. A rendelkezésre álló lemeztípusok kapcsolatos további információkért tekintse meg ezt a cikket [lemez típusának kiválasztása](disks-types.md)

Áttelepítheti a Managed Disks a következő esetekben:

| Áttelepítése...                                            | Dokumentáció-hivatkozás                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Önálló virtuális gépek és virtuális gépek konvertálni a rendelkezésre állási csoport a managed Disks szolgáltatásba   | [Felügyelt lemezek használata virtuális gépek konvertálása](convert-unmanaged-to-managed-disks.md) |
| Egyetlen virtuális gép Kapcsolatcsoportok módosítása klasszikusról Resource Manager felügyelt lemezeken     | [Virtuális gép létrehozása a klasszikus virtuális merevlemezből](create-vm-specialized-portal.md)  | 
| Virtuális hálózat módosítása klasszikusról Resource Manager felügyelt lemezeken található összes virtuális gép     | [IaaS-erőforrások áttelepítése klasszikusról Resource Manager](migration-classic-resource-manager-ps.md) , majd [virtuális gép átalakítása nem felügyeltről felügyelt lemezessé](convert-unmanaged-to-managed-disks.md) | 






## <a name="plan-for-the-conversion-to-managed-disks"></a>A felügyelt lemezekre való konvertálást tervezése

Ez a szakasz segít, hogy a legjobb döntést a virtuális gép és a lemez típusa.


## <a name="location"></a>Hely

Jelöljön ki egy helyet, ahol érhetők el az Azure Managed Disks. A prémium szintű Managed Disks szolgáltatásba áthelyezéséhez, is elérhető legyen a Premium storage a régióban, ahol kíván áthelyezni. Lásd: [Azure-szolgáltatások régió szerint](https://azure.microsoft.com/regions/#services) naprakész információk az elérhető helyek.

## <a name="vm-sizes"></a>A virtuális gépek mérete

Ha az áttelepítés a Premium Managed Disks szolgáltatásba, akkor frissítse a virtuális gép méretét a Premium Storage képes a méret elérhető a régióban, ahol a virtuális gép is található. Tekintse át a Premium Storage képes a a Virtuálisgép-méretek. Az Azure virtuális gép mérete előírások felsorolt [virtuális gépek méretei](sizes.md).
Tekintse át a teljesítményt nyújt, amely a Premium Storage működnek, és válassza ki a leginkább megfelelő Virtuálisgép-méretet a számítási feladathoz leginkább megfelelő virtuális gépek. Győződjön meg arról, hogy van-e elegendő sávszélesség érhető el a meghajtó a lemez forgalmat a virtuális gép.

## <a name="disk-sizes"></a>Lemezméretek

**Premium Managed Disks**

Prémium szintű felügyelt lemezek, amelyek használhatók a virtuális gép hét típusa van, mindegyik adott IOPs és átviteli sebesség korlátok. Figyelembe ezeket a korlátokat a prémium szintű lemeztípus kiválasztása a virtuális gép kapacitását, teljesítmény, méretezhetőség tekintetében az alkalmazás igényeinek megfelelően, és csúcs tölti be.

| Prémium szintű lemezek típusa  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Lemezméret           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPS-érték lemezenként       | 120   | 240   | 500   | 1100  |2300              | 5000              | 7500              | 7500              | 
| Adattovábbítás lemezenként | 25 MB / s  | 50 MB / s  | 100 MB / s | 125 MB / s |150 MB / s | 200 MB / s | 250 MB / s | 250 MB / s |

**Standard szintű felügyelt lemezekre**

Hét típusa, a standard szintű felügyelt lemezek, amelyek használhatók a virtuális gép van. Azok a más kapacitásokhoz de azonos IOPS- és feldolgozásisebesség-korlátait. Válassza ki a standard szintű Managed disks kapacitást az alkalmazás igényeinek megfelelően.

| Standard lemez típusa  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Lemezméret           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1024 GB (1 TB)   | 2048 GB (2TB)    | 4095 GB (4 TB)   | 
| IOPS-érték lemezenként       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Adattovábbítás lemezenként | 60 MB / s | 60 MB / s | 60 MB / s | 60 MB / s |60 MB / s | 60 MB / s | 60 MB / s | 60 MB / s | 

## <a name="disk-caching-policy"></a>Lemez gyorsítótárazási házirend

**Premium Managed Disks**

Alapértelmezés szerint a lemez gyorsítótárazási házirend a *csak olvasható* minden a prémium szintű adatlemezek esetén és *olvasási és írási* a prémium szintű operációsrendszer-lemez a virtuális Géphez csatlakoztatva. Ezt a konfigurációs beállítást az optimális teljesítmény érdekében az alkalmazás IOs-hez javasolt. Írási vagy csak írási adatlemezek (például az SQL Server-naplófájlok) tiltsa le a lemezek gyorsítótárazása, így jobb alkalmazásteljesítményt érhet el.

## <a name="pricing"></a>Díjszabás

Tekintse át a [a felügyelt lemezek díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/). Prémium szintű Managed Disks díjszabása megegyezik a prémium szintű Unmanaged Disks. Azonban a standard szintű Managed Disks díjszabása eltér attól a standard szintű nem felügyelt lemezek.



## <a name="next-steps"></a>További lépések

- Tudjon meg többet [Managed Disks](managed-disks-overview.md)
