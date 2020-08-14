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
ms.openlocfilehash: 546c21802d275fe99ed2acbf00e32d37db3603f2
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88225196"
---
Jelenleg az ultra-lemezek további korlátozásokkal rendelkeznek, ezek a következők:

A jelenleg csak az ultra-lemezek számára elérhető infrastruktúra-redundancia lehetőség a rendelkezésre állási zónák. A más redundancia-beállításokat használó virtuális gépek nem csatolhatnak Ultra-lemezt.

Az alábbi táblázat az ultra lemezek régióit ismerteti, valamint a hozzájuk tartozó rendelkezésre állási lehetőségeket:

> [!NOTE]
> Ha az alábbi listán szereplő egyik régió nem rendelkezik a lemezre alkalmas rendelkezésre állási zónákkal, akkor az adott régióban lévő virtuális gépeket infrastruktúra-redundancia nélkül kell üzembe helyezni az ultra-lemez csatlakoztatása érdekében.

|Régiók  |Az ultra-lemezeket támogató rendelkezésre állási zónák száma  |
|---------|---------|
|USA-beli államigazgatás – Virginia     |Nincsenek         |
|USA-beli államigazgatás – Arizona     |Nincsenek         |
|USA déli középső régiója     |Nincsenek         |
|USA középső régiója     |Három zóna         |
|USA nyugati régiója     |Nincsenek         |
|USA 2. nyugati régiója    |Három zóna         |
|USA keleti régiója     |Három zóna         |
|USA 2. keleti régiója     |Három zóna         |
|Délkelet-Ázsia     |Három zóna         |
|Kelet-Ázsia     |Nincsenek         |
|Észak-Európa     |Három zóna          |
|Nyugat-Európa     |Három zóna          |
|Az Egyesült Királyság déli régiója     |Három zóna          |
|Kelet-Japán     |Három zóna         |
|Közép-Franciaország    |Két zóna        |
|Dél-Brazília    |Nincsenek        |
|Kelet-Ausztrália    |Három zóna        |
|Közép-Kanada *    |Három zóna        |

\* Vegye fel a kapcsolatot az Azure támogatási szolgálatával, hogy hozzáférhessen a régió Availability Zoneséhez.

- Csak a következő virtuálisgép-sorozatokban támogatott:
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [Easv4](../articles/virtual-machines/eav4-easv4-series.md#easv4-series)
    - [Edsv4](../articles/virtual-machines/edv4-edsv4-series.md#edsv4-series)
    - [Esv4](../articles/virtual-machines/ev4-esv4-series.md#esv4-series)
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