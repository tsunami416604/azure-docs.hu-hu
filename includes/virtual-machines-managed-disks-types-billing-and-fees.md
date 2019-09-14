---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a416d1c6e813be558f034e15576c57efa6073788
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968581"
---
**Kimenő**adatforgalom: [Kimenő](https://azure.microsoft.com/pricing/details/bandwidth/) adatforgalom (az Azure-adatközpontok által kiváltott adatok) a sávszélesség-használatért kell fizetnie.

**Tranzakciók**: A standard szintű felügyelt lemezen elvégezhető tranzakciók száma után számítunk fel díjat. A standard SSD-k esetében minden I/O-művelet a 256 KiB-nál kisebb vagy azzal egyenlő, egyetlen I/O-műveletnek számít. A 256 KiB-nál nagyobb teljesítményű i/O-műveletek a 256 KiB-os méretnél több I/O-műveletnek számítanak. A standard HDD-k esetében minden egyes IO-művelet egyetlen tranzakciónak tekintendő, az I/O-mérettől függetlenül.

A Managed Disks díjszabásáról, beleértve a tranzakciós költségeket is, tekintse meg a [Managed Disks díjszabását](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Ultra Disk VM foglalási díja

Az Azure-beli virtuális gépek képesek jelezni, hogy kompatibilisek-e az ultra Disks szolgáltatással. Az ultra Disk-kompatibilis virtuális gépek dedikált sávszélesség-kapacitást foglalnak magukban a számítási virtuálisgép-példány és a blokk tárolási méretezési egység között a teljesítmény optimalizálása és a késés csökkentése érdekében. Ha ezt a képességet hozzáadja a virtuális géphez, a foglalási díjat csak akkor számítjuk fel, ha a virtuális gépen engedélyezte az ultra Disk képességet, anélkül, hogy az eszközhöz hozzá kellene csatlakoztatni. Ha Ultra lemez van csatlakoztatva az ultra Disk-kompatibilis virtuális géphez, ez a díj nem lesz alkalmazva. Ez a díj a virtuális gép által kiosztott vCPU. 

> [!Note]
> A [korlátozott alapszintű](../articles/virtual-machines/linux/constrained-vcpu.md)virtuálisgép-méretek esetében a foglalás díja a vCPU tényleges számától, és nem a korlátozott magoktól függ. A Standard_E32-8s_v3 esetében a foglalás díja a 32 mag alapján történik. 

Az ultra Disk díjszabásáról az [Azure Disks díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/managed-disks/) tájékozódhat.
