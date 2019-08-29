---
title: Példák a Azure Data Factory díjszabásának megismerésére | Microsoft Docs
description: Ez a cikk ismerteti és bemutatja a Azure Data Factory díjszabási modellt részletes példákkal
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/25/2018
ms.openlocfilehash: f08dea90e7700082b6eeb708b576451060f81255
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70140946"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>A Data Factory díjszabásának ismertetése példákkal

Ez a cikk ismerteti és bemutatja a Azure Data Factory díjszabási modellt részletes példákkal.

> [!NOTE]
> Az alábbi példákban használt árak feltételezettek, és nem jelentenek tényleges díjszabást.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Adatok másolása az AWS S3-ból az Azure Blob Storage-ba óránként

Ebben az esetben az AWS S3-ról az Azure Blob Storage-ba történő Adatmásolást óránkénti időpontra szeretné másolni.

A forgatókönyv végrehajtásához létre kell hoznia egy folyamatot a következő elemekkel:

1. Egy másolási tevékenység egy bemeneti adatkészlettel az AWS S3-ból másolandó adatokhoz.

2. Az Azure Storage-ban tárolt adat kimeneti adatkészlete.

3. Egy ütemezett trigger a folyamat óránkénti végrehajtásához.

   ![Scenario1](media/pricing-concepts/scenario1.png)

| **Műveletek** | **Típusok és egységek** |
| --- | --- |
| Társított szolgáltatás létrehozása | 2 olvasási/írási entitás  |
| Adatkészletek létrehozása | 4 írási/olvasási entitások (2 az adatkészlet létrehozásához, 2 a társított szolgáltatási referenciák esetében) |
| Folyamat létrehozása | 3 olvasási/írási entitás (1 a folyamat létrehozásához, 2 az adatkészlet-hivatkozásokhoz) |
| Folyamat beolvasása | 1 olvasási/írási entitás |
| Folyamat futtatása | 2 tevékenység fut (1 a trigger futtatásához, 1 a tevékenység futtatásához) |
| Adatok másolása feltételezés: végrehajtási idő = 10 perc | 10 \* 4 Azure Integration Runtime (alapértelmezett DIU-beállítás = 4) az adatintegrációs egységekkel és a másolási teljesítmény optimalizálásával kapcsolatos további információkért tekintse meg [ezt a cikket.](copy-activity-performance.md) |
| Figyelő folyamat feltételezése: Csak 1 Futtatás történt | 2 figyelési futtatási rekordok újrapróbálva (1 a folyamat futtatásához, 1 a tevékenység futtatásához) |

**Forgatókönyvek teljes díjszabása: $0,16811**

- Data Factory műveletek = **$0,0001**
  - Olvasás/írás = 10\*00001 = $0,0001 [1 R/W = $0,50/50000 = 0,00001]
  - Figyelés = 2\*000005 = $0,00001 [1 figyelés = $0,25/50000 = 0,000005]
- Folyamat-előkészítési végrehajtás&amp; = $0,168
  - Tevékenység-futtatások\*= 001 2 = 0,002 [1 Futtatás = $1/1000 = 0,001]
  - Adatáthelyezési tevékenységek = $0,166 (a végrehajtási idő 10 perce arányban. 0,25 USD/óra Azure Integration Runtime)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Az Adatmásolás és az átalakítás Azure Databricks óránként

Ebben az esetben az AWS S3-ból az Azure Blob Storage-ba másolhatja az adatok adatait, és a Azure Databricks óránként át kell alakítani.

A forgatókönyv végrehajtásához létre kell hoznia egy folyamatot a következő elemekkel:

1. Egy másolási tevékenység bemeneti adatkészlettel az AWS S3-ból másolt adatokhoz, valamint az Azure Storage-beli adatok kimeneti adatkészlete.
2. Egy Azure Databricks tevékenység az adatátalakításhoz.
3. Egy ütemezett trigger a folyamat óránkénti végrehajtásához.

![Scenario2](media/pricing-concepts/scenario2.png)

| **Műveletek** | **Típusok és egységek** |
| --- | --- |
| Társított szolgáltatás létrehozása | 3 olvasási/írási entitás  |
| Adatkészletek létrehozása | 4 írási/olvasási entitások (2 az adatkészlet létrehozásához, 2 a társított szolgáltatási referenciák esetében) |
| Folyamat létrehozása | 3 olvasási/írási entitás (1 a folyamat létrehozásához, 2 az adatkészlet-hivatkozásokhoz) |
| Folyamat beolvasása | 1 olvasási/írási entitás |
| Folyamat futtatása | 3 tevékenység fut (1 a trigger futtatásához, 2 a tevékenység-futtatásokhoz) |
| Adatok másolása feltételezés: végrehajtási idő = 10 perc | 10 \* 4 Azure Integration Runtime (alapértelmezett DIU-beállítás = 4) az adatintegrációs egységekkel és a másolási teljesítmény optimalizálásával kapcsolatos további információkért tekintse meg [ezt a cikket.](copy-activity-performance.md) |
| Figyelő folyamat feltételezése: Csak 1 Futtatás történt | 3 a folyamatban lévő rekordok figyelése újrapróbálkozott (1 a folyamat futtatásához, 2 a tevékenység futtatásához) |
| Databricks tevékenység végrehajtása – feltételezés: végrehajtási idő = 10 perc | 10 perc külső folyamat tevékenységének végrehajtása |

**Forgatókönyvek teljes díjszabása: $0,16916**

- Data Factory műveletek = **$0,00012**
  - Írás/írás = 11\*00001 = $0,00011 [1 R/W = $0,50/50000 = 0,00001]
  - Monitoring = 3\*000005 = $0,00001 [1 figyelés = $0,25/50000 = 0,000005]
- Folyamat-előkészítési végrehajtás&amp; = $0,16904
  - Tevékenység-futtatások\*= 001 3 = 0,003 [1 Futtatás = $1/1000 = 0,001]
  - Adatáthelyezési tevékenységek = $0,166 (a végrehajtási idő 10 perce arányban. 0,25 USD/óra Azure Integration Runtime)
  - Külső folyamat tevékenysége = $0,000041 (a végrehajtási idő 10 perce arányban. $0.00025/óra Azure Integration Runtime)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Adatok másolása és átalakítása dinamikus paraméterekkel óránként

Ebben a forgatókönyvben az AWS S3-ról az Azure Blob Storage-ba történő adatmásolásra és a Azure Databricks (a szkript dinamikus paramétereinek használatával) történő átalakítására van szükség óránkénti ütemterv szerint.

A forgatókönyv végrehajtásához létre kell hoznia egy folyamatot a következő elemekkel:

1. Egy másolási tevékenység egy bemeneti adatkészlettel az AWS S3-ból másolt adatokhoz, az Azure Storage-beli adatok kimeneti adatkészlete.
2. Egy keresési tevékenység, amely dinamikusan továbbítja a paramétereket az átalakítási parancsfájlba.
3. Egy Azure Databricks tevékenység az adatátalakításhoz.
4. Egy ütemezett trigger a folyamat óránkénti végrehajtásához.

![Scenario3](media/pricing-concepts/scenario3.png)

| **Műveletek** | **Típusok és egységek** |
| --- | --- |
| Társított szolgáltatás létrehozása | 3 olvasási/írási entitás  |
| Adatkészletek létrehozása | 4 írási/olvasási entitások (2 az adatkészlet létrehozásához, 2 a társított szolgáltatási referenciák esetében) |
| Folyamat létrehozása | 3 olvasási/írási entitás (1 a folyamat létrehozásához, 2 az adatkészlet-hivatkozásokhoz) |
| Folyamat beolvasása | 1 olvasási/írási entitás |
| Folyamat futtatása | 4 tevékenység fut (1 a trigger futtatásához, 3 a tevékenység futtatásához) |
| Adatok másolása feltételezés: végrehajtási idő = 10 perc | 10 \* 4 Azure Integration Runtime (alapértelmezett DIU-beállítás = 4) az adatintegrációs egységekkel és a másolási teljesítmény optimalizálásával kapcsolatos további információkért tekintse meg [ezt a cikket.](copy-activity-performance.md) |
| Figyelő folyamat feltételezése: Csak 1 Futtatás történt | 4 figyelési futtatási rekordok újrapróbálva (1 a folyamat futtatásához, 3 a tevékenység futtatásához) |
| Keresési tevékenység végrehajtási feltételezése: végrehajtási idő = 1 perc | 1 perc folyamat tevékenységének végrehajtása |
| Databricks tevékenység végrehajtása – feltételezés: végrehajtási idő = 10 perc | 10 perc külső folyamat tevékenységének végrehajtása |

**Forgatókönyvek teljes díjszabása: $0,17020**

- Data Factory műveletek = **$0,00013**
  - Írás/írás = 11\*00001 = $0,00011 [1 R/W = $0,50/50000 = 0,00001]
  - Monitoring = 4\*000005 = $0,00002 [1 figyelés = $0,25/50000 = 0,000005]
- Folyamat-előkészítési végrehajtás&amp; = $0,17007
  - Tevékenység-futtatások\*= 001 4 = 0,004 [1 Futtatás = $1/1000 = 0,001]
  - Adatáthelyezési tevékenységek = $0,166 (a végrehajtási idő 10 perce arányban. 0,25 USD/óra Azure Integration Runtime)
  - Folyamat aktivitása = $0,00003 (a végrehajtási idő 1 perces elszámolása. $0.002/óra Azure Integration Runtime)
  - Külső folyamat tevékenysége = $0,000041 (a végrehajtási idő 10 perce arányban. $0.00025/óra Azure Integration Runtime)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday-preview-pricing"></a>A leképezési adatfolyamok hibakeresése normál munkanapokon (előzetes verzió díjszabása)

Adatmérnökként minden nap feldolgozza a leképezési adatfolyamatok tervezését, összeállítását és tesztelését. Reggel be kell jelentkeznie az ADF felhasználói felületére, és engedélyeznie kell a hibakeresési módot az adatforgalomhoz. A hibakeresési munkamenetek alapértelmezett ÉLETTARTAMa 60 perc. A nap folyamán 10 óráig dolgozhat, így a hibakeresési munkamenet soha nem jár le. Ezért a napi díj a következő lesz:

**10 (óra) x 8 (magok) x $0,112 = $8,96**

## <a name="transform-data-in-blob-store-with-mapping-data-flows-preview-pricing"></a>A blob-tárolóban lévő adatátalakítások leképezési adatforgalmával (előzetes verzió díjszabása)

Ebben a forgatókönyvben a blob-tárolóban lévő adatátalakítást az ADF-leképezési adatfolyamatok óránkénti időpontjában szeretné átalakítani.

A forgatókönyv végrehajtásához létre kell hoznia egy folyamatot a következő elemekkel:

1. Egy adatfolyam-tevékenység az átalakítási logikával.

2. Az Azure Storage-ban tárolt adatok bemeneti adatkészlete.

3. Az Azure Storage-ban tárolt adat kimeneti adatkészlete.

4. Egy ütemezett trigger a folyamat óránkénti végrehajtásához.

| **Műveletek** | **Típusok és egységek** |
| --- | --- |
| Társított szolgáltatás létrehozása | 2 olvasási/írási entitás  |
| Adatkészletek létrehozása | 4 írási/olvasási entitások (2 az adatkészlet létrehozásához, 2 a társított szolgáltatási referenciák esetében) |
| Folyamat létrehozása | 3 olvasási/írási entitás (1 a folyamat létrehozásához, 2 az adatkészlet-hivatkozásokhoz) |
| Folyamat beolvasása | 1 olvasási/írási entitás |
| Folyamat futtatása | 2 tevékenység fut (1 a trigger futtatásához, 1 a tevékenység futtatásához) |
| Adatáramlási feltételezések: végrehajtási idő = 10 perc + 10 perc TTL | 10 \* 8 mag az általános számítási feladatokhoz, amelynek élettartama 10 |
| Figyelő folyamat feltételezése: Csak 1 Futtatás történt | 2 figyelési futtatási rekordok újrapróbálva (1 a folyamat futtatásához, 1 a tevékenység futtatásához) |

**Forgatókönyvek teljes díjszabása: $0,3011**

- Data Factory műveletek = **$0,0001**
  - Olvasás/írás = 10\*00001 = $0,0001 [1 R/W = $0,50/50000 = 0,00001]
  - Figyelés = 2\*000005 = $0,00001 [1 figyelés = $0,25/50000 = 0,000005]
- Folyamat-előkészítési végrehajtás&amp; = $0,301
  - Tevékenység-futtatások\*= 001 2 = 0,002 [1 Futtatás = $1/1000 = 0,001]
  - Adatfolyam-tevékenységek = $0,299 arány 20 percre (10 perc végrehajtási idő + 10 perc TTL). $0.112/óra Azure Integration Runtime 8 maggal általános számítási feladatokkal

## <a name="next-steps"></a>További lépések

Most, hogy megértette Azure Data Factory díjszabását, megkezdheti!

- [Adatelőállító létrehozása a Azure Data Factory felhasználói felületének használatával](quickstart-create-data-factory-portal.md)

- [Az Azure Data Factory bemutatása](introduction.md)

- [Visual authoring in Azure Data Factory](author-visually.md)
