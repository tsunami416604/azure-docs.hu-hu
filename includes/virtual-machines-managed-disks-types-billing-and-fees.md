---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c0627dd0833e3b20468eb5f50fbeb9fd9d9ae2b3
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88864729"
---
**Kimenő**adatforgalom: a [kimenő](https://azure.microsoft.com/pricing/details/bandwidth/) adatforgalom (az Azure-adatközpontok által kiváltott adatok) a sávszélesség-használatért kell fizetnie.

**Tranzakciók**: a standard szintű felügyelt lemezeken végrehajtott tranzakciók száma. A standard SSD-k esetében minden I/O-művelet a 256 KiB-nál kisebb vagy azzal egyenlő, egyetlen I/O-műveletnek számít. A 256 KiB-nál nagyobb teljesítményű i/O-műveletek a 256 KiB-os méretnél több I/O-műveletnek számítanak. A standard HDD-k esetében minden egyes IO-művelet egyetlen tranzakciónak tekintendő, az I/O-mérettől függetlenül.

A Managed Disks díjszabásáról, beleértve a tranzakciós költségeket is, tekintse meg a [Managed Disks díjszabását](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Ultra Disk VM foglalási díja

Az Azure-beli virtuális gépek képesek jelezni, hogy kompatibilisek-e az ultra Disks szolgáltatással. Az ultra Disk-kompatibilis virtuális gépek dedikált sávszélesség-kapacitást foglalnak magukban a számítási virtuálisgép-példány és a blokk tárolási méretezési egység között a teljesítmény optimalizálása és a késés csökkentése érdekében. Ha ezt a képességet hozzáadja a virtuális géphez, a foglalási díjat csak akkor számítjuk fel, ha a virtuális gépen engedélyezte az ultra Disk képességet, anélkül, hogy az eszközhöz hozzá kellene csatlakoztatni. Ha Ultra lemez van csatlakoztatva az ultra Disk-kompatibilis virtuális géphez, ez a díj nem lesz alkalmazva. Ez a díj a virtuális gép által kiosztott vCPU. 

> [!Note]
> A [korlátozott alapszintű](../articles/virtual-machines/constrained-vcpu.md)virtuálisgép-méretek esetében a foglalás díja a vCPU tényleges számától, és nem a korlátozott magoktól függ. Standard_E32-8s_v3 esetén a foglalás díja 32 mag lesz. 

Az ultra Disk díjszabásáról az [Azure Disks díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/managed-disks/) tájékozódhat.

### <a name="azure-disk-reservation"></a>Azure-lemezek foglalása

A lemezes foglalás az a lehetőség, hogy a lemezes tárterületet egy évig előre, kedvezményes áron vásárolja meg, ami csökkenti a teljes költségeket. A lemezes foglalások megvásárlásakor kiválaszthat egy adott lemezes SKU-t egy adott régióban, például a 10 P30 (1TiB) prémium SSD-ket az USA 2. keleti régiójában egy éves időszakra. A foglalási élmény a fenntartott virtuális gépek (VM) példányaihoz hasonló. A megtakarítások maximalizálása érdekében a virtuális gépek és a lemezek foglalása is megtehető. Az Azure-lemezek foglalása egyéves kötelezettségvállalási tervet biztosít a P30 (1TiB) prémium SSD-SKU-k számára a P80 (32 TiB) és az összes éles régióban. A fenntartott lemezek díjszabásával kapcsolatos további információkért lásd az [Azure Disks díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/managed-disks/).