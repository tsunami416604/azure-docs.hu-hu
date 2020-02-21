---
title: Azure-beli virtuális gépek méretei – tárolás | Microsoft Docs
description: Felsorolja az Azure-beli virtuális gépekhez elérhető különböző tárterület-optimalizált méreteket. A vCPU, az adatlemezek és a hálózati adapterek számával, valamint a tárolási teljesítményével és a hálózat sávszélességével kapcsolatos információkat sorolja fel ebben a sorozatban.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 17cceb04d3d6e559cd6f2b49674f6d408a0650a4
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492993"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>Tárterületre optimalizált virtuálisgép-méretek

A tárolásra optimalizált virtuálisgép-méretek nagy méretű adatátviteli sebességet és IO-t biztosítanak, és ideálisak a Big adatok, az SQL, a NoSQL adatbázisok, az adattárházak és a nagy tranzakciós adatbázisok számára.  Ilyenek például a Cassandra, a MongoDB, a Cloudera és a Redis. Ez a cikk a vCPU, az adatlemezek és a hálózati adapterek számáról, valamint a helyi tárterület átviteli sebességéről és a hálózati sávszélességről nyújt információt az egyes optimalizált méretekhez.

A [Lsv2 sorozat](lsv2-series.md) nagy átviteli sebességű, kis késleltetésű, közvetlenül leképezett helyi NVMe-tárolót biztosít az [AMD EPYC<sup>TM</sup> 7551 processzoron](https://www.amd.com/en/products/epyc-7000-series) , amely a 2.55 GHz-es és a 3,0 GHz-es maximális lendülettel rendelkezik. Az Lsv2 sorozatú virtuális gépek elérhetőek 8 és 80 vCPU-s méretekben egy párhuzamos többszálas konfigurációban.  A vCPU-nkénti memória 8 GiB, és 8 vCPU-nkét egy 1,92 TB-os NVMe SSD M.2 eszköz, és az L80s v2-nél akár 19,2 TB (10x1,92 TB) is elérhető.

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.

Ismerje meg, hogyan optimalizálhatja a teljesítményt a Windows vagy [Linux](linux/storage-performance.md) [rendszerhez](windows/storage-performance.md) készült Lsv2-sorozatú virtuális gépeken.