---
title: Azure Stream Analytics Azure SQL Database kimenete
description: Ez a cikk a Azure Stream Analytics számára Azure SQL Database ismerteti.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 4310bd94edd5ebe14eab40b6d19e2bacbdd1b03c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906222"
---
# <a name="azure-sql-database-output-from-azure-stream-analytics"></a>Azure Stream Analytics Azure SQL Database kimenete

A [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) a természettel rokon adatokhoz, illetve olyan alkalmazásokhoz is használhatja, amelyek a kapcsolódó adatbázisban tárolt tartalomtól függenek. Azure Stream Analytics feladatok írását egy meglévő táblába SQL Database. A tábla sémájának pontosan egyeznie kell a feladatok kimenetében szereplő mezőkkel és típusokkal. Az [Azure szinapszis Analytics](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) kimenetét az SQL Database output kapcsolón keresztül is megadhatja. Ha többet szeretne megtudni az írási sebesség javításának módjairól, tekintse meg a [Azure SQL Database as stream Analytics a kimenettel](stream-analytics-sql-output-perf.md) című cikket.

Az [Azure SQL felügyelt példányát](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) is használhatja kimenetként. [Konfigurálnia kell a nyilvános végpontot az SQL felügyelt példányában](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) , majd manuálisan kell konfigurálnia a következő beállításokat a Azure stream Analyticsban. A SQL Servert futtató Azure-beli virtuális gépeket az alábbi beállítások manuális konfigurálásával is támogatja.

## <a name="output-configuration"></a>Kimeneti konfiguráció

A következő táblázat felsorolja a tulajdonságok nevét és leírását SQL Database kimenet létrehozásához.

| Tulajdonság neve | Leírás |
| --- | --- |
| Kimeneti alias |Egy rövid név, amely a lekérdezésekben a lekérdezés kimenetének az adatbázisba való irányításához használatos. |
| Adatbázis | Annak az adatbázisnak a neve, ahová a kimenetet küldi. |
| Kiszolgálónév | A logikai SQL-kiszolgáló neve vagy a felügyelt példány neve. SQL felügyelt példány esetén a 3342-es portot kell megadnia. Például: *sampleserver. public. database. Windows. net, 3342* |
| Felhasználónév | Az adatbázishoz írási hozzáféréssel rendelkező Felhasználónév. A Stream Analytics csak az SQL-hitelesítést támogatja. |
| Jelszó | Az adatbázishoz való kapcsolódáshoz használt jelszó. |
| Táblázat | A tábla neve, ahol a kimenet meg van írva. A tábla neve megkülönbözteti a kis-és nagybetűket. A táblázat sémájának pontosan meg kell egyeznie a feladatok által létrehozott mezők és típusok számával. |
|Partíciós séma öröklése| Az előző lekérdezési lépés particionálási sémájának öröklésére szolgáló lehetőség, amely lehetővé teszi, hogy teljesen párhuzamos topológiát engedélyezzen több íróival a táblához. További információ: [Azure stream Analytics kimenet Azure SQL Database](stream-analytics-sql-output-perf.md).|
|Kötegek maximális száma| Az összes tömeges beszúrási tranzakcióval ellátott rekordok számának ajánlott felső korlátja.|

Két olyan adapter van, amely lehetővé teszi a Azure Stream Analytics kimenetét az Azure szinapszis Analyticsbe (korábban SQL Data Warehouse): SQL Database és az Azure Szinapszisba. Azt javasoljuk, hogy az SQL Database adapter helyett az Azure szinapszis Analytics-adaptert válassza, ha az alábbi feltételek bármelyike teljesül:

* **Átviteli sebesség**: Ha a várt átviteli sebesség meghaladja a 10 MB/s-ot, a jobb teljesítmény érdekében használja az Azure szinapszis kimeneti kapcsolóját.

* **Bemeneti partíciók**: Ha nyolc vagy több bemeneti partícióval rendelkezik, az Azure szinapszis kimeneti beállításával jobban kibővítheti a méretezést.

## <a name="partitioning"></a>Particionálás

A particionálásnak engedélyezve kell lennie, és a lekérdezésben a PARTITION BY záradék alapján kell megfelelnie. Ha a particionálás öröklése beállítás engedélyezve van, az a [teljes párhuzamosítható lekérdezések](stream-analytics-scale-jobs.md)bemeneti particionálását követi. Ha többet szeretne megtudni az adatok Azure SQL Databaseba való betöltésével kapcsolatos jobb írási teljesítmény eléréséről, tekintse meg [Azure stream Analytics kimenetét Azure SQL Database](stream-analytics-sql-output-perf.md).

## <a name="output-batch-size"></a>Kimeneti köteg mérete

Az üzenetek maximális méretét a **kötegek maximális száma**beállítással állíthatja be. Az alapértelmezett maximális érték 10 000, és az alapértelmezett minimum a 100 sor egy tömeges beszúrással. További információ: [Azure SQL-korlátok](../sql-database/sql-database-resource-limits.md). A kötegek kezdetben tömegesen vannak beszúrva a kötegek maximális számával. A Batch az SQL-újrapróbálkozást lehetővé tevő hibák alapján fél (a minimális kötegek száma) között oszlik meg.

## <a name="next-steps"></a>Következő lépések

* [Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](stream-analytics-quick-create-portal.md)
* [Gyors útmutató: Azure Stream Analytics-feladatok létrehozása az Azure CLI használatával](quick-create-azure-cli.md)
* [Gyors útmutató: Azure Stream Analytics-feladatok létrehozása ARM-sablon használatával](quick-create-azure-resource-manager.md)
* [Gyors útmutató: Stream Analytics-feladatok létrehozása Azure PowerShell használatával](stream-analytics-quick-create-powershell.md)
* [Rövid útmutató: Azure Stream Analytics-feladatok létrehozása a Visual Studio használatával](stream-analytics-quick-create-vs.md)
* [Rövid útmutató: Azure Stream Analytics-feladatok létrehozása a Visual Studio Code-ban](quick-create-visual-studio-code.md)
