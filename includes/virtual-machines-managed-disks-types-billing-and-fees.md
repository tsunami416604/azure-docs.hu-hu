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
ms.openlocfilehash: 6f6d373d6dd21f3fd04ca434678ec798146a0ffa
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69512660"
---
**Kimenő**adatforgalom: [Kimenő](https://azure.microsoft.com/pricing/details/bandwidth/) adatforgalom (az Azure-adatközpontok által kiváltott adatok) a sávszélesség-használatért kell fizetnie.

**Tranzakciók**: A standard szintű felügyelt lemezen elvégezhető tranzakciók száma után számítunk fel díjat. A standard SSD-k esetében minden I/O-művelet a 256 KiB-nál kisebb vagy azzal egyenlő, egyetlen I/O-műveletnek számít. A 256 KiB-nál nagyobb teljesítményű i/O-műveletek a 256 KiB-os méretnél több I/O-műveletnek számítanak. A standard HDD-k esetében minden egyes IO-művelet egyetlen tranzakciónak tekintendő, az I/O-mérettől függetlenül.

A Managed Disks díjszabásáról, beleértve a tranzakciós költségeket is, tekintse meg a [Managed Disks díjszabását](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Ultra Disk VM foglalási díja

Az Azure-beli virtuális gépek képesek jelezni, hogy kompatibilisek-e az ultra Disks szolgáltatással. Az ultra Disk-kompatibilis virtuális gépek dedikált sávszélesség-kapacitást foglalnak magukban a számítási virtuálisgép-példány és a blokk tárolási méretezési egység között a teljesítmény optimalizálása és a késés csökkentése érdekében. Ha ezt a képességet hozzáadja a virtuális géphez, a foglalási díjat csak akkor számítjuk fel, ha a virtuális gépen engedélyezte az ultra Disk képességet, anélkül, hogy az eszközhöz hozzá kellene csatlakoztatni. Ha Ultra lemez van csatlakoztatva az ultra Disk-kompatibilis virtuális géphez, ez a díj nem lesz alkalmazva. Ez a díj a virtuális gép által kiosztott vCPU.

Az ultra Disk díjszabásáról az [Azure Disks díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/managed-disks/) tájékozódhat.