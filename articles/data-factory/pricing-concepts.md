---
title: Az Azure Data Factory díjszabásának ismertetése példákon keresztül
description: Ez a cikk részletes példákkal ismerteti és mutatja be az Azure Data Factory díjszabási modelljét
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: ee5acc97e4b05a0e93f4ceee8c04b400da211b49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76769496"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>A Data Factory árképzési díjszabásának ismertetése példákon keresztül

Ez a cikk ismerteti és bemutatja az Azure Data Factory díjszabási modell részletes példákkal.

> [!NOTE]
> Az alábbi példákban használt árak hipotetikusak, és nem jelentik a tényleges árképzést.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Adatok másolása az AWS S3-ról az Azure Blob-tárhelyre óránként

Ebben a forgatókönyvben az AWS S3-ból az Azure Blob storage-ba óránként i.

A forgatókönyv megvalósításához létre kell hoznia egy folyamatot a következő elemekkel:

1. Az AWS S3-ból másolandó adatok bemeneti adatkészletével rendelkező másolási tevékenység.

2. Az Azure Storage-adatok kimeneti adatkészlete.

3. Egy ütemezési eseményindító a folyamat óránkénti végrehajtására.

   ![1. forgatókönyv](media/pricing-concepts/scenario1.png)

| **Műveletek** | **Típusok és egységek** |
| --- | --- |
| Csatolt szolgáltatás létrehozása | 2 Olvasási/írási entitás  |
| Adatkészletek létrehozása | 4 Olvasási/írási entitások (2 adatkészlet létrehozásához, 2 csatolt szolgáltatáshivatkozásokhoz) |
| Folyamat létrehozása | 3 Olvasási/írási entitások (1 a folyamat létrehozásához, 2 az adatkészlet-hivatkozásokhoz) |
| Folyamat bekéselése | 1 Olvasási/írási entitás |
| Folyamat futtatása | 2 Tevékenységfuttatás (1 az eseményindító futtatáshoz, 1 tevékenységfuttatáshoz) |
| Adatok másolása Feltételezés: végrehajtási idő = 10 min | 10 \* 4 Azure-integrációs runtime (alapértelmezett DIU-beállítás = 4) Az adatintegrációs egységekről és a másolási teljesítmény optimalizálásáról ebben [a cikkben](copy-activity-performance.md) olvashat bővebben. |
| Figyelő csővezeték feltételezése: Csak 1 futtatás történt | 2 A figyelőfuttatási rekordok újrakísérelése (1 a folyamat futtatásához, 1 a tevékenység futtatásához) |

**Teljes forgatókönyv szerinti árképzés: $0.16811**

- Data Factory műveletek = **$0.0001**
  - Olvasás/írás =\*10 00001 = 0,0001 $[1 R/W = 0,50 0000 $ = 0,00001]
  - Figyelés\*= 2 000005 = $0.00001 [1 Figyelés = 0,25 USD/50000 = 0,000005]
- Csővezeték-vezénylési &amp; végrehajtás = **$0.168**
  - Tevékenységfuttatások =\*001 2 = 0,002 [1 futtatás = $1/1000 = 0,001]
  - Adatmozgatási tevékenységek = 0,166 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 $0.25/óra az Azure-integrációs futásidőben)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Adatok másolása és átalakítás az Azure Databricks használatával óránként

Ebben a forgatókönyvben szeretné másolni az adatokat Az AWS S3 az Azure Blob storage és az adatok átalakítása az Azure Databricks óránkénti ütemezés szerint.

A forgatókönyv megvalósításához létre kell hoznia egy folyamatot a következő elemekkel:

1. Egy másolási tevékenység egy bemeneti adatkészletaz adatokat kell másolni az AWS S3, és egy kimeneti adatkészlet az adatok az Azure storage-ban.
2. Egy Azure Databricks-tevékenység az adatátalakításhoz.
3. Óránként egy ütemezési eseményindító hajthatja végre a folyamatot.

![2. forgatókönyv](media/pricing-concepts/scenario2.png)

| **Műveletek** | **Típusok és egységek** |
| --- | --- |
| Csatolt szolgáltatás létrehozása | 3 Olvasási/írási entitás  |
| Adatkészletek létrehozása | 4 Olvasási/írási entitások (2 adatkészlet létrehozásához, 2 csatolt szolgáltatáshivatkozásokhoz) |
| Folyamat létrehozása | 3 Olvasási/írási entitások (1 a folyamat létrehozásához, 2 az adatkészlet-hivatkozásokhoz) |
| Folyamat bekéselése | 1 Olvasási/írási entitás |
| Folyamat futtatása | 3 Tevékenységfuttatás (1 az eseményindító futtatáshoz, 2 tevékenységfuttatáshoz) |
| Adatok másolása Feltételezés: végrehajtási idő = 10 min | 10 \* 4 Azure-integrációs runtime (alapértelmezett DIU-beállítás = 4) Az adatintegrációs egységekről és a másolási teljesítmény optimalizálásáról ebben [a cikkben](copy-activity-performance.md) olvashat bővebben. |
| Figyelő csővezeték feltételezése: Csak 1 futtatás történt | 3 A figyelőfuttatási rekordok újrakísérelése (1 a folyamat futtatásához, 2 a tevékenység futtatásához) |
| Adatokbrick tevékenység végrehajtása Feltételezés: végrehajtási idő = 10 min | 10 min külső csővezeték-tevékenység végrehajtása |

**Teljes forgatókönyv szerinti árképzés: $0.16916**

- Adatfeldolgozó műveletek = **$0.00012**
  - Olvasás/írás =\*11 00001 = 0,00011 $[1 R/W = 0,50 USD/50000 = 0,00001]
  - Figyelés\*= 3 000005 = $0.00001 [1 Figyelés = 0,25 USD/50000 = 0,000005]
- Csővezeték-vezénylési &amp; végrehajtás = **$0.16904**
  - Tevékenységfuttatások =\*001 3 = 0,003 [1 futtatás = $1/1000 = 0,001]
  - Adatmozgatási tevékenységek = 0,166 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 $0.25/óra az Azure-integrációs futásidőben)
  - Külső csővezeték-tevékenység = $0.000041 (10 perces végrehajtási időre arányosan. $0.00025/óra az Azure-integrációs futásidőben)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Adatok másolása és átalakítás dinamikus paraméterekkel óránként

Ebben a forgatókönyvben szeretné másolni az adatokat AWS S3 az Azure Blob storage és az Azure Databricks (dinamikus paraméterekkel a parancsfájlban) óránkénti ütemezés szerint.

A forgatókönyv megvalósításához létre kell hoznia egy folyamatot a következő elemekkel:

1. Egy másolási tevékenység egy bemeneti adatkészletaz adatokat kell másolni az AWS S3, egy kimeneti adatkészlet az adatok az Azure storage-ban.
2. Egy keresési tevékenység a paraméterek dinamikus átadása az átalakítási parancsfájl.
3. Egy Azure Databricks-tevékenység az adatátalakításhoz.
4. Óránként egy ütemezési eseményindító hajthatja végre a folyamatot.

![3. forgatókönyv](media/pricing-concepts/scenario3.png)

| **Műveletek** | **Típusok és egységek** |
| --- | --- |
| Csatolt szolgáltatás létrehozása | 3 Olvasási/írási entitás  |
| Adatkészletek létrehozása | 4 Olvasási/írási entitások (2 adatkészlet létrehozásához, 2 csatolt szolgáltatáshivatkozásokhoz) |
| Folyamat létrehozása | 3 Olvasási/írási entitások (1 a folyamat létrehozásához, 2 az adatkészlet-hivatkozásokhoz) |
| Folyamat bekéselése | 1 Olvasási/írási entitás |
| Folyamat futtatása | 4 Tevékenységfuttatás (1 az eseményindító futtatáshoz, 3 tevékenységfuttatáshoz) |
| Adatok másolása Feltételezés: végrehajtási idő = 10 min | 10 \* 4 Azure-integrációs runtime (alapértelmezett DIU-beállítás = 4) Az adatintegrációs egységekről és a másolási teljesítmény optimalizálásáról ebben [a cikkben](copy-activity-performance.md) olvashat bővebben. |
| Figyelő csővezeték feltételezése: Csak 1 futtatás történt | 4 A figyelőfuttatási rekordok újrakísérelése (1 a folyamat futtatásához, 3 a tevékenység futtatásához) |
| Lookup activity végrehajtási tevékenység Feltételezés: végrehajtási idő = 1 min | 1 min csővezeték-tevékenység végrehajtása |
| Adatokbrick tevékenység végrehajtása Feltételezés: végrehajtási idő = 10 min | 10 min külső csővezeték-tevékenység végrehajtása |

**Teljes forgatókönyv szerinti árképzés: $0.17020**

- Adatfeldolgozó műveletek = **$0.00013**
  - Olvasás/írás =\*11 00001 = 0,00011 $[1 R/W = 0,50 USD/50000 = 0,00001]
  - Figyelés\*= 4 000005 = $0.00002 [1 Figyelés = 0,25 USD/50000 = 0,000005]
- Csővezeték-vezénylési &amp; végrehajtás = **$0.17007**
  - Tevékenységfuttatások =\*001 4 = 0,004 [1 futtatás = $1/1000 = 0,001]
  - Adatmozgatási tevékenységek = 0,166 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 $0.25/óra az Azure-integrációs futásidőben)
  - Csővezeték-tevékenység = $0.00003 (1 perces végrehajtási időre arányosan. $0.002/óra az Azure-integrációs futásidőben)
  - Külső csővezeték-tevékenység = $0.000041 (10 perces végrehajtási időre arányosan. $0.00025/óra az Azure-integrációs futásidőben)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday"></a>Adatfolyam-hibakeresés használata normál munkanaphoz

Adatmérnökként Ön felelős a feltérképezési adatfolyamatok tervezéséért, létrehozásáért és teszteléséért minden nap. Reggel bejelentkezik az ADF felhasználói felületére, és engedélyezi az adatfolyamok hibakeresési módját. A hibakeresési munkamenetek alapértelmezett TTL-je 60 perc. A nap folyamán 8 órán át dolgozik, így a hibakeresési munkamenet soha nem jár le. Ezért a napi díj a következő lesz:

**8 (óra) x 8 (számításra optimalizált magok) x $0.193 = $12.35**

## <a name="transform-data-in-blob-store-with-mapping-data-flows"></a>Adatok átalakítása a blobtárolóban leképezési adatfolyamokkal

Ebben a forgatókönyvben szeretné átalakítani az adatokat blob Store vizuálisan ADF leképezési adatok óránkénti ütemezés szerint.

A forgatókönyv megvalósításához létre kell hoznia egy folyamatot a következő elemekkel:

1. Adatfolyam-tevékenység az átalakítási logikával.

2. Az Azure Storage-adatok bemeneti adatkészlete.

3. Az Azure Storage-adatok kimeneti adatkészlete.

4. Egy ütemezési eseményindító a folyamat óránkénti végrehajtására.

| **Műveletek** | **Típusok és egységek** |
| --- | --- |
| Csatolt szolgáltatás létrehozása | 2 Olvasási/írási entitás  |
| Adatkészletek létrehozása | 4 Olvasási/írási entitások (2 adatkészlet létrehozásához, 2 csatolt szolgáltatáshivatkozásokhoz) |
| Folyamat létrehozása | 3 Olvasási/írási entitások (1 a folyamat létrehozásához, 2 az adatkészlet-hivatkozásokhoz) |
| Folyamat bekéselése | 1 Olvasási/írási entitás |
| Folyamat futtatása | 2 Tevékenységfuttatás (1 az eseményindító futtatáshoz, 1 tevékenységfuttatáshoz) |
| Adatáramlási feltételezések: végrehajtási idő = 10 perc + 10 perc TTL | 10 \* 16 mag általános számítás 10 TTL-lel |
| Figyelő csővezeték feltételezése: Csak 1 futtatás történt | 2 A figyelőfuttatási rekordok újrakísérelése (1 a folyamat futtatásához, 1 a tevékenység futtatásához) |

**Teljes forgatókönyv szerinti árképzés: $1.4631**

- Data Factory műveletek = **$0.0001**
  - Olvasás/írás =\*10 00001 = 0,0001 $[1 R/W = 0,50 0000 $ = 0,00001]
  - Figyelés\*= 2 000005 = $0.00001 [1 Figyelés = 0,25 USD/50000 = 0,000005]
- Csővezeték-vezénylési &amp; végrehajtás = **$1.463**
  - Tevékenységfuttatások =\*001 2 = 0,002 [1 futtatás = $1/1000 = 0,001]
  - Adatfolyam-tevékenységek = $1.461 20 percig arányosan (10 perc végrehajtási idő + 10 perc TTL). $0.274/óra az Azure-integrációs futásidőben 16 maggal rendelkező általános számítással

## <a name="next-steps"></a>További lépések

Most, hogy megértette az Azure Data Factory díjszabását, első lépésekre számíthat!

- [Adat-előállító létrehozása az Azure Data Factory felhasználói felületén](quickstart-create-data-factory-portal.md)

- [Az Azure Data Factory bemutatása](introduction.md)

- [Vizuális szerzői jog az Azure Data Factoryban](author-visually.md)
