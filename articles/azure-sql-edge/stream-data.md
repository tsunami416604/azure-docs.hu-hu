---
title: Adatfolyamok az Azure SQL Edge-ben (előzetes verzió)
description: Tudnivalók az Azure SQL Edge-ben zajló adatátvitelről (előzetes verzió)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 15b6b80f9924b050b388d74adc1d67db6a386134
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597309"
---
# <a name="data-streaming-in-azure-sql-edge-preview"></a>Adatfolyamok az Azure SQL Edge-ben (előzetes verzió)

Az Azure SQL Edge (előzetes verzió) két különböző lehetőséget biztosít az adatfolyamok megvalósítására. 

1. Azure streaming Analytics Edge-feladatok üzembe helyezése az Azure-ban. Az Azure streaming Analytics Edge-feladatok Azure SQL Edge-ben történő üzembe helyezésével kapcsolatos további információkért tekintse meg a [Azure stream Analytics feladatok üzembe helyezése](deploy-dacpac.md)című témakört.
2. Az új **T-SQL streaming** funkció használatával adatfolyam-feladatokat hozhat létre az SQL Edge-ben anélkül, hogy az Azure-ban be kellene állítania a folyamatos átviteli feladatokat. 

Noha az SQL Edge-ben mindkét lehetőséget használhatja az adatfolyamok megvalósítására, erősen ajánlott csak egyet használni. Ha mindkettőt használja, lehetséges, hogy a verseny olyan feltételekkel járhat, amelyek hatással vannak az adatátviteli műveletek működésére.

A dokumentum további része a **T-SQL streaming**új funkcióját jelenti, amely valós idejű adatfolyam-továbbítást, elemzést és esemény-feldolgozást biztosít a nagy mennyiségű, egyszerre több forrásból származó gyors adatfolyam-adatok elemzéséhez és feldolgozásához. A *T-SQL streaming* ugyanazon a nagy teljesítményű adatfolyam-továbbítási motoron alapul, amely a Microsoft Azure [Azure stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction) , és támogatja a szélén futó Azure stream Analytics által kínált hasonló funkciókat.

A Azure Stream Analyticshoz hasonlóan a T-SQL streaming lehetővé teszi a minták és kapcsolatok felismerését számos IoT bemeneti forrásból, például eszközökről, érzékelőkről és alkalmazásokból kinyert információk alapján. Ezek a minták felhasználhatók a műveletek indításához és olyan munkafolyamatok elindításához, mint például a riasztások létrehozása, az információk bejelentési vagy vizualizációs megoldásba való továbbítása, vagy az adatok későbbi használatra történő tárolása. 

A következő forgatókönyvek példákat mutatnak a T-SQL streaming használatára:

* Valós idejű telemetria streamek elemzése a IoT-eszközökről.
* Az autonóm és az illesztőprogram nélküli járművekből generált adatok valós idejű elemzése.
* Távoli monitorozás és prediktív karbantartás a nagy értékű ipari vagy gyártási eszközökhöz.
* A IoT-érzékelők beolvasása a mezőgazdaságban vagy egy energetikai farmban.

## <a name="how-does-t-sql-streaming-work"></a>Hogyan működik a T-SQL streaming?

A T-SQL streaming pontosan ugyanolyan módon működik, mint a [Azure stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction#how-does-stream-analytics-work), például a folyamatos átviteli feladatok koncepcióját használja a valós idejű adatfolyamok feldolgozásához. 

A stream Analytics-feladatok a következőkből állnak:

- Stream bemenete – A stream bemenete meghatározza az adatforrással létesített kapcsolatokat, hogy beolvassa az adatfolyamot a rendszerből. Az Azure SQL Edge jelenleg a következő stream típusú bemeneti típusokat támogatja:
    - Peremhálózati hub
    - Kafka – a Kafka-bemenetek támogatása jelenleg csak az Azure SQL Edge Intel/AMD64 verzióiban érhető el.

- Stream output – A stream kimenete meghatározza az adatforráshoz való kapcsolódást az adatstreamnek a szolgáltatásba való írásához. Az Azure SQL Edge jelenleg a következő stream-kimeneti típusokat támogatja
    - Peremhálózati hub
    - SQL – az SQL-kimenet lehet egy helyi adatbázis az SQL Edge-példányon belül, vagy egy távoli SQL Server vagy Azure SQL Database. 
    - Azure Blob Storage

- Stream-lekérdezés – az adatfolyam-lekérdezés határozza meg a bemeneti adatfolyamra alkalmazni kívánt transzformációt, összesítéseket, szűrési, rendezési és illesztési adatokat, mielőtt az adatfolyam kimenetére íródik. Az adatfolyam-lekérdezés a Azure Stream Analytics által használt lekérdezési nyelven alapul. Azure Stream Analytics lekérdezési nyelvre vonatkozó további információkért tekintse meg [stream Analytics lekérdezési nyelvet](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

> [!IMPORTANT]
> A T-SQL streaming a Azure Stream Analyticstól eltérően jelenleg nem támogatja a keresési adatok vagy az [UDF és a uda adatfolyam-](https://docs.microsoft.com/azure/stream-analytics/streaming-technologies#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c) [feladatainak használatával történő használatát](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data) .

> [!NOTE]
> A T-SQL streaming csak a Azure Stream Analytics által támogatott nyelvi felület részhalmazát támogatja. Azure Stream Analytics lekérdezési nyelvvel kapcsolatos további információkért lásd: [stream Analytics lekérdezési nyelv](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

## <a name="limitations-and-restrictions"></a>Korlátozások és korlátozások

A következő korlátozások és korlátozások érvényesek a T-SQL Streamingre. 

- Egy adott időben csak egy folyamatos átviteli feladatok lehetnek aktívak. A már végrehajtás alatt álló feladatokat le kell állítani egy másik feladat elindítása előtt.
- A folyamatos átviteli feladatok végrehajtása egyetlen szálból áll. Ha a folyamatos átviteli feladatok több lekérdezést is tartalmaznak, a rendszer a lekérdezéseket soros sorrendben értékeli ki.

## <a name="next-steps"></a>További lépések

- [Stream Analytics-feladatok létrehozása az Azure SQL Edge-ben (előzetes verzió)](create-stream-analytics-job.md)
- [A stream-feladatokhoz társított metaadatok megtekintése az Azure SQL Edge-ben (előzetes verzió)](streaming-catalog-views.md)
- [Külső stream létrehozása](create-external-stream-transact-sql.md)