---
title: Microsoft Azure FXT Edge Filer – áttekintés
description: Az Azure FXT Edge Filer hibrid tárolási gyorsítótárának ismertetése, amely egy aktív archív és fájl-hozzáférés-gyorsító megoldás a nagy teljesítményű számítástechnikához
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: acf35015ff7851f7ea2a2527852be2573512e35d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "72254843"
---
# <a name="what-is-azure-fxt-edge-filer-hybrid-storage-cache"></a>Mi az Azure FXT Edge Filer hibrid tárolási gyorsítótára?

Az Azure FXT Edge Filer egy hibrid tárológyorsítótár-kezelő berendezés, amely gyors fájlhozzáférést és aktív archiválást biztosít a nagy teljesítményű számítástechnikai (HPC) feladatokhoz.

Több adatforrással is működik, függetlenül attól, hogy egy helyi adatközpontban, távolról vagy a felhőben tárolja. Az Azure FXT Edge Filer egységes névteret biztosíthat a különböző tárolórendszerekben lévő adatok számára.

Három vagy több FXT Edge Filer hardvereszköz fürtözött fájlrendszerként működik együtt a gyorsítótár biztosításához. A szükséges hardver ek beszerzésével kapcsolatos részletekért forduljon a Microsoft képviselőjéhez. 

További információt az [Azure FXT Edge Filer](https://azure.microsoft.com/services/fxt-edge-filer/)terméktájékoztatójában és adatlapjában talál.

## <a name="use-cases"></a>Használati esetek

Az Azure FXT Edge Filer az alábbihoz hasonló munkafolyamatokhoz a legjobb termelékenységet növeli:

* Olvasási nehéz fájlhozzáférési munkafolyamat 
* NFSv3 vagy SMB2 protokollok
* 1000–100 000 CPU-mag számítási farmjai

### <a name="nas-optimization-and-scaling"></a>NAS optimalizálás és méretezés

Az Azure FXT Edge Filer gyorsítótárhasználatával zökkenőmentesen hozzáférhet a meglévő NetApp és Dell EMC Isilon NAS rendszerekhez. Azure Blob ot vagy más felhőalapú tárhelyet is hozzáadhat, hogy méretezhetőséget biztosítson anélkül, hogy át kellene dolgoznia az adatelérési folyamatokat az ügyféloldalon. 

### <a name="wan-caching"></a>WAN gyorsítótárazás

Az Azure FXT Edge Filer segítségével támogathatja a gyors fájlhozzáférést a kiemelt felhasználóktól, ha a szükséges adatokat máshol tárolják. Biztosítson hozzáférést, miközben a biztonsági mentést és más adatkezelési rendszereket egy központi adatközpontban tartja. 

### <a name="active-archive-in-azure-blob"></a>Aktív archiválás az Azure Blobban

Bővítse adatközpontját felhőalapú tárhelyre az Azure FXT Edge Filer-rel, mint hozzáférési ponttal. 

## <a name="features"></a>Szolgáltatások 

Két hardvermodell áll rendelkezésre. 

| Modell | Dram | NVMe SSD | Hálózati portok | 
|-------|------|----------|---------------|
| FXT 6600 | 1536 GB | 25,6 TB | 6 x 25Gb/10Gb + 2 x 1Gb |
| FXT 6400 | 768 GB | 12,8 TB | 6 x 25Gb/10Gb + 2 x 1Gb |


## <a name="next-steps"></a>További lépések

* A [specifikációk](fxt-specs.md) vagy a telepítési oktatóanyag elolvasásával folytassa a további megismerést az Azure FXT Edge Filer-ról. [installation tutorial](fxt-install.md)
* Ismerje meg, hogyan vásárolhat Azure FXT Edge Filer-t az [Azure FXT Edge Filer termékoldalán.](https://azure.microsoft.com/services/fxt-edge-filer/)