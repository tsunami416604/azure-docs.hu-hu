---
title: Az alkalmazás teljesítményértékelése Azure Disk Storage
description: Ismerje meg az Azure-beli alkalmazás teljesítménytesztének folyamatát.
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: d8bf90f4ecea992b3212200905cd9204e4079b1b
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88683963"
---
# <a name="benchmarking-a-disk"></a>Lemez teljesítményértékelése

A teljesítményértékelés a különböző számítási feladatok szimulálása az alkalmazáson, és az egyes számítási feladatok teljesítményének mérése. A [nagy teljesítményű cikk tervezésekor](../premium-storage-performance.md)leírt lépések használatával összegyűjtöttük az alkalmazás teljesítményének követelményeit. Ha az alkalmazást futtató virtuális gépeken teljesítményteszt-eszközöket futtat, megadhatja, hogy az alkalmazás milyen teljesítményszint mellett érhet el Premium Storage. Ebben a cikkben példát mutatunk az Azure Premium Storage-lemezekkel kiépített standard DS14 virtuális gépek összehasonlítására.

A Windows és a Linux rendszerhez készült közös teljesítményteszt-eszközöket Iometer és FIO használjuk. Ezek az eszközök több szálat szimulálnak, mint például a számítási feladatok, és mérik a rendszer teljesítményét. Az eszközök használatával olyan paramétereket is konfigurálhat, mint például a blokk mérete és a várólista mélysége, amelyet általában nem lehet módosítani egy alkalmazás esetében. Ez nagyobb rugalmasságot biztosít, hogy a maximális teljesítményt egy prémium szintű lemezekkel kiépített, nagy léptékű virtuális gépeken lehessen vezetni, különböző típusú alkalmazás-munkaterhelések esetén. Ha többet szeretne megtudni az egyes teljesítményértékelési eszközökről, látogasson el a [Iometer](http://www.iometer.org/) és a [Fio](http://freecode.com/projects/fio)webhelyre.

Az alábbi példák követéséhez hozzon létre egy standard DS14 virtuális gépet, és csatoljon 11 Premium Storage lemezt a virtuális géphez. A 11 lemez esetében állítsa be a 10 lemezt a "None" értékkel rendelkező gazdagép-gyorsítótárazással, majd egy NoCacheWrites nevű kötetre. Konfigurálja a gazdagép gyorsítótárazását "ReadOnly" néven a fennmaradó lemezen, és hozzon létre egy CacheReads nevű kötetet a lemezzel. Ezzel a beállítással megtekintheti a standard DS14 virtuális gép maximális olvasási és írási teljesítményét. A prémium szintű SSD-k használatával létrehozott DS14 virtuális gépek létrehozásával kapcsolatos részletes információkért tekintse meg a [tervezés a nagy teljesítmény](../premium-storage-performance.md)érdekében című témakört.

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>További lépések

Folytassa a cikket a [nagy teljesítmény kialakításához](../premium-storage-performance.md).

Ebben a cikkben a prototípushoz tartozó meglévő alkalmazáshoz hasonló ellenőrzőlistát hoz létre. A teljesítményértékelési eszközök használatával szimulálhatja a számítási feladatokat, és mérhetővé teheti a teljesítményt a prototípus alkalmazásban. Ezzel meghatározhatja, hogy melyik lemez-ajánlat megfelel vagy meghaladja az alkalmazás teljesítményére vonatkozó követelményeket. Ezután megadhatja az éles alkalmazásra vonatkozó irányelveket.