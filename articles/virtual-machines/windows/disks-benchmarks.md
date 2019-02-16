---
title: A teljesítménytesztek az alkalmazás az Azure Disk Storage – felügyelt lemezek
description: Ismerje meg az alkalmazás az Azure-ban a teljesítménytesztek folyamatán.
services: virtual-machines-windows,storage
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: article
ms.service: virtual-machines-windows
ms.tgt_pltfrm: windows
ms.subservice: disks
ms.openlocfilehash: 8db1fb3c9b3ed551cd668cf14105eb8bfb486251
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56331268"
---
# <a name="benchmarking-a-disk"></a>A teljesítménytesztek lemez

A teljesítménytesztek az a folyamat számos különböző számítási feladatok az alkalmazás és az egyes munkaterhelésekhez tartozó az alkalmazás teljesítményének méréséhez. Az ismertetett lépéseket követve a [nagy teljesítményű cikk tervezése](premium-storage-performance.md), teljesítmény-követelmények kigyűjtötte. Teljesítménymérési eszközökről futtatja az alkalmazást futtató virtuális gépeken, megadhatja, hogy a teljesítményi szint, amely az alkalmazás a Premium Storage segítségével érheti el. Ebben a cikkben biztosítunk egy Standard DS14 virtuális gép kiépítése az Azure Premium Storage-lemez teljesítményértékelési példákat.

A Microsoft rendelkezik használt közös teljesítménymérési eszközökről Iometer és FIO, Windows és Linux rendszereken. Ezek az eszközök elindítanak egy éles számítási feladat például szimuláló több szálon, és a rendszer teljesítményének mérésére. Az eszközök használatával is konfigurálhatja blokk mérete és a várólista mélysége, amelyek normál esetben nem módosítható az alkalmazás paraméterek. Ez nagyobb rugalmasságot biztosít, prémium szintű lemezek a különböző típusú alkalmazások számítási feladatai együtt üzembe helyezett virtuális gépek nagy léptékű a maximális teljesítmény érdekében. Minden egyes teljesítménymérési eszköz olvashat további [Iometer](http://www.iometer.org/) és [FIO](http://freecode.com/projects/fio).

Kövesse az alábbi példák, hozzon létre egy Standard DS14 virtuális Gépet, és 11 Premium Storage-lemez csatolása a virtuális gép. A 11 lemezek 10 lemez konfigurálása az állomás-gyorsítótárazás, "None", és stripe-NoCacheWrites nevű kötet be őket. Állomás-gyorsítótárazás "Csak olvasható", a fennmaradó lemez konfigurálása, és hozzon létre egy kötetet CacheReads nevű ezt a lemezt. Ezt a beállítást használja, Ön láthatja a maximális olvasási és írási teljesítményt, a Standard DS14 virtuális gépről. Lépjen a prémium szintű SSD-kkel rendelkező virtuális gép – DS14 létrehozásáról részletes lépéseket [magas teljesítmény-központú tervezés](premium-storage-performance.md).

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>További lépések

Haladjon végig a tervezési, nagy teljesítményű cikkhez. Az, létrehoz egy feladatlista hasonlóak a meglévő alkalmazás a prototípus. Benchmarking eszközökkel a számítási feladatokat szimulálhat, és mérhetik a teljesítményt, a prototípus-alkalmazás. Ezzel a módszerrel megadhatja, hogy mely lemezek ajánlat megfelelő, vagy a előremutató alkalmazás kielégítéséhez. Ezután az alkalmazás éles üzemét ugyanez az útmutató is alkalmazható.

> [!div class="nextstepaction"]
> Tekintse meg a cikket a [magas teljesítmény-központú tervezés](premium-storage-performance.md) megkezdéséhez.