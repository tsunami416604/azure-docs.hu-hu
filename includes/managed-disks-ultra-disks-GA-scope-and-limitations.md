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
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008636"
---
Most, ultra lemezek további korlátozások, ezek a következők:

Az ultralemezek számára jelenleg csak a rendelkezésre állási zónák érhetők el infrastruktúra-redundancia-beállítások. Más redundanciabeállításokat használó virtuális gépek nem tudnak ultralemezt csatolni.

Az alábbi táblázat ismerteti az ultralemezek régióit, valamint a megfelelő rendelkezésre állási lehetőségeket:

> [!NOTE]
> Ezeken a régiókon belül néhány rendelkezésre állási zóna nem kínál ultralemezeket.

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

- Csak a következő virtuálisgép-sorozatban támogatottak:
    - [ESv3 között](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3 között](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2 között](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Nem minden virtuális gép méret érhető el minden támogatott régióban ultralemezekkel
- Csak adatlemezként érhetők el, és csak a 4k fizikai szektorméretet támogatják. Az Ultra Disk 4K natív szektormérete miatt vannak olyan alkalmazások, amelyek nem kompatibilisek az ultralemezekkel. Erre példa az Oracle Database, amely 12.2-es vagy újabb kiadást igényel az ultralemezek támogatásához.  
- Csak üres lemezként hozható létre  
- Jelenleg nem támogatja a lemezpillanatképeket, a virtuálisgép-lemezképeket, a rendelkezésre állási csoportokat, az Azure dedikált gazdagépeket vagy az Azure lemeztitkosítást
- Jelenleg nem támogatja az Azure Backup vagy az Azure Site Recovery szolgáltatással való integrációt
- A ga-virtuális gépeki IOPS jelenlegi maximális korlátja 80 000.

Az Azure ultra lemezek alapértelmezés szerint alapértelmezés szerint régiónként és előfizetésenként akár 16 TiB-t is kínálnak, de az ultralemezek kérésre nagyobb kapacitást támogatnak. A kapacitás növelésének kéréséhez forduljon az Azure-támogatáshoz.