---
title: Cheat Sheet – Azure szinapszis Analytics (munkaterületek – előzetes verzió)
description: Útmutató a felhasználónak az Azure szinapszis Analytics szolgáltatással való bejárásához
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 3141f8044a4a257de8022ff789b12d5d3e6e7a90
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85807026"
---
# <a name="azure-synapse-analytics-cheat-sheet"></a>Azure szinapszis Analytics – Cheat Sheet

[!INCLUDE [preview](includes/note-preview.md)]

Az Azure szinapszis Analytics Cheat Sheet végigvezeti Önt a szolgáltatás alapvető fogalmait és a fontos parancsokat. Ez a cikk segítséget nyújt az új tanulók és a fontos Azure szinapszis-témakörök kiemeléséhez.

## <a name="architecture"></a>Architektúra

> [!div class="mx-imgBorder"]
>![Szinapszis-architektúra](media/overview-cheat-sheet/azure-synapse-architecture-cheat-sheet.png)

## <a name="terminology"></a>Terminológia
| Kifejezés                         | Definíció      |
|:---                                 |:---                 |
| **Szinapszis-munkaterület** | Biztonságos együttműködési határ az Azure-beli felhőalapú vállalati elemzésekhez. A munkaterület egy adott régióban van üzembe helyezve, és rendelkezik egy társított ADLS Gen2 fiókkal és fájlrendszerrel (az ideiglenes adattárolók tárolásához). A munkaterület egy erőforráscsoport alatt található. |
| **Synapse SQL**   | Az elemzéseket készletekkel vagy igény szerinti funkciókkal futtathatja.  |
| **SQL-készlet**   | a megfelelő adatbázisokkal rendelkező 0 – N SQL kiépített erőforrások egy munkaterületen helyezhetők üzembe. Minden SQL-készlethez tartozik egy társított adatbázis. Egy SQL-készlet méretezhető, szüneteltethető és folytatható manuálisan vagy automatikusan. Az SQL-készletek 100 DWU akár 30 000 DWU is méretezhetők.       |
| **Igény szerinti SQL**   | Elosztott adatfeldolgozási rendszer, amely nagy léptékű adathoz készült, és lehetővé teszi a T-SQL-lekérdezések futtatását a adat-Lake-ben. Kiszolgáló nélküli, így nem kell felügyelni az infrastruktúrát.       |
|**A szinapszis Apache Spark** | Spark Run – a Spark-készletben használt idő. A jelenleg támogatott verzió a Spark 2,4 a Python 3.6.1, a Scala 2.11.12, a .NET-támogatás a Apache Spark 0,5 és a Delta Lake 0,3.  | 
| **Apache Spark készlet**  | a 0 – N Spark kiosztott erőforrásai és a hozzájuk tartozó adatbázisok is üzembe helyezhetők egy munkaterületen. A Spark-készlet lehet automatikusan szüneteltethető, folytatható és méretezhető.  |
| **Spark-alkalmazás**  |   Egy illesztőprogram-folyamatból és egy végrehajtó folyamatokból áll. A Spark-alkalmazások egy Spark-készleten futnak.            |
| **Spark-munkamenet**  |   Spark-alkalmazás egyesített belépési pontja. Lehetővé teszi a Spark különböző funkcióinak és kisebb számú szerkezetének kezelését. Jegyzetfüzet futtatásához létre kell hoznia egy munkamenetet. Egy munkamenet konfigurálható úgy, hogy egy adott méretű, meghatározott számú végrehajtón fusson. A notebook-munkamenet alapértelmezett konfigurációja 2 közepes méretű végrehajtón fut. |
| **SQL-kérelem**  |   Olyan művelet, mint például az SQL-készlet vagy az SQL igény szerinti futtatása. |
|**Adatintegráció**| Lehetővé teszi az adatgyűjtést a különböző források között, valamint a munkaterületen belül vagy a munkaterületen kívül futó tevékenységek szervezését.| 
|**Artifacts**| A felhasználó által az adatforrások kezeléséhez, a fejlesztéshez, a koordináláshoz és a vizualizációhoz szükséges összes objektumot magában foglalja.|
|**Jegyzetfüzet**| A Scala, a PySpark, a C# és a SparkSQL támogató interaktív és reaktív adatelemzési és mérnöki felület. |
|**Spark-feladatdefiníció**|Egy Spark-feladatoknak a kódot és függőségeit tartalmazó szerelvény jar használatával történő elküldésére szolgáló felület.|
|**Adatfolyam**|  A teljes körű vizuális élményt nyújt, és nincs szükség kódolásra big data átalakításhoz. Az optimalizálást és a végrehajtást kiszolgáló nélküli módon kezeljük. |
|**SQL-szkript**| Egy fájlban mentett SQL-parancsok készlete. Egy SQL-parancsfájl egy vagy több SQL-utasítást is tartalmazhat. SQL-kérelmek SQL-készleten vagy igény szerinti SQL-en keresztüli futtatására is használható.|
|**Folyamat**| A feladatokat elvégző tevékenységek logikai csoportosítása.|
|**Tevékenység**| Meghatározza az adatokon végrehajtandó műveleteket, például az adatok másolását, egy jegyzetfüzet vagy egy SQL-parancsfájl futtatását.|
|**Eseményindító**| Folyamat végrehajtása. Futtatható manuálisan vagy automatikusan (Schedule, bukdácsoló vagy eseményvezérelt).|
|**Társított szolgáltatások**| Kapcsolati karakterláncok, amelyek meghatározzák a munkaterület külső erőforrásokhoz való kapcsolódásához szükséges kapcsolati adatokat.|
|**Adatkészlet**|  Az adatok elnevezett nézete, amely egyszerűen rámutat vagy hivatkozik a tevékenységekben a bemenetként és kimenetként használt adatokra. Egy társított szolgáltatáshoz tartozik.|

## <a name="next-steps"></a>Következő lépések

- [Munkaterület létrehozása](quickstart-create-workspace.md)
- [A szinapszis Studio használata](quickstart-synapse-studio.md)
- [SQL-készlet létrehozása](quickstart-create-sql-pool-portal.md)
- [Apache Spark-készlet létrehozása](quickstart-create-apache-spark-pool-portal.md)
- [Az igény szerinti SQL használata](quickstart-sql-on-demand.md)

