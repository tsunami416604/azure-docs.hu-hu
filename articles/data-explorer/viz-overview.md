---
title: Az Azure Adatkezelő adatvizualizáció
description: Ismerje meg az Azure-Adatkezelő adatainak megjelenítésének különböző módszereit
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 32ccc5ad236c87d3609798a8432db14ee440d067
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064563"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Adatvizualizáció az Azure Adatkezelő 

Az Azure Adatkezelő egy gyors és rugalmasan méretezhető adatfelderítési szolgáltatás a napló-és telemetria, amelyekkel összetett elemzési megoldások hozhatók létre nagy mennyiségű adattal. Az Azure Adatkezelő különböző vizualizációs eszközökkel integrálódik, így megjelenítheti az adatait, és megoszthatja az eredményeket a szervezeten belül. Ezek az információk a gyakorlatban hasznosítható elemzésekben alakíthatók át, ami hatással lehet a vállalatra.

Az adatvizualizáció és a jelentéskészítés az adatelemzési folyamat kritikus lépése. Az Azure Adatkezelő számos BI-szolgáltatást támogat, így használhatja azt, amelyik leginkább megfelel a forgatókönyvnek és a költségvetésnek.

## <a name="kusto-query-language-visualizations"></a>Kusto lekérdezés nyelvi vizualizációi

A Kusto lekérdezési nyelve [`render operator`](/azure/kusto/query/renderoperator) különböző vizualizációkat kínál, például táblázatokat, kördiagramokat és sávdiagramok a lekérdezési eredmények ábrázolásához. A lekérdezési vizualizációk hasznosak lehetnek a anomáliák észlelése és előrejelzése, a gépi tanulás és egyebek terén.

## <a name="power-bi"></a>Power BI

Az Azure Adatkezelő különböző módszerekkel képes csatlakozni a [Power BIhoz](https://powerbi.microsoft.com) : 

  * [Beépített natív Power BI-összekötő](/azure/data-explorer/power-bi-connector)

  * [Lekérdezés importálása az Azure Adatkezelőból a Power BIba](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL-lekérdezés](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Az Azure Adatkezelő lehetővé teszi a [Microsoft Excelhez](https://products.office.com/excel) való csatlakozást a [beépített natív Excel-összekötővel](excel-connector.md), vagy [importálhat egy lekérdezést](excel-blank-query.md) az Azure Adatkezelőból az Excelbe.

## <a name="grafana"></a>Grafana

A [Grafana](https://grafana.com) egy Azure adatkezelő beépülő modult biztosít, amely lehetővé teszi az azure-adatkezelő adatainak megjelenítését. Az [Azure adatkezelő-t a Grafana adatforrásaként állíthatja be, majd megjelenítheti az adatmegjelenítést](/azure/data-explorer/grafana). 

## <a name="odbc-connector"></a>ODBC-összekötő

Az Azure Adatkezelő egy [nyílt adatbázis-kapcsolati (ODBC) összekötőt](connect-odbc.md) biztosít, így bármely, az ODBC-t támogató alkalmazás csatlakozhat az Azure Adatkezelőhoz.

## <a name="tableau"></a>Tableau

Az Azure Adatkezelő lehetővé teszi a [tablóhoz](https://www.tableau.com) való csatlakozást az [ODBC-összekötő](/azure/data-explorer/connect-odbc) használatával, majd [az adatmegjelenítést a tablóban](tableau.md).

## <a name="qlik"></a>Qlik

Az Azure Adatkezelő lehetővé teszi a [Qlik](https://www.qlik.com) -hez való csatlakozást az [ODBC-összekötővel](/azure/data-explorer/connect-odbc) , majd a Qlik értelem-irányítópultok létrehozását és az adatgyűjtés megjelenítését. Az alábbi videó segítségével megismerheti az Azure Adatkezelő-beli Qlik-val való megjelenítését. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Az Azure Adatkezelő lehetőséget biztosít a [Sisense](https://www.sisense.com) való csatlakozásra a JDBC-összekötő használatával. Az [Azure adatkezelő-t a Sisense adatforrásaként állíthatja be, majd megjelenítheti az adatmegjelenítést](/azure/data-explorer/sisense).

## <a name="redash"></a>Redash

A [redash](https://redash.io/) használatával irányítópultokat hozhat létre, és megjelenítheti az adatmegjelenítést. [Állítsa be az Azure adatkezelőt adatforrásként a redash számára, majd jelenítse meg az adatmegjelenítést](/azure/data-explorer/redash).