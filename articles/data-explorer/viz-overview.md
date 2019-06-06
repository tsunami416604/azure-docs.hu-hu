---
title: Az Azure Data Explorer adatmegjelenítés
description: További információ a különböző módokon jelenítheti meg az Azure Data Explorer adatok
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 85c37b6d626fc9942f5df956e738431d2727d282
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481834"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Az Azure Data Explorer adatmegjelenítés 

Az Azure Data Explorer egy gyors és hatékonyan méretezhető exploration szolgáltatás, amellyel hatalmas mennyiségű adat összetett elemzési megoldásokat hozhat létre naplóját és a telemetriai adatok. Az Azure Data Explorer integrálható különböző vizualizációs eszközök, így az adatok megjelenítése és az eredményeket megoszthatja a szervezet. Ezek az adatok átalakíthatók ahhoz, hogy az befolyásolná az üzleti információkká.

Adatvizualizációk és jelentéskészítés a kritikus fontosságú lépés az adatok elemzési folyamat során. Azure adatkezelő számos BI szolgáltatás támogatja, így használhatja azt, amelyik leginkább megfelel a forgatókönyv és a költségvetés.

* Az Azure Data Explorer Vizualizációk: Kusto lekérdezési nyelv segítségével a [ `render operator` ](/azure/kusto/query/renderoperator) kínál különféle vizualizációtípusokat ábrázolhatja a lekérdezés eredményeit. A rendellenességek észlelése és előrejelzését, gépi tanulási és egyéb hasznosak a lekérdezés vizualizációkat.

* [Power BI](https://powerbi.microsoft.com): Az Azure Data Explorer lehetővé teszi, hogy a Power bi-hoz, különböző módszerekkel: 

  * [Beépített natív Power BI-összekötő](/azure/data-explorer/power-bi-connector)

  * [Importálás az Azure Data Explorer lekérdezése Power BI-bA](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL-lekérdezés](/azure/data-explorer/power-bi-sql-query).

* [Microsoft Excel](https://products.office.com/excel): Az Azure Data Explorer lehetővé teszi, hogy a Kapcsolódás az Excelhez a beépített natív Excel-összekötő használatával, vagy importálhat egy lekérdezést az Azure Data Explorer Excel formátumba.

* [Grafana](https://grafana.com): Grafana biztosít az Azure Data Explorer beépülő modul, amely lehetővé teszi, hogy az Azure Data Explorer adatainak megjelenítése. Ön [beállítása Azure adatkezelő adatforrásként Grafana számára, és ezután megjelenítheti az adatokat](/azure/data-explorer/grafana)

* [Sisense](https://www.sisense.com): Az Azure Data Explorer lehetővé teszi az Sisense csatlakozni a JDBC-összekötő segítségével. Ön [beállítása az Azure Data Explorer adatforrásként Sisense számára, és ezután megjelenítheti az adatokat](/azure/data-explorer/sisense).

* [A tableau](https://www.tableau.com): Az Azure Data Explorer lehetővé teszi, hogy csatlakozzon a Tableau az [ODBC-összekötő és a Tableau megjeleníthetők](/azure/data-explorer/connect-odbc).

* [Qlik](https://www.qlik.com): Az Azure Data Explorer lehetővé teszi, hogy a Qlik használatával csatlakozni a [ODBC-összekötő](/azure/data-explorer/connect-odbc).