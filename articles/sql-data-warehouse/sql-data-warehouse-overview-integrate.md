---
title: Integrált megoldások készítése
description: Eszközök és partnerek Azure SQL Data Warehouse-nal integrált megoldásokkal.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 6e159e0e254ae8b2515515dfaeb2c514e0f25e0b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685641"
---
# <a name="integrate-other-services-with-sql-data-warehouse"></a>Más szolgáltatások integrálása SQL Data Warehouse
Az alapvető funkciói mellett SQL Data Warehouse lehetővé teszi a felhasználók számára az Azure számos más szolgáltatásának integrálását. Néhány ilyen szolgáltatás a következőket tartalmazza:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

SQL Data Warehouse továbbra is integrálva van több szolgáltatással az Azure-ban és több [integrációs partneren](sql-data-warehouse-partner-data-integration.md)keresztül.

## <a name="power-bi"></a>Power BI
A Power BI integráció lehetővé teszi a SQL Data Warehouse számítási teljesítményének összevonását a Power BI dinamikus jelentéskészítésével és vizualizációval. Power BI integráció jelenleg a következőket tartalmazza:

* **Közvetlen**kapcsolat: egy fejlettebb kapcsolat a logikai pushdown SQL Data Warehouse. A pushdown gyorsabb elemzést tesz lehetővé nagyobb méretekben.
* **Megnyitás a Power BIban**: a "Megnyitás Power bi" gomb a példányok információit átadja a Power BInak a kapcsolódás egyszerűsített módjaként.

További információ: Integration [with Power bi](sql-data-warehouse-get-started-visualize-with-power-bi.md), vagy a [Power bi dokumentációja](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory a felhasználók számára felügyelt platformot biztosít összetett kinyerési és betöltési folyamatok létrehozásához. A SQL Data Warehouse Azure Data Factory integrációja a következőket tartalmazza:

* **Tárolt eljárások**: összehangolja a tárolt eljárások végrehajtását SQL Data Warehouseon.
* **Másolás**: használja az ADF-et az adatSQL Data Warehouseba való áthelyezéséhez. Ez a művelet az ADF standard adatáthelyezési mechanizmusát vagy a borítók alá tartozó albaset is használhatja. 

További információ: [integráció a Azure Data Factorysal](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning
A Azure Machine Learning egy teljes körűen felügyelt elemzési szolgáltatás, amely lehetővé teszi, hogy bonyolult modelleket hozzon létre a prediktív eszközök nagy készletével. A SQL Data Warehouse a modellek forrásaként és célként is támogatott a következő funkciókkal:

* **Olvasási információk:** A méreteket a T-SQL-lel SQL Data Warehouse.
* **Írási érték:** A változások bármely modellből visszakerülnek a SQL Data Warehousera.

További információ: [integráció a Azure Machine Learningsal](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
A Azure Stream Analytics egy összetett, teljes körűen felügyelt infrastruktúra az Azure Event hub-ból generált események feldolgozásához és felhasználásához.  A SQL Data Warehouse való integráció lehetővé teszi, hogy a folyamatos átvitelt és a fejlettebb elemzést lehetővé tévő kapcsolatok mellett a streaming-adattovábbítás hatékonyan feldolgozható és tárolható legyen.  

* **Feladatok kimenete:** Kimenet küldése Stream Analytics feladatokból közvetlenül a SQL Data Warehouse.

További információ: [integráció a Azure stream Analyticssal](sql-data-warehouse-integrate-azure-stream-analytics.md).


