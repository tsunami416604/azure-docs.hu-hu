---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a5e0e459800e7cb57672518597f3d04a74f53118
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008636"
---
Jelenleg az ultra-lemezek további korlátozásokkal rendelkeznek, ezek a következők:

A jelenleg csak az ultra-lemezek számára elérhető infrastruktúra-redundancia lehetőség a rendelkezésre állási zónák. A más redundancia-beállításokat használó virtuális gépek nem csatolhatnak Ultra-lemezt.

Az alábbi táblázat az ultra lemezek régióit ismerteti, valamint a hozzájuk tartozó rendelkezésre állási lehetőségeket:

> [!NOTE]
> Az ezekben a régiókban található egyes rendelkezésre állási zónák nem biztosítanak Ultra-lemezeket.

|Régiók  |Nincs infrastruktúra-redundancia  |Rendelkezésre állási zónák  |
|---------|---------|---------|
|USA nyugati régiója     |Igen         |Nem         |
|USA nyugati régiója, 2.    |Nem         |Igen         |
|USA keleti régiója     |Nem         |Igen         |
|USA 2. keleti régiója     |Nem         |Igen         |
|Délkelet-Ázsia     |Nem         |Igen         |
|Észak-Európa     |Nem         |Igen         |
|Nyugat-Európa     |Nem         |Igen         |
|Az Egyesült Királyság déli régiója     |Nem         |Igen         |

- Csak a következő virtuálisgép-sorozatokban támogatott:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Nem minden virtuálisgép-méret érhető el minden olyan támogatott régióban, ahol az ultra Disks
- Csak adatlemezként érhetők el, és csak a 4k fizikai szektor méretének támogatásához. Az ultra-lemez 4K-os natív szektorának mérete miatt bizonyos alkalmazások nem lesznek kompatibilisek az ultra Disks szolgáltatással. Az ultra-lemezek támogatásához az egyik példa a Oracle Database, amely a 12,2-as vagy újabb kiadást igényli.  
- Csak üres lemezként hozható létre  
- Jelenleg nem támogatja a lemezes pillanatképeket, a virtuálisgép-lemezképeket, a rendelkezésre állási csoportokat, az Azure dedikált gazdagépeket vagy az Azure Disk Encryption
- Jelenleg nem támogatja az integrációt Azure Backup vagy Azure Site Recovery
- A GA virtuális gépek IOPS jelenlegi maximális korlátja 80 000.

Az Azure Ultra Disks szolgáltatás alapértelmezés szerint akár 16 TiB-előfizetést is kínál, de az ultra-lemezek kéréssel magasabb szintű kapacitást is nyújtanak. A kapacitás növelésének igényléséhez forduljon az Azure ügyfélszolgálatához.