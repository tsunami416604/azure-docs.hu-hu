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
ms.openlocfilehash: 0d771f03f9f71151ef25140148d4dd4daf3d46ec
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56443424"
---
**Kimenő adatforgalom**: [Kimenő adatforgalom](https://azure.microsoft.com/pricing/details/bandwidth/) (adatok csak az Azure adatközpontok kimenő adatforgaloma) díjak lépnek fel a sávszélesség-használat.

**Tranzakciók**: A tranzakciók, amely tartalmazza a standard szintű felügyelt lemez díjköteles. Az Azure díjat 0.0036 $ / 100 000 tranzakció, a standard szintű HDD. Tranzakciónak számít az adatok tárolóba írása és tárolóból való olvasása is.

Standard SSD-k használata az i/o-egység mérete 256 KB-os. Ha az átvitt adatok kisebb, mint 256 KB-os, akkor számít egy i/o-egységek. Nagyobb méretű i/o-méretek számítanak több i/o-méret 256 KB. Ha például egy 1100 KB i/o öt i/o-egységek számítanak.

Nincs tranzakció a prémium szintű felügyelt lemez számára ingyenesen.

A Managed Disks díjszabása a részletes információkért lásd: [Managed Disks díjszabását ismertető](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-ssd-vm-reservation-fee"></a>Ultranagy SSD Virtuálisgép-foglalás díj

Az Azure virtuális gépek teszi, hogy azt jelzik, ha kompatibilisek ultranagy SSD rendelkeznek. Kompatibilis virtuális gép foglalja le egy ultra lemez dedikált sávszélesség-kapacitást a számítási Virtuálisgép-példány és a teljesítmény optimalizálása és a késés csökkentése érdekében a blokk tárolóskálázási egységben között. Ez a funkció hozzáadása a Foglalás számítunk fel, amelyek csak áll elő, ha ultranagy lemez funkció a virtuális gépen engedélyezve van, anélkül, hogy egy ultra lemez csatolása a virtuális gép eredményeket. Ha egy ultra lemez csatolva van a ultra a lemez a kompatibilis virtuális gépek, nem fogjuk alkalmazni ezt a díjat. Ez a díj a virtuális gép kiépítése vCPU történik.

Tekintse meg a [Azure Disks díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/managed-disks/) ultra a lemez a lemezek díjszabását.