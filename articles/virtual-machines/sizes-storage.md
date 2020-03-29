---
title: Az Azure virtuális gépek méretei - Storage | Microsoft dokumentumok
description: Az Azure-ban a virtuális gépekhez elérhető különböző tárhelyre optimalizált méretek. A vCPU-k, adatlemezek és hálózati adapterek számával, valamint a tárolóátviteli és hálózati sávszélességgel kapcsolatos információkat sorolja fel a sorozat méreteihez.
services: virtual-machines
documentationcenter: ''
author: sasha-melamed
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
ms.openlocfilehash: 5a611dc288fc18a14f6000689f9f9b49d4b3feb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913336"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>Tárhelyre optimalizált virtuális gépméretek

A tárhelyre optimalizált virtuális gépméretek nagy lemezátviteli és I/O-t kínálnak, és ideálisak big data, SQL, NoSQL adatbázisok, adattárház és nagy méretű tranzakciós adatbázisok számára.  Ilyen például cassandra, MongoDB, Cloudera és Redis. Ez a cikk a vCPU-k, adatlemezek és hálózati adapterek, valamint a helyi tárolási átviteli és hálózati sávszélesség számáról tartalmaz információt az egyes optimalizált mérethez.

Az [Lsv2 sorozat](lsv2-series.md) nagy átviteli sebességgel, alacsony késleltetéssel, közvetlenül feltérképezett helyi NVMe tárolóval rendelkezik, amely az [AMD EPYC<sup>TM</sup> 7551 processzoron](https://www.amd.com/en/products/epyc-7000-series) fut, 2,55 GHz-es magemeléssel és 3,0 GHz-es maximális növekedéssel. Az Lsv2 sorozatú virtuális gépek 8 és 80 vCPU között kaphatók egyidejű többszálas konfigurációban.  Van 8 GiB memória vCPU-nként, és egy 1.92TB NVMe SSD M.2 eszköz 8 vCPU-nként, akár 19.2TB (10x1.92TB) elérhető az L80s v2.

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések

További információ arról, hogy [az Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek a számítási teljesítmény összehasonlításában az Azure-sKU-k között.

Ismerje meg, hogyan optimalizálhatja a teljesítményt az Lsv2 sorozatú [windowsos](windows/storage-performance.md) vagy [Linuxos](linux/storage-performance.md)virtuális gépeken.
