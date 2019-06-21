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
ms.openlocfilehash: 268467796e67caf2d20fedb44d83fd455a09b83e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179106"
---
**Kimenő adatforgalom**: [Kimenő adatforgalom](https://azure.microsoft.com/pricing/details/bandwidth/) (adatok csak az Azure adatközpontok kimenő adatforgaloma) díjak lépnek fel a sávszélesség-használat.

**Tranzakciók**: A tranzakciók, amely tartalmazza a standard szintű felügyelt lemez díjköteles. Standard SSD-k, az egyes i/o műveletek kisebb vagy egyenlő, mint 256 átviteli KiB számít egy egyetlen i/o-művelet. Az átviteli sebesség nagyobb, mint 256 KiB műveletei számítanak több i/o-mérete 256 KiB. Standard HDD minden egyes i/o-művelet különálló tranzakcióként, függetlenül az i/o-mérete számít történik.

Részletes információk a Managed Disks, többek között a tranzakciós költségek, a díjszabással: [Managed Disks díjszabását ismertető](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-ssd-vm-reservation-fee"></a>Ultranagy SSD Virtuálisgép-foglalás díj

Az Azure virtuális gépek teszi, hogy azt jelzik, ha kompatibilisek ultranagy SSD rendelkeznek. Kompatibilis virtuális gép foglalja le egy ultra lemez dedikált sávszélesség-kapacitást a számítási Virtuálisgép-példány és a teljesítmény optimalizálása és a késés csökkentése érdekében a blokk tárolóskálázási egységben között. Ez a funkció hozzáadása a Foglalás számítunk fel, amelyek csak áll elő, ha ultranagy lemez funkció a virtuális gépen engedélyezve van, anélkül, hogy egy ultra lemez csatolása a virtuális gép eredményeket. Ha egy ultra lemez csatolva van a ultra a lemez a kompatibilis virtuális gépek, nem fogjuk alkalmazni ezt a díjat. Ez a díj a virtuális gép kiépítése vCPU történik.

Tekintse meg a [Azure Disks díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/managed-disks/) ultra a lemez a lemezek díjszabását.