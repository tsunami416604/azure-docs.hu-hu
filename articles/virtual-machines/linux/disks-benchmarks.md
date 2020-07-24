---
title: Az alkalmazás teljesítményértékelése Azure Disk Storage
description: Ismerje meg az Azure-beli alkalmazás teljesítménytesztének folyamatát.
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: how-to
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: c1dfcad5a4fb6611543e6421e82889f239f14951
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87010826"
---
# <a name="benchmark-your-application-on-azure-disk-storage"></a>Az alkalmazás teljesítményértékelése Azure Disk Storage

A teljesítményértékelés a különböző számítási feladatok szimulálása az alkalmazáson, és az egyes számítási feladatok teljesítményének mérése. A [nagy teljesítményű cikk tervezése című cikkben](premium-storage-performance.md)ismertetett lépések segítségével. Ha az alkalmazást futtató virtuális gépeken teljesítményteszt-eszközöket futtat, megadhatja, hogy az alkalmazás milyen teljesítményszint mellett érhet el Premium Storage. Ebben a cikkben példát mutatunk az Azure Premium Storage-lemezekkel kiépített standard DS14 virtuális gépek összehasonlítására.

A Windows és a Linux rendszerhez készült közös teljesítményteszt-eszközöket Iometer és FIO használjuk. Ezek az eszközök több szálat szimulálnak, mint például a számítási feladatok, és mérik a rendszer teljesítményét. Az eszközök használatával olyan paramétereket is konfigurálhat, mint például a blokk mérete és a várólista mélysége, amelyet általában nem lehet módosítani egy alkalmazás esetében. Ez nagyobb rugalmasságot biztosít, hogy a maximális teljesítményt egy prémium szintű lemezekkel kiépített, nagy léptékű virtuális gépeken lehessen vezetni, különböző típusú alkalmazás-munkaterhelések esetén. Ha többet szeretne megtudni az egyes teljesítményértékelési eszközökről, látogasson el a [Iometer](http://www.iometer.org/) és a [Fio](http://freecode.com/projects/fio)webhelyre.

Az alábbi példák követéséhez hozzon létre egy standard DS14 virtuális gépet, és csatoljon 11 Premium Storage lemezt a virtuális géphez. A 11 lemez esetében állítsa be a 10 lemezt a "None" értékkel rendelkező gazdagép-gyorsítótárazással, majd egy NoCacheWrites nevű kötetre. Konfigurálja a gazdagép gyorsítótárazását "ReadOnly" néven a fennmaradó lemezen, és hozzon létre egy CacheReads nevű kötetet a lemezzel. Ezzel a beállítással megtekintheti a standard DS14 virtuális gép maximális olvasási és írási teljesítményét. A prémium szintű lemezekkel rendelkező DS14-alapú virtuális gépek létrehozásával kapcsolatos részletes információkért tekintse meg a [tervezés a nagy teljesítmény](premium-storage-performance.md)érdekében című témakört.

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>További lépések

Folytassa a cikket a [nagy teljesítmény kialakításához](premium-storage-performance.md).

Ebben a cikkben a prototípushoz tartozó meglévő alkalmazáshoz hasonló ellenőrzőlistát hoz létre. A teljesítményértékelési eszközök használatával szimulálhatja a számítási feladatokat, és mérhetővé teheti a teljesítményt a prototípus alkalmazásban. Ezzel meghatározhatja, hogy melyik lemez-ajánlat megfelel vagy meghaladja az alkalmazás teljesítményére vonatkozó követelményeket. Ezután megadhatja az éles alkalmazásra vonatkozó irányelveket.