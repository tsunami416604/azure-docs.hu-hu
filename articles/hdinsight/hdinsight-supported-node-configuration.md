---
title: Az Azure HDInsight által támogatott csomópont-konfigurációk
description: A HDInsight-fürtcsomópontok minimális és ajánlott konfigurációinak megismerése.
keywords: VM-méretek, szektorcsoport-méretek, fürtkonfiguráció
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e482cf9b5367beba00784e69c5bad88142df5225
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70076211"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Mik az Azure HDInsight alapértelmezett és ajánlott csomópont-konfigurációi?

Ez a cikk az Azure HDInsight-fürtök alapértelmezett és ajánlott csomópont-konfigurációit ismerteti.

## <a name="default-and-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Alapértelmezett és ajánlott csomópont-konfiguráció és virtuálisgép-méretek fürtökhöz

A következő táblázatok a HDInsight-fürtök alapértelmezett és ajánlott virtuálisgép-méreteit sorolja fel.  Ezek az információk szükségesek ahhoz, hogy megértsék a virtuálisgép-méreteket, amelyeket a PowerShell vagy az Azure CLI-parancsfájlok létrehozásakor kell használni a HDInsight-fürtök telepítéséhez. 

Ha a fürtben több mint 32 feldolgozó csomópontra van szüksége, válasszon ki legalább 8 maggal és 14 GB RAM-mal rendelkező fő csomópont-méretet. 

A csak az adatlemezeket tartalmazó fürtök a Kafka és a HBase fürtök, amelyeken engedélyezve van a gyorsított írási funkció. A HDInsight ezekben a forgatókönyvekben támogatja a P30 és a S30 lemez méretét.

Az egyes virtuálisgép-típusok leírásával kapcsolatos további részletekért tekintse meg a következő dokumentumokat:

* [Általános célú virtuálisgép-méretek: Dv2 sorozat 1-5](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dv2-series)
* [Memória-optimalizált virtuális gépek méretei: Dv2 sorozat 11-15](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#dv2-series-11-15)
* [Általános célú virtuálisgép-méretek: Av2 sorozat 1-8](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#av2-series)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Minden támogatott régió, kivéve Dél-Brazília és Nyugat-Japán

> [!Note]
> Ahhoz, hogy az SKU-azonosító a PowerShellben és más parancsfájlokban `Standard_` is használható legyen, az alábbi táblázatokban adja hozzá az összes virtuális gép SKU elejéhez. Ilyen lehet például `D12_v2` `Standard_D12_v2`a következő:.

| Fürt típusa | Hadoop | HBase | Interaktív lekérdezés | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| Fej: alapértelmezett VM-méret | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 | D3_v2 |
| Fej: ajánlott virtuálisgép-méretek | D13_v2,<br/>D14_v2,<br/>D5_v2 | D3_v2,<br/>D4_v2,<br/>D12_v2 | D13_v2,<br/>D14_v2 | A4_v2,<br/>A8_v2 | D12_v2,<br/>D13_v2,<br/>D14_v2 | D12_v2,<br/>D13_v2,<br/>D14_v2 | D3_v2,<br/>D4_v2,<br/>D12_v2 |
| Worker: alapértelmezett VM-méret | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 2 S30 lemezzel |
| Worker: ajánlott virtuálisgép-méretek | D5_v2,<br>D12_v2,<br/>D13_v2 | D3_v2,<br/>D4_v2,<br/>D13_v2 | D13_v2,<br/>D14_v2 | D3_v2<br/>D4_v2,<br/>D12_v2 | D12_v2,<br>D13_v2,<br>D14_v2 | D4_v2,<br/>D12_v2,<br>D13_v2,<br>D14_v2 | D3_v2,<br/>D4_v2,<br/>DS3_v2,<br/>DS4_v2 |
| ZooKeeper: alapértelmezett VM-méret |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: ajánlott virtuálisgép-méretek |  | A4_v2, <br/>A8_v2, <br/>A2m_v2 | A4_v2,<br/>A8_v2,<br/>A2m_v2 | A4_v2,<br/>A2_v2,<br/>A8_v2 |  | A2_v2 | A4_v2,<br/> A8_v2,<br/>A2m_v2 |
| ML szolgáltatások: alapértelmezett VM-méret |  |  |  |  |  | D4_v2 |  |
| ML szolgáltatások: ajánlott VM-méret |  |  |  |  |  | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/>D14_v2 |  |

### <a name="brazil-south-and-japan-west-only"></a>Dél-Brazília és Nyugat-Japán

| Fürt típusa | Hadoop | HBase | Interaktív lekérdezés | Storm | Spark | ML-szolgáltatások |
|---|---|---|---|---|---|---|
| Fej: alapértelmezett VM-méret | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Fej: ajánlott virtuálisgép-méretek | D5_v2,<br/> D13_v2,<br/> D14_v2 | D3_v2,<br/> D4_v2,<br/> D12_v2 | D13_v2,<br/> D14_v2 | A4_v2,<br/> A8_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 |
| Worker: alapértelmezett VM-méret | D4 | D4 | D14 | D3 | D13 | D4 |
| Worker: ajánlott virtuálisgép-méretek | D5_v2,<br/> D12_v2,<br/> D13_v2 | D3_v2,<br/> D4_v2,<br/> D13_v2 | D13_v2,<br/> D14_v2 | D3_v2,<br/> D4_v2,<br/> D12_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/> D14_v2 |
| ZooKeeper: alapértelmezett VM-méret |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: ajánlott virtuálisgép-méretek |  | A4_v2,<br/> A8_v2,<br/> A2m_v2 | A4_v2,<br/> A8_v2,<br/> A2m_v2 | A4_v2,<br/> A8_v2 |  | A2_v2 |
| ML szolgáltatások: alapértelmezett VM-méretek |  |  |  |  |  | D4 |
| ML szolgáltatások: ajánlott virtuálisgép-méretek |  |  |  |  |  | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/> D14_v2 |

> [!NOTE]
> - A *Nimbus* a Storm-fürt típusának nevezzük.
> - A Worker neve a Storm-fürt típusának felügyelője.
> - A feldolgozó neve a HBase-fürt típusának régiója.

## <a name="next-steps"></a>További lépések

* [Mik azok az Apache Hadoop-összetevők és verziók a HDInsight?](hdinsight-component-versioning.md)
