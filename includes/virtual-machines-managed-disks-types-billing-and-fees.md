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
ms.openlocfilehash: 2303d36e93cecfca03894a8b0e55458c03b13d78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73412974"
---
**Kimenő adatátvitelek:** [A kimenő adatátvitelek](https://azure.microsoft.com/pricing/details/bandwidth/) (az Azure-adatközpontokból kimenő adatok) sávszélesség-használat miatt számlázást vonnak maga után.

**Tranzakciók**: A szabványos felügyelt lemezen végrehajtott tranzakciók számát számlázunk. Szabványos SSD-k esetén minden 256 KiB átviteli képesvagy azzal egyenlő I/O-művelet egyetlen I/O-műveletnek minősül. A 256 KiB-nél nagyobb I/O-műveletek több 256 KiB méretű I/O-nak számítanak. Standard HDD-k esetén minden I/O-művelet egyetlen tranzakciónak számít, függetlenül az I/O méretétől.

A felügyelt lemezek díjszabásáról, beleértve a tranzakciós költségeket is, a [Felügyelt lemezek díjszabása](https://azure.microsoft.com/pricing/details/managed-disks)című témakörben talál részletes információt.

### <a name="ultra-disk-vm-reservation-fee"></a>Ultra lemezes virtuális gép foglalási díja

Az Azure virtuális gépek képesek jelezni, ha azok kompatibilisek az ultralemezek. Az ultralemez-kompatibilis virtuális gép dedikált sávszélesség-kapacitást foglal le a számítási virtuálisgép-példány és a blokktároló méretezési egység között a teljesítmény optimalizálása és a késés csökkentése érdekében. Ez a funkció hozzáadása a virtuális gép hez egy foglalási díjat eredményez, amely csak akkor írható ki, ha engedélyezte az ultra lemez képesség a virtuális gép anélkül, hogy egy ultra lemezt csatlakoztatna hozzá. Ha egy ultra lemez csatlakozik az ultra lemez kompatibilis virtuális gép, ez a töltés nem lesz alkalmazva. Ez a díj a virtuális gépen kiosztott vCPU-nként történik. 

> [!Note]
> A [korlátozott alapvető virtuális gép méretei](../articles/virtual-machines/linux/constrained-vcpu.md)esetén a foglalási díj a vCPU-k tényleges számán alapul, nem pedig a korlátozott magokon. A Standard_E32 8s_v3 esetén a foglalási díj 32 magon alapul. 

Tekintse meg az [Azure Disks díjszabási lap](https://azure.microsoft.com/pricing/details/managed-disks/) ultra lemez díjszabási részleteket.

### <a name="azure-disk-reservation"></a>Azure-lemezfoglalás

A lemezfoglalás az a lehetőség, hogy egy éves lemezes tárhelyet vásároljon előre, kedvezményes áron, csökkentve a teljes költséget. Lemezfoglalás vásárlásakor egy adott lemeztermékváltozatot kell kiválasztania egy célrégióban, például 10 P30 (1TiB) prémium Szintű SSD-t az USA keleti régiójában 2 régióban egy éves időtartamra. A foglalási élmény hasonló a fenntartott virtuálisgép-példányokhoz. A virtuális gép- és lemezfoglalások összekötegelésével maximalizálhatja a megtakarításokat. Az Azure Disks Reservation egyéves kötelezettségvállalási tervet kínál a P30 (1TiB) és a P80 (32 TiB) között az összes éles régióban egyéves kötelezettségvállalási tervhez. A fenntartott lemezek díjszabásával kapcsolatos további információkért tekintse meg az [Azure Disks díjszabási lapját.](https://azure.microsoft.com/pricing/details/managed-disks/)