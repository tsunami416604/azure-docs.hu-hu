---
title: Az Azure Data Explorer adatvizualizációja
description: Ismerje meg, hogy miként jelenítheti meg az Azure Data Explorer adatait
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 7b1c7825beb769b610d661cb9644fc3f3919d548
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139062"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Adatmegjelenítés az Azure Data Explorer rel 

Az Azure Data Explorer egy gyors és jól méretezhető adatfeltárási szolgáltatás a napló- és telemetriai adatokhoz, amely et összetett elemzési megoldások létrehozásához használják hatalmas mennyiségű adathoz. Az Azure Data Explorer különböző vizualizációs eszközökkel integrálható, így megjelenítheti az adatokat, és megoszthatja az eredményeket a szervezeten belül. Ezek az adatok perelhető elemzési információkká alakíthatók, hogy hatást gyakoroljanak a vállalkozásra.

Az adatvizualizáció és a jelentéskészítés kritikus lépés az adatelemzési folyamatban. Az Azure Data Explorer számos üzletiintelligencia-szolgáltatást támogat, így a forgatókönyvnek és a költségvetésnek leginkább megfelelőt használhatja.

## <a name="kusto-query-language-visualizations"></a>Kusto nyelvi vizualizációk lekérdezése

A Kusto [`render operator`](/azure/kusto/query/renderoperator) lekérdezési nyelv különböző vizualizációkat kínál, például táblázatokat, kördiagramokat és sávdiagramokat a lekérdezéseredmény ábrázolására. A lekérdezési vizualizációk hasznosak az anomáliadetektálás és -előrejelzés, a gépi tanulás és egyebek terén.

## <a name="power-bi"></a>Power BI

Az Azure Data Explorer lehetővé teszi a [Power BI-hoz](https://powerbi.microsoft.com) való csatlakozást különböző módszerekkel: 

  * [Beépített natív Power BI-csatlakozó](/azure/data-explorer/power-bi-connector)

  * [Importálás lekérdezése az Azure Data Explorerből a Power BI-ba](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL-lekérdezés](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Az Azure Data Explorer lehetővé teszi, hogy csatlakozzon a [Microsoft Excel](https://products.office.com/excel) [a beépített natív Excel-összekötő,](excel-connector.md)vagy [importálja a lekérdezést](excel-blank-query.md) az Azure Data Explorer az Excelbe.

## <a name="grafana"></a>Grafana

[A Grafana](https://grafana.com) egy Azure Data Explorer beépülő modult biztosít, amely lehetővé teszi az Azure Data Explorer ből származó adatok megjelenítését. [Az Azure Data Explorert a Grafana adatforrásaként állítja be, majd megjeleníti az adatokat.](/azure/data-explorer/grafana) 

## <a name="kibana"></a>Kibana

Az Azure Data Explorer lehetővé teszi, hogy csatlakozzon [kibana (a Discover lap)](https://www.elastic.co/guide/en/kibana/6.8/discover.html) segítségével K2Bridge, egy nyílt forráskódú összekötő. [Az Azure Data Explorert a Kibana adatforrásaként állítja be, majd megjeleníti az adatokat.](/azure/data-explorer/k2bridge)

## <a name="odbc-connector"></a>ODBC-összekötő

Az Azure Data Explorer egy [ODBC-összekötőt](connect-odbc.md) biztosít, így az ODBC-t támogató alkalmazások csatlakozhatnak az Azure Data Explorerhez.

## <a name="tableau"></a>Tableau

Az Azure Data Explorer lehetővé teszi, hogy az [ODBC-összekötő](/azure/data-explorer/connect-odbc) használatával csatlakozzon a [Tableau-hoz,](https://www.tableau.com) majd [megjelenítse az adatokat a Tableau-ban.](tableau.md)

## <a name="qlik"></a>Qlik

Az Azure Data Explorer lehetővé teszi, hogy az [ODBC-összekötő](/azure/data-explorer/connect-odbc) használatával csatlakozzon a [Qlikhez,](https://www.qlik.com) majd hozzon létre Qlik Sense irányítópultokat, és vizualizálja az adatokat. Az alábbi videó segítségével megtudhatja, hogyan jelenítheti meg az Azure Data Explorer adatait a Qlik segítségével. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Az Azure Data Explorer lehetővé teszi a [Sisense-hez](https://www.sisense.com) való csatlakozást a JDBC-összekötő használatával. [Az Azure Data Explorert a Sisense adatforrásaként állítja be, majd megjeleníti az adatokat.](/azure/data-explorer/sisense)

## <a name="redash"></a>Redash

A [Redash](https://redash.io/) segítségével irányítópultokat hozhat létre, és megjelenítheti az adatokat. [Állítsa be az Azure Data Explorert adatforrásként a Redash számára, majd jelenítse meg az adatokat.](/azure/data-explorer/redash)