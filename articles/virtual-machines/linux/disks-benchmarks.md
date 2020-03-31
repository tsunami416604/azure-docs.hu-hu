---
title: Az alkalmazás teljesítményezése az Azure Disk Storage szolgáltatásban
description: Ismerje meg az alkalmazás azure-beli teljesítményértékelésének folyamatát.
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: conceptual
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: 640ec54e9634751d05c2cea90d7c03d02e7a3387
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75720055"
---
# <a name="benchmarking-a-disk"></a>Lemez teljesítményezése

A teljesítményértékelés az alkalmazás különböző számítási feladatainak szimulálása és az egyes számítási feladatok alkalmazásteljesítményének mérése. A nagy teljesítményű [cikk tervezésében](premium-storage-performance.md)ismertetett lépések használata . Az alkalmazást üzemeltető virtuális gépeken futó teljesítményértékelési eszközök segítségével meghatározhatja, hogy az alkalmazás milyen teljesítményszinteket érhet el a Prémium szintű storage szolgáltatással. Ebben a cikkben példákat nyújtunk be az Azure Premium Storage-lemezekkel kiépített standard DS14 virtuális gépek teljesítményértékelésére.

Az Iometer és a FIO közös teljesítményértékelési eszközöket használtunk Windows és Linux rendszerhez. Ezek az eszközök több szálat hoznak létre egy éles környezetben, például a munkaterhelést szimulálva, és mérik a rendszer teljesítményét. Az eszközök segítségével olyan paramétereket is konfigurálhat, mint a blokkméret és a várólista mélysége, amelyeket általában nem módosíthat egy alkalmazásesetében. Ez nagyobb rugalmasságot biztosít a maximális teljesítmény biztosításához egy nagy méretű virtuális gép emelt szintű lemezekkel a különböző típusú alkalmazás-számítási feladatok. Ha többet szeretne megtudni az egyes teljesítményértékelési eszközökről, látogasson el [az Iometer](http://www.iometer.org/) és [a FIO oldalra.](http://freecode.com/projects/fio)

Az alábbi példák követéséhez hozzon létre egy szabványos DS14 virtuális gép, és csatolja 11 prémium szintű storage-lemezek a virtuális géphez. A 11 lemez közül állítson be 10 lemezt "Nincs" típusú állomásgyorsítótárazásával, és csíkozzák őket egy NoCacheWrites nevű kötetté. Konfigurálja az állomásgyorsítótárazást "Csak olvasás" állapotban a fennmaradó lemezen, és hozzon létre egy CacheReads nevű kötetet ezzel a lemezzel. Ezzel a beállítással láthatja a szabványos DS14 virtuális gép maximális olvasási és írási teljesítményét. A DS14-es virtuális gép prémium szintű lemezekkel való létrehozásának részletes lépéseit a [Tervezés nagy teljesítményérdekében](premium-storage-performance.md)című segédprogram című verzióban található.

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>További lépések

Folytassa a cikket [tervezése a nagy teljesítményű](premium-storage-performance.md).

Ebben a cikkben hozzon létre egy ellenőrzőlistát hasonló a meglévő alkalmazás a prototípushoz. A teljesítményértékelési eszközök segítségével szimulálhatja a számítási feladatokat, és mérheti a teljesítményt a prototípus alkalmazás. Ezzel meghatározhatja, hogy melyik lemezajánlat felelmeg vagy haladhatja meg az alkalmazás teljesítménykövetelményeit. Ezután ugyanazokat az irányelveket valósíthatja meg az éles alkalmazáshoz.