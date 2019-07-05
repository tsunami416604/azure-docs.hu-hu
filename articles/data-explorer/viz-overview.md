---
title: Az Azure Data Explorer adatmegjelenítés
description: További információ a különböző módokon jelenítheti meg az Azure Data Explorer adatok
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: d1c73d8eb65ed5d67d5250b4a3bca3b80450001e
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536717"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Az Azure Data Explorer adatmegjelenítés 

Az Azure Data Explorer egy gyors és hatékonyan méretezhető exploration szolgáltatás, amellyel hatalmas mennyiségű adat összetett elemzési megoldásokat hozhat létre naplóját és a telemetriai adatok. Az Azure Data Explorer integrálható különböző vizualizációs eszközök, így az adatok megjelenítése és az eredményeket megoszthatja a szervezet. Ezek az adatok átalakíthatók ahhoz, hogy az befolyásolná az üzleti információkká.

Adatvizualizációk és jelentéskészítés a kritikus fontosságú lépés az adatok elemzési folyamat során. Azure adatkezelő számos BI szolgáltatás támogatja, így használhatja azt, amelyik leginkább megfelel a forgatókönyv és a költségvetés.

## <a name="kusto-query-language-visualizations"></a>Kusto-lekérdezés nyelvi Vizualizációk

A Kusto-lekérdezés nyelv [ `render operator` ](/azure/kusto/query/renderoperator) kínál a különböző Vizualizációk, például a táblák, tortadiagramot vagy sávdiagramot ábrázolhatja a lekérdezési eredmények. A rendellenességek észlelése és előrejelzését, gépi tanulási és egyéb hasznosak a lekérdezés vizualizációkat.

## <a name="power-bi"></a>Power BI

Az Azure Data Explorer lehetővé teszi, hogy csatlakozni [Power BI](https://powerbi.microsoft.com) különböző módszerekkel: 

  * [Beépített natív Power BI-összekötő](/azure/data-explorer/power-bi-connector)

  * [Importálás az Azure Data Explorer lekérdezése Power BI-bA](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL-lekérdezés](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Az Azure Data Explorer lehetővé teszi, hogy csatlakozni [Microsoft Excel](https://products.office.com/excel) Excel-összekötő használatával a beépített natív, vagy importálhat egy lekérdezést az Azure Data Explorer Excel formátumba.

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) biztosít az Azure Data Explorer beépülő modul, amely lehetővé teszi, hogy az Azure Data Explorer adatainak megjelenítése. Ön [beállítása Azure adatkezelő adatforrásként Grafana számára, és ezután megjelenítheti az adatokat](/azure/data-explorer/grafana). 

## <a name="odbc-connector"></a>ODBC-összekötő

Az Azure Data Explorer biztosít egy [megnyitott adatbázis-kapcsolat (ODBC) összekötő](connect-odbc.md) így minden olyan alkalmazás, amely támogatja a ODBC csatlakozhatnak az Azure Data Explorer.

## <a name="tableau"></a>A tableau

Az Azure Data Explorer lehetővé teszi, hogy csatlakozni [Tableau](https://www.tableau.com) használatával a [ODBC-összekötő](/azure/data-explorer/connect-odbc) , majd [megjelenítheti az adatokat, a Tableau](tableau.md).

## <a name="qlik"></a>Qlik

Az Azure Data Explorer lehetővé teszi, hogy csatlakozni [Qlik](https://www.qlik.com) használatával a [ODBC-összekötő](/azure/data-explorer/connect-odbc) Qlik Sense irányítópultokat hozhat létre és megjelenítheti az adatokat. Használja az alábbi videó, tudhat meg az Azure Data Explorer adatok vizualizálása a Qlik. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Az Azure Data Explorer lehetővé teszi, hogy csatlakozni [Sisense](https://www.sisense.com) a JDBC-összekötő segítségével. Ön [beállítása az Azure Data Explorer adatforrásként Sisense számára, és ezután megjelenítheti az adatokat](/azure/data-explorer/sisense).