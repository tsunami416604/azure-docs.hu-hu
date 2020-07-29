---
title: Adatfolyamok az Azure SQL Edge-ben (előzetes verzió)
description: Ismerje meg az Azure SQL Edge (előzetes verzió) adatátviteli szolgáltatását.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 866c74fbdfcfcef7cbb7d6cddb360c4265a2f776
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84669613"
---
# <a name="data-streaming-in-azure-sql-edge-preview"></a>Adatfolyamok az Azure SQL Edge-ben (előzetes verzió)

Az Azure SQL Edge (előzetes verzió) az alábbi lehetőségeket biztosítja az adatfolyamok megvalósításához: 

- Az Azure-ban létrehozott Azure Stream Analytics Edge-feladatok üzembe helyezése. További információ: [Azure stream Analytics feladatok telepítése](deploy-dacpac.md).
- A T-SQL streaming használatával adatfolyam-feladatok hozhatók létre az Azure SQL Edge-ben anélkül, hogy az Azure-ban be kellene állítania a folyamatos átviteli feladatokat. 

Bár az Azure SQL Edge-ben mindkét lehetőség használható az adatfolyamok megvalósítására, csak az egyiket használhatja. Ha mindkettőt használja, a verseny olyan feltételekkel is rendelkezhet, amelyek hatással vannak az adatátviteli műveletek működésére.

A T-SQL streaming ennek a cikknek a középpontjában áll. Valós idejű adatfolyam-továbbítást, elemzést és eseményt biztosít, így egyszerre több forrásból is elemezheti és feldolgozhatja a gyors adatfolyam-továbbítási adatok nagy mennyiségét. A T-SQL streaming egy olyan nagy teljesítményű streaming motor használatával készült, amely a Microsoft Azure [Azure stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction) . A szolgáltatás a szélén futó Azure Stream Analytics által kínált hasonló képességeket támogatja.

A Stream Analyticshoz hasonlóan a T-SQL streaming felismeri a mintákat és a kapcsolatokat számos IoT bemeneti forrásból, például eszközökből, érzékelőkből és alkalmazásokból kinyert információk alapján. Ezeket a mintákat használhatja a műveletek elindításához és a munkafolyamatok kezdeményezéséhez. Létrehozhat például riasztásokat, adatokat adhat hozzá egy jelentéskészítési vagy vizualizációs megoldáshoz, vagy tárolhatja az adatokat későbbi használatra. 

A T-SQL streaming segítségével:

* Valós idejű telemetria streamek elemzése a IoT-eszközökről.
* Az autonóm és az illesztőprogram nélküli járművekből generált adatok valós idejű elemzési módszereit használhatja.
* Használjon távoli monitorozást és prediktív karbantartást a nagy értékű ipari vagy gyártási eszközökhöz.
* A IoT-érzékelő beolvasása a mezőgazdaságban vagy az energia-farmban anomáliák észlelésével és a minták felismerésével.

## <a name="how-does-t-sql-streaming-work"></a>Hogyan működik a T-SQL streaming?

A T-SQL streaming pontosan ugyanolyan módon működik, mint a [Azure stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction#how-does-stream-analytics-work). Például a folyamatos átviteli *feladatok* koncepcióját használja a valós idejű adatfolyamok feldolgozásához. 

A stream Analytics-feladatok a következőkből állnak:

- **Stream bemenete**: Ez határozza meg az adatforrással létesített kapcsolatokat, hogy beolvassa az adatfolyamot a rendszerből. Az Azure SQL Edge jelenleg a következő stream típusú bemeneti típusokat támogatja:
    - Peremhálózati hub
    - Kafka (a Kafka-bemenetek támogatása jelenleg csak az Azure SQL Edge Intel/AMD64 verziójában érhető el.)

- **Stream kimenete**: Ez határozza meg az adatforráshoz való kapcsolódást az adatfolyamnak a szolgáltatásba való írásához. Az Azure SQL Edge jelenleg a következő stream-kimeneti típusokat támogatja
    - Peremhálózati hub
    - SQL (az SQL-kimenet lehet az Azure SQL Edge példányán belüli helyi adatbázis, illetve távoli SQL Server vagy Azure SQL Database.) 
    - Azure Blob Storage

- **Stream-lekérdezés**: Ez határozza meg a bemeneti adatfolyamra alkalmazni kívánt átalakítást, összesítéseket, szűrést, rendezést és illesztéseket, mielőtt az adatfolyam kimenetére íródik. Az adatfolyam-lekérdezés ugyanarra a lekérdezési nyelvre épül, mint amelyet a Stream Analytics használ. További információ: [stream Analytics lekérdezési nyelv](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

> [!IMPORTANT]
> A T-SQL streaming a Stream Analyticstól eltérően jelenleg nem támogatja a keresések [és az UDF és a uda adatfolyam-](https://docs.microsoft.com/azure/stream-analytics/streaming-technologies#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c) [feladatokban](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data) való használatát.

> [!NOTE]
> A T-SQL streaming csak a Stream Analytics által támogatott nyelvi felület részhalmazát támogatja. További információ: [stream Analytics lekérdezési nyelv](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

## <a name="limitations-and-restrictions"></a>Korlátozások és korlátozások

A következő korlátozások és korlátozások érvényesek a T-SQL streamingre. 

- Egy adott időpontban csak egy folyamatos átviteli feladatok lehetnek aktívak. A már futó feladatokat le kell állítani egy másik feladat elindítása előtt.
- A folyamatos átviteli feladatok végrehajtása egyetlen szálból áll. Ha a folyamatos átviteli feladatok több lekérdezést is tartalmaznak, a rendszer a lekérdezéseket soros sorrendben értékeli ki.

## <a name="next-steps"></a>További lépések

- [Stream Analytics-feladatok létrehozása az Azure SQL Edge-ben (előzetes verzió)](create-stream-analytics-job.md)
- [A stream-feladatokhoz társított metaadatok megtekintése az Azure SQL Edge-ben (előzetes verzió)](streaming-catalog-views.md)
- [Külső stream létrehozása](create-external-stream-transact-sql.md)