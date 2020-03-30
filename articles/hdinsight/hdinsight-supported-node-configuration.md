---
title: Az Azure HDInsight támogatja a csomópontkonfigurációkat
description: Ismerje meg a HDInsight fürtcsomópontok minimális és ajánlott konfigurációit.
keywords: vm-méretek, fürtméretek, fürtkonfiguráció
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e75146266568001d8fee7be26898ac8bdfffb7fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484786"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Melyek az Azure HDInsight alapértelmezett és ajánlott csomópont-konfigurációi?

Ez a cikk ismerteti az Azure HDInsight-fürtök alapértelmezett és ajánlott csomópont-konfigurációk.

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Alapértelmezett és minimálisajánlott csomópontkonfiguráció és virtuálisgép-méretek fürtökhöz

Az alábbi táblázatok a HDInsight-fürtök alapértelmezett és ajánlott virtuálisgép-méreteit sorolják fel.  Ez az információ a HdInsight-fürtök üzembe helyezéséhez szükséges virtuális gépméretek megértéséhez szükséges.

Ha egy fürtben 32-nél több munkavégző csomópontra van szüksége, válasszon ki egy legalább 8 maggal és 14 GB RAM-mal rendelkező főcsomópontméretet. 

Az adatlemezekkel rendelkező fürttípusok csak a Kafka és a HBase-fürtök, amelyeken engedélyezve van a Gyorsított írási szolgáltatás. A HDInsight ezekben az esetekben támogatja a P30 és S30 lemezméreteket.

Az ebben a dokumentumban használt összes minimálisan ajánlott virtuálisgép-típus specifikációit az alábbi táblázat foglalja össze.

| Méret              | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | Max hálózati adapterek / várható hálózati sávszélesség (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16 / 16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32 / 32x500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64 / 64x500           | 8 / 12000                                    |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2x500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4x500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8x500               | 4 / 1000                     |

Az egyes virtuálisgép-típusok specifikációiról a következő dokumentumokban talál további információt:

* [Általános célú virtuális gépméretek: Dv2 sorozat 1-5](../virtual-machines/dv2-dsv2-series.md)
* [Memória optimalizált virtuális gép méretei: Dv2 sorozat 11-15](../virtual-machines/dv2-dsv2-series-memory.md)
* [Általános célú virtuális gépméretek: Av2 sorozat 1-8](../virtual-machines/av2-series.md)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Brazília déli és nyugat-japán kivételével valamennyi támogatott régió

> [!Note]
> A termékváltozat-azonosító powershellben és más parancsfájlokban `Standard_` való használathoz való lekérni, adja hozzá az összes virtuálisgép-termékváltozat elejéhez az alábbi táblázatokban. Például, `D12_v2` lenne `Standard_D12_v2`.

| Fürt típusa | Hadoop | HBase | Interaktív lekérdezés | Storm | Spark | ML-kiszolgáló | Kafka |
|---|---|---|---|---|---|---|---|
| Fej: alapértelmezett virtuális gép méret | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2* | D12_v2 | D3_v2 |
| Fej: minimálisajánlott virtuálisgép-méretek | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2* | D12_v2 | D3_v2 |
| Dolgozó: alapértelmezett virtuális gép mérete | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 2 S30 lemezbrókerenként |
| Dolgozó: minimálisan ajánlott virtuálisgép-méretek | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| ZooKeeper: alapértelmezett virtuális gép méret |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: minimális ajánlott virtuálisgép-méretek |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| ML-szolgáltatások: alapértelmezett virtuális gépméret |  |  |  |  |  | D4_v2 |  |
| ML-szolgáltatások: minimális ajánlott virtuális gép méret |  |  |  |  |  | D4_v2 |  |

\*= Virtuálisgép-méretek a Spark Enterprise Security Package (ESP) fürtökhöz

### <a name="brazil-south-and-japan-west-only"></a>Csak Dél-Brazília és Nyugat-Japán

| Fürt típusa | Hadoop | HBase | Interaktív lekérdezés | Storm | Spark | ML-szolgáltatások |
|---|---|---|---|---|---|---|
| Fej: alapértelmezett virtuális gép méret | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Fej: minimálisajánlott virtuálisgép-méretek | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
| Dolgozó: alapértelmezett virtuális gép mérete | D4 | D4 | D14 | D3 | D13 | D4 |
| Dolgozó: minimálisan ajánlott virtuálisgép-méretek | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 |
| ZooKeeper: alapértelmezett virtuális gép méret |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: minimális ajánlott virtuálisgép-méretek |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ML-szolgáltatások: alapértelmezett virtuális gépméretek |  |  |  |  |  | D4 |
| ML-szolgáltatások: minimális ajánlott virtuálisgép-méretek |  |  |  |  |  | D4_v2 |

> [!NOTE]
> - A Storm fürttípus *Nimbus* néven ismert.
> - A dolgozó a Storm fürttípus *felügyelőjeként* ismert.
> - A dolgozó a HBase fürttípus *régiója.*

## <a name="next-steps"></a>További lépések

* [Milyen apache Hadoop-összetevők és -verziók érhetők el a HDInsight segítségével?](hdinsight-component-versioning.md)
