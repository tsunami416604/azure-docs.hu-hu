---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/25/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: aa9327bd0ba6763aa4e89630611aabb3c5195655
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85448739"
---
Jelenleg az ultra-lemezek további korlátozásokkal rendelkeznek, ezek a következők:

A jelenleg csak az ultra-lemezek számára elérhető infrastruktúra-redundancia lehetőség a rendelkezésre állási zónák. A más redundancia-beállításokat használó virtuális gépek nem csatolhatnak Ultra-lemezt.

Az alábbi táblázat az ultra lemezek régióit ismerteti, valamint a hozzájuk tartozó rendelkezésre állási lehetőségeket:

> [!NOTE]
> Ha az alábbi listán szereplő egyik régió nem rendelkezik a lemezre alkalmas rendelkezésre állási zónákkal, akkor az adott régióban lévő virtuális gépeket infrastruktúra-redundancia nélkül kell üzembe helyezni az ultra-lemez csatlakoztatása érdekében.

|Régiók  |Az ultra-lemezeket támogató rendelkezésre állási zónák száma  |
|---------|---------|
|USA-beli államigazgatás – Virginia     |None         |
|USA déli középső régiója     |None         |
|USA középső régiója     |Három zóna         |
|USA nyugati régiója     |None         |
|USA nyugati régiója, 2.    |Három zóna         |
|USA keleti régiója     |Három zóna         |
|USA 2. keleti régiója     |Két zóna         |
|Délkelet-Ázsia     |Három zóna         |
|Észak-Európa     |Három zóna          |
|Nyugat-Európa     |Három zóna          |
|Az Egyesült Királyság déli régiója     |Három zóna          |
|Kelet-Japán     |Két zóna         |
|Közép-Franciaország    |Két zóna        |


- Csak a következő virtuálisgép-sorozatokban támogatott:
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Nem minden virtuálisgép-méret érhető el minden olyan támogatott régióban, ahol az ultra Disks
- Csak adatlemezként érhetők el, és csak a 4k fizikai szektor méretének támogatásához. Az ultra-lemez 4K-os natív szektorának mérete miatt bizonyos alkalmazások nem lesznek kompatibilisek az ultra Disks szolgáltatással. Az ultra-lemezek támogatásához az egyik példa a Oracle Database, amely a 12,2-as vagy újabb kiadást igényli.  
- Csak üres lemezként hozható létre  
- Jelenleg nem támogatja a lemezes pillanatképeket, a virtuálisgép-lemezképeket, a rendelkezésre állási csoportokat, az Azure dedikált gazdagépeket vagy az Azure Disk Encryption
- Jelenleg nem támogatja az integrációt Azure Backup vagy Azure Site Recovery
- Csak a nem gyorsítótárazott olvasások és a nem gyorsítótárazott írások támogatása
- A GA virtuális gépek IOPS jelenlegi maximális korlátja 80 000.

Az Azure Ultra Disks szolgáltatás alapértelmezés szerint akár 16 TiB-előfizetést is kínál, de az ultra-lemezek kéréssel magasabb szintű kapacitást is nyújtanak. A kapacitás növelésének igényléséhez forduljon az Azure ügyfélszolgálatához.