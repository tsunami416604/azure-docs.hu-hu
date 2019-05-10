---
title: Azure Data Factory díjszabása a példákon keresztül ismertetése |} A Microsoft Docs
description: Ez a cikk azt ismerteti, és bemutatja az Azure Data Factory díjszabási modell részletes példák
documentationcenter: ''
author: shlo
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: shlo
ms.openlocfilehash: a825982532047f6e311c5508394df243310f02ab
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233927"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>A Data Factory díjszabása a példákon keresztül ismertetése

Ez a cikk ismerteti, és bemutatja az Azure Data Factory díjszabási modell részletes példák.

> [!NOTE]
> Az alábbi példákban használt díjak elméleti és jelenti a tényleges díjszabás nem tartozhat.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Adatok másolása az AWS S3-ból az Azure Blob storage-óránként

Ebben a forgatókönyvben a kívánt adatok másolása az AWS S3-ból az Azure Blob storage-óránkénti ütemezés szerint.

Ehhez a forgatókönyvhöz, létrehoz egy folyamatot a következő elemeket kell:

1. A másolási tevékenység az AWS S3 átmásolni az adatok bemeneti adatkészlet.

2. Az adatok Azure Storage a kimeneti adatkészletet.

3. Ütemezési eseményindító óránként a folyamat végrehajtásához.

   ![Scenario1](media/pricing-concepts/scenario1.png)

| **Műveletek** | **Típusok és egységek** |
| --- | --- |
| Társított szolgáltatás létrehozása | 2 olvasási/írási entitás  |
| Adatkészletek létrehozása | 4 olvasási/írási entitások (2 adatkészlet létrehozásához, a társított szolgáltatás hivatkozik 2) |
| Folyamat létrehozása | 3/olvasási entitások (1. a folyamat létrehozása, az adatkészlet hivatkozások 2) |
| Folyamatok beolvasása | 1 olvasási/írási entitás |
| Folyamat futtatása | 2 tevékenység-végrehajtás (1. az eseményindító-futtatás, 1. a tevékenység-végrehajtás) |
| Másolás adatok feltételezzük: végrehajtási idő = 10 perc | 10 \* 4 azure integrációs modul (alapértelmezett beállítás DIU = 4) integrációs adategységek és teljesítményéről. másolási optimalizálása. További információkért lásd: [Ez a cikk](copy-activity-performance.md) |
| A figyelő folyamat feltételezzük: Csak 1 történt futtatása | 2 futtatása figyelés rekordok újrapróbált (1. a folyamat futtatásához, 1. a tevékenység-végrehajtásonként) |

**Teljes forgatókönyv díjszabás: $0.16811**

- Data Factory műveletek = **$0,0001**
  - Olvasási/írási = 10\*00001 $0,0001 = [1 R/W $ 0,50/50000 = 0,00001 =]
  - Figyelés = 2\*000005 $0,00001 = [1 figyelési $ 0,25/50000 = = 0.000005]
- Vezénylési folyamat &amp; végrehajtási = **$0.168**
  - Tevékenység-végrehajtás = 001\*2 = 0,002 [Futtatás 1 = $1 és 1000 0,001 =]
  - Adattovábbítási tevékenységek = $0.166 (Prorated végrehajtási idő 10 percig. az Azure integrációs modul$ 0,25/óra)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Adatok másolása és átalakítás az Azure Databricks óránként

Ebben a forgatókönyvben a kívánt adatok másolása az AWS S3-ból az Azure Blob storage- és átalakíthatja az adatokat az Azure Databricks-óránkénti ütemezés szerint.

Ehhez a forgatókönyvhöz, létrehoz egy folyamatot a következő elemeket kell:

1. Egy másolási tevékenység a bemeneti adatkészlet az AWS S3 átmásolni az adatok és a egy kimeneti adatkészletet az adatok Azure Storage.
2. Egy-egy Azure Databricks tevékenység az adatátalakítás.
3. Egy ütemezési eseményindító óránként a folyamat végrehajtásához.

![Scenario2](media/pricing-concepts/scenario2.png)

| **Műveletek** | **Típusok és egységek** |
| --- | --- |
| Társított szolgáltatás létrehozása | 3/olvasási entitás  |
| Adatkészletek létrehozása | 4 olvasási/írási entitások (2 adatkészlet létrehozásához, a társított szolgáltatás hivatkozik 2) |
| Folyamat létrehozása | 3/olvasási entitások (1. a folyamat létrehozása, az adatkészlet hivatkozások 2) |
| Folyamatok beolvasása | 1 olvasási/írási entitás |
| Folyamat futtatása | 3 tevékenység-végrehajtás (1. az eseményindító-futtatás, a tevékenység-végrehajtás 2) |
| Másolás adatok feltételezzük: végrehajtási idő = 10 perc | 10 \* 4 azure integrációs modul (alapértelmezett beállítás DIU = 4) integrációs adategységek és teljesítményéről. másolási optimalizálása. További információkért lásd: [Ez a cikk](copy-activity-performance.md) |
| A figyelő folyamat feltételezzük: Csak 1 történt futtatása | 3 futtatása figyelés rekordok újrapróbált (1. a folyamat futtatásához, 2 a tevékenység-végrehajtásonként) |
| Hajtsa végre a Databricks tevékenység feltételezzük: végrehajtási idő 10 perc | 10 perc külső folyamat tevékenység-végrehajtás |

**Teljes forgatókönyv díjszabás: $0.16916**

- Data Factory műveletek = **$0.00012**
  - Olvasási/írási = 11\*00001 $0.00011 = [1 R/W $ 0,50/50000 = 0,00001 =]
  - Figyelés = 3\*000005 $0,00001 = [1 figyelési $ 0,25/50000 = = 0.000005]
- Vezénylési folyamat &amp; végrehajtási = **$0.16904**
  - Tevékenység-végrehajtás = 001\*3 = 0,003 [Futtatás 1 = $1 és 1000 0,001 =]
  - Adattovábbítási tevékenységek = $0.166 (Prorated végrehajtási idő 10 percig. az Azure integrációs modul$ 0,25/óra)
  - Külső a folyamatok tevékenységeit = $0.000041 (Prorated végrehajtási idő 10 percig. az Azure integrációs modul$ 0.00025/óra)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Adatok másolása és a dinamikus paraméterek óránként átalakítása

Ebben a forgatókönyvben a kívánt adatok másolása az AWS S3 az Azure Blob storage és az Azure Databricks átalakító (a dinamikus paraméterek a parancsfájl) egy óránkénti ütemezés szerint.

Ehhez a forgatókönyvhöz, létrehoz egy folyamatot a következő elemeket kell:

1. Egy másolási tevékenység az AWS S3, az adatok az Azure storage a kimeneti adatkészlet átmásolni az adatok bemeneti adatkészlet.
2. Egy keresési tevékenység az átalakítási parancsfájl dinamikusan átadása paramétereket.
3. Egy-egy Azure Databricks tevékenység az adatátalakítás.
4. Egy ütemezési eseményindító óránként a folyamat végrehajtásához.

![Scenario3](media/pricing-concepts/scenario3.png)

| **Műveletek** | **Típusok és egységek** |
| --- | --- |
| Társított szolgáltatás létrehozása | 3/olvasási entitás  |
| Adatkészletek létrehozása | 4 olvasási/írási entitások (2 adatkészlet létrehozásához, a társított szolgáltatás hivatkozik 2) |
| Folyamat létrehozása | 3/olvasási entitások (1. a folyamat létrehozása, az adatkészlet hivatkozások 2) |
| Folyamatok beolvasása | 1 olvasási/írási entitás |
| Folyamat futtatása | 4 tevékenység-végrehajtás (1. az eseményindító-futtatás, a tevékenység-végrehajtás 3) |
| Másolás adatok feltételezzük: végrehajtási idő = 10 perc | 10 \* 4 azure integrációs modul (alapértelmezett beállítás DIU = 4) integrációs adategységek és teljesítményéről. másolási optimalizálása. További információkért lásd: [Ez a cikk](copy-activity-performance.md) |
| A figyelő folyamat feltételezzük: Csak 1 történt futtatása | 4 futtatása figyelés rekordok újrapróbált (1. a folyamat futtatásához, 3, a tevékenység-végrehajtásonként) |
| Végrehajtani átvállalása keresési tevékenység: végrehajtási idő = 1 perc | a folyamatok tevékenységeit végrehajtása. 1 perc |
| Hajtsa végre a Databricks tevékenység feltételezzük: végrehajtási idő 10 perc | Külső folyamat tevékenység-végrehajtási 10 perc |

**Teljes forgatókönyv díjszabás: $0.17020**

- Data Factory műveletek = **$0.00013**
  - Olvasási/írási = 11\*00001 $0.00011 = [1 R/W $ 0,50/50000 = 0,00001 =]
  - Figyelés = 4\*000005 $0.00002 = [1 figyelési $ 0,25/50000 = = 0.000005]
- Vezénylési folyamat &amp; végrehajtási = **$0.17007**
  - Tevékenység-végrehajtás = 001\*4 = 0.004 [Futtatás 1 = $1 és 1000 0,001 =]
  - Adattovábbítási tevékenységek = $0.166 (Prorated végrehajtási idő 10 percig. az Azure integrációs modul$ 0,25/óra)
  - Folyamat-tevékenység = $0.00003 (Prorated végrehajtási idő 1 perce. az Azure integrációs modul$ 0,002/óra)
  - Külső a folyamatok tevékenységeit = $0.000041 (Prorated végrehajtási idő 10 percig. az Azure integrációs modul$ 0.00025/óra)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday-preview-pricing"></a>A leképezési adatok folyamat hibakeresési az egy normál workday (előzetes verzió díjszabása)

Egy adat-mérnök, mint Ön felelős tervezése, létrehozása és tesztelése a leképezési adatfolyamok minden nap. Jelentkezzen be az ADF felhasználói felületén a reggel, és az adatok elkezdenek beérkezni a hibakeresési mód engedélyezéséhez. Az alapértelmezett élettartam hibakeresési munkamenetek érték 60 perc. Hogy működik 10 óra, a nap folyamán, így a hibakeresési munkamenet soha nem jár le. Így a díj az adott napra vonatkozóan a következő lesz:

**10 (óra) x 8 (mag) x 0.112 $ $8.96 =**

## <a name="transform-data-in-blob-store-with-mapping-data-flows-preview-pricing"></a>Adatok átalakítása a blobtár az adatfolyamok (előzetes verzió díjszabása) leképezése

Ebben a forgatókönyvben a kívánt adatok átalakítása a Blob Store vizuálisan az ADF-leképezés adatfolyamok egy óránkénti ütemezés szerint.

Ehhez a forgatókönyvhöz, létrehoz egy folyamatot a következő elemeket kell:

1. Egy adatfolyam-tevékenységet az Adatátalakítási logikát.

2. Az Azure Storage az adatok bemeneti adatkészlet.

3. Az adatok Azure Storage a kimeneti adatkészletet.

4. Ütemezési eseményindító óránként a folyamat végrehajtásához.

| **Műveletek** | **Típusok és egységek** |
| --- | --- |
| Társított szolgáltatás létrehozása | 2 olvasási/írási entitás  |
| Adatkészletek létrehozása | 4 olvasási/írási entitások (2 adatkészlet létrehozásához, a társított szolgáltatás hivatkozik 2) |
| Folyamat létrehozása | 3/olvasási entitások (1. a folyamat létrehozása, az adatkészlet hivatkozások 2) |
| Folyamatok beolvasása | 1 olvasási/írási entitás |
| Folyamat futtatása | 2 tevékenység-végrehajtás (1. az eseményindító-futtatás, 1. a tevékenység-végrehajtás) |
| Data Flow feltételezések: végrehajtási idő = 10 perc + 10 perc élettartam | 10 \* általános számítási 10 TTL-8 mag |
| A figyelő folyamat feltételezzük: Csak 1 történt futtatása | 2 futtatása figyelés rekordok újrapróbált (1. a folyamat futtatásához, 1. a tevékenység-végrehajtásonként) |

**Teljes forgatókönyv díjszabás: $0.3011**

- Data Factory műveletek = **$0,0001**
  - Olvasási/írási = 10\*00001 $0,0001 = [1 R/W $ 0,50/50000 = 0,00001 =]
  - Figyelés = 2\*000005 $0,00001 = [1 figyelési $ 0,25/50000 = = 0.000005]
- Vezénylési folyamat &amp; végrehajtási = **$0.301**
  - Tevékenység-végrehajtás = 001\*2 = 0,002 [Futtatás 1 = $1 és 1000 0,001 =]
  - Data Flow tevékenységek $0.299 Prorated = 20 percig (10 percre megállítja végrehajtási ideje + 10 percre megállítja TTL). $0.112/ óra Azure integrációs modul az általános 8 maggal rendelkező számítási

## <a name="next-steps"></a>További lépések

Most, hogy megismerkedett az Azure Data Factory díjszabása, kezdheti!

- [Adat-előállító létrehozása az Azure Data Factory felhasználói felületének használatával](quickstart-create-data-factory-portal.md)

- [Az Azure Data Factory bemutatása](introduction.md)

- [Az Azure Data Factoryban vizuális szerzői](author-visually.md)
