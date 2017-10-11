---
title: "Azure virtuális gépek áttelepítése felügyelt lemezek |} Microsoft Docs"
description: "Létre nem felügyelt lemezek tárfiókokban felügyelt lemezeket használó Azure virtuális gépek áttelepítését."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: cynthn
ms.openlocfilehash: e23697b390e03bd2b71f2c905882070d864d62ed
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Azure virtuális gépek áttelepítése az Azure-ban kezelt lemezek

Azure-lemezeket felügyelt leegyszerűsíti a tárolókezelési külön-külön a storage-fiókok kezeléséhez szükség.  Kezelt lemezeken is kihasználhatják a megbízhatóságot, rendelkezésre állási csoport a virtuális gépek is áttelepítheti a meglévő Azure virtuális gépeken. Ez biztosítja, hogy a rendelkezésre állási csoport másik virtuális lemezek megfelelően különítve egymástól egyetlen pont, a hibák elkerülése érdekében. A rendelkezésre állási csoport a különböző tárolási méretezési egységeit (bélyegek) egy méretezési egység tárolóhibák miatt a hardver és szoftver-hibák hatásának korlátozó különböző virtuális lemezek automatikusan helyezi.
A igények alapján választhat a két típusú tárolási lehetőségek közül választhat:

- [Prémium szintű felügyelt lemez](../../storage/common/storage-premium-storage.md) tartós állapot meghajtót (SSD)-alapú tárolási adathordozókon rendszerrel highperformance, alacsony késésű lemez I/O-igényes munkaterhelések futó virtuális gépek támogatása. Telepítse át a prémium szintű felügyelt lemez is igénybe vehet a sebesség előnyeit, és ezek a lemezek teljesítményét.

- [Standard szintű felügyelt lemez](../../storage/common/storage-standard-storage.md) használják a merevlemez-meghajtó (HDD)-alapú tárolási adathordozókon, és a legalkalmasabb fejlesztési és tesztelési célú és egyéb gyakori hozzáférés munkaterhelések kevesebb érzékeny teljesítmény variancia.

Felügyelt lemezek telepítheti át a következő esetekben:

| Telepítse át...                                            | Dokumentáció-hivatkozás                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alakítsa át az önálló virtuális gépek és virtuális gépek a rendelkezésre állási készlet felügyelt lemezek   | [Alakítsa át a virtuális gépek felügyelt lemezeket használni](convert-unmanaged-to-managed-disks.md) |
| Egy virtuális a klasszikus az erőforrás-kezelő kezelt lemezeken     | [Egyetlen virtuális gép áttelepítése](migrate-single-classic-to-resource-manager.md)  | 
| A Vneten belül a klasszikus az erőforrás-kezelő kezelt lemezeken található virtuális gépek     | [Az erőforrás-kezelő át IaaS-erőforrásokra a klasszikus](migration-classic-resource-manager-ps.md) , majd [alakítsa át a virtuális gép nem felügyelt lemezekből felügyelt](convert-unmanaged-to-managed-disks.md) | 






## <a name="plan-for-the-conversion-to-managed-disks"></a>A felügyelt lemezek átalakítás megtervezése

Ez a szakasz segítséget nyújt a legjobb döntést a virtuális gép és a lemez típusok.


## <a name="location"></a>Hely

Jelölje ki a helyet, ahol Azure felügyelt lemezek érhetőek el. Prémium felügyelt lemezzel helyez át, ha is győződjön meg arról, hogy prémium szintű storage elérhető a régióban, ha azt tervezi, hogy helyezze át. Lásd: [Azure-szolgáltatások régiónként](https://azure.microsoft.com/regions/#services) elérhető helyről naprakész tájékoztatást.

## <a name="vm-sizes"></a>A virtuális gépek mérete

Ha áttelepítés prémium szintű felügyelt lemez, hogy frissítse a virtuális gép méretét prémium szintű Storage képes a rendelkezésre álló terület a régióban, ahol a virtuális gép. Tekintse át a Virtuálisgép-méretek, amelyek a prémium szintű Storage-kompatibilis. Az Azure virtuális gép mérete paramétereknek szereplő [virtuális gépek méretei](sizes.md).
Tekintse át a virtuális gépek, amely együttműködik a prémium szintű Storage, és válassza ki a leginkább megfelelő virtuális gép méretét, amely a legjobban megfelel a számítási feladatok teljesítményétől. Győződjön meg arról, hogy nincs elegendő sávszélesség érhető el a virtuális Gépet, a lemez forgalom alapjául.

## <a name="disk-sizes"></a>Lemezméretek

**Prémium szintű felügyelt lemez**

Hét különböző típusú együtt a virtuális Gépet felügyelt prémium lemezeket, és mindegyik rendelkezik-e adott iops-érték és átviteli korlátok. Vegye figyelembe a működés felső korlátjának kiválasztása a prémium szintű lemez a virtuális gép alapján a kapacitás, a teljesítmény, méretezhetőség az alkalmazás igényeinek megfelelően, és a maximális tölti be.

| Prémium szintű lemezek típusa  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Lemezméret           | 128 GB| 512 GB| 128 GB| 512 GB            | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPS-érték lemezenként       | 120   | 240   | 500   | 2300              | 5000              | 7500              | 7500              | 
| Adattovábbítás lemezenként | 25 MB / s  | 50 MB / s  | 100 MB / s | 150 MB / s | 200 MB / s | 250 MB / s | 250 MB / s |

**Standard szintű felügyelt lemez**

Standard szintű felügyelt lemez együtt a virtuális gép hét típusa van. Azok a különböző kapacitással rendelkeznek, de azonos IOPS és átviteli sebességének korlátai. Válassza ki a standard szintű felügyelt lemez az alkalmazás a kapacitásigények alapján.

| Standard lemez típusa  | S4               | S6               | S10              | S20              | S30              | S40              | S50              | 
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------| 
| Lemezméret           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1024 GB (1 TB)   | 2048 GB (2TB)    | 4095 GB (4 TB)   | 
| IOPS-érték lemezenként       | 500              | 500              | 500              | 500              | 500              | 500             | 500              | 
| Adattovábbítás lemezenként | 60 MB / s | 60 MB / s | 60 MB / s | 60 MB / s | 60 MB / s | 60 MB / s | 60 MB / s | 

## <a name="disk-caching-policy"></a>Lemez gyorsítótárazási házirend

**Prémium szintű felügyelt lemez**

Alapértelmezés szerint a gyorsítótárazási házirend lemez van *csak olvasható* prémium adatok lemezein, és *írható-olvasható* az a prémium szintű operációsrendszer-lemez csatolva a virtuális gép. A konfigurációs beállítás ajánlott az alkalmazás IOs rendszerhez az optimális teljesítmény eléréséhez. Írási műveleteket vagy a csak írható adatlemezek (köztük SQL Server) tiltsa le a lemezt gyorsítótárazás, hogy az alkalmazás jobb teljesítményt érhet el.

## <a name="pricing"></a>Díjszabás

Tekintse át a [kezelt lemezek árképzési](https://azure.microsoft.com/en-us/pricing/details/managed-disks/). Prémium szintű felügyelt lemez árképzési legyen, mint a nem felügyelt Premium lemezek. Azonban a standard szintű felügyelt lemez árképzési más nem felügyelt Standard lemezeknél.



## <a name="next-steps"></a>Következő lépések

- További információ [kezelt lemezek](managed-disks-overview.md)
