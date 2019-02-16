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
ms.openlocfilehash: 82bfdfd7481b47c08f76d077585f85763e58b87b
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56331220"
---
**Kimenő adatforgalom**: [Kimenő adatforgalom](https://azure.microsoft.com/pricing/details/bandwidth/) (adatok csak az Azure adatközpontok kimenő adatforgaloma) díjak lépnek fel a sávszélesség-használat.

**Tranzakciók**: A tranzakciók, amely tartalmazza a standard szintű felügyelt lemez díjköteles. Az Azure díjat 0.0036 $ / 100 000 tranzakció, a standard szintű HDD. Tranzakciónak számít az adatok tárolóba írása és tárolóból való olvasása is.

Standard SSD-k használata az i/o-egység mérete 256 KB-os. Ha az átvitt adatok kisebb, mint 256 KB-os, akkor számít egy i/o-egységek. Nagyobb méretű i/o-méretek számítanak több i/o-méret 256 KB. Ha például egy 1100 KB i/o öt i/o-egységek számítanak.

Nincs tranzakció a prémium szintű felügyelt lemez számára ingyenesen.

A Managed Disks díjszabása a részletes információkért lásd: [Managed Disks díjszabását ismertető](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disks-vm-reservation-fee"></a>Ultranagy lemezek virtuális gép foglalás díj

Azure virtuális gépek teszi, hogy azt jelzik, ha kompatibilisek-ultranagy lemezekkel rendelkezik. Kompatibilis virtuális gép foglalja le egy ultra lemez dedikált sávszélesség-kapacitást a számítási Virtuálisgép-példány és a teljesítmény optimalizálása és a késés csökkentése érdekében a blokk tárolóskálázási egységben között. Ez a funkció hozzáadása a Foglalás számítunk fel, amelyek csak áll elő, ha ultranagy lemez funkció a virtuális gépen engedélyezve van, anélkül, hogy egy ultra lemez csatolása a virtuális gép eredményeket. Ha egy ultra lemez csatolva van a ultra a lemez a kompatibilis virtuális gépek, nem fogjuk alkalmazni ezt a díjat. Ez a díj a virtuális gép kiépítése vCPU történik.

Tekintse meg a [Azure Disks díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/managed-disks/) ultra a lemez a lemezek díjszabását.