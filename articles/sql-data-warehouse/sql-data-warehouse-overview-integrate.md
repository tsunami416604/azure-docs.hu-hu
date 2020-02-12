---
title: Integrált megoldások készítése
description: Az SQL Analytics használatával kiépített adattárházba integrált megoldási eszközök és partnerek.
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
ms.openlocfilehash: 8b38b50401c50ecfb9cd37c53ad013ca05e126c0
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153298"
---
# <a name="integrate-other-services-with-a-sql-analytics-data-warehouse"></a>Más szolgáltatások integrálása SQL Analytics-adattárházba 
Az Azure szinapszis Analyticsen belüli SQL Analytics funkció lehetővé teszi a felhasználók számára az Azure számos más szolgáltatásának integrálását. Az SQL Analytics használatával létrehozhat egy adattárházat az SQL-készlet erőforrásán keresztül, amely több további szolgáltatást is használhat, amelyek némelyike többek között a következőkre használható:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Az Azure-beli integrációs szolgáltatásokkal kapcsolatos további információkért tekintse át az [integrációs partnerek](sql-data-warehouse-partner-data-integration.md)című cikket.

## <a name="power-bi"></a>Power BI
Az Power BI integráció lehetővé teszi az adatraktár számítási teljesítményének összevonását a Power BI dinamikus jelentéskészítésével és vizualizációval. Power BI integráció jelenleg a következőket tartalmazza:

* **Közvetlen**kapcsolat: az SQL-készlettel kiépített adattárházak logikai pushdown való fejlettebb kapcsolata. A pushdown gyorsabb elemzést tesz lehetővé nagyobb méretekben.
* **Megnyitás a Power BIban**: a "Megnyitás Power bi" gomb a példányok információit átadja a Power BInak a kapcsolódás egyszerűsített módjaként.

További információ: Integration [with Power bi](sql-data-warehouse-get-started-visualize-with-power-bi.md), vagy a [Power bi dokumentációja](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory a felhasználók számára felügyelt platformot biztosít összetett kinyerési és betöltési folyamatok létrehozásához. Az SQL Pool Azure Data Factory-integrációja a következőket tartalmazza:

* **Tárolt eljárások**: a tárolt eljárások végrehajtásának összehangolása.
* **Másolás**: használja az ADF-et az adatgyűjtés SQL-készletbe való áthelyezéséhez. Ez a művelet az ADF standard adatáthelyezési mechanizmusát vagy a borítók alá tartozó albaset is használhatja. 

További információ: [integráció a Azure Data Factorysal](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning
A Azure Machine Learning egy teljes körűen felügyelt elemzési szolgáltatás, amely lehetővé teszi, hogy bonyolult modelleket hozzon létre a prediktív eszközök nagy készletével. Az SQL-készlet ezen modellek forrásaként és célként is támogatott, és a következő funkciókkal rendelkezik:

* **Olvasási információk:** A T-SQL-T az SQL-készleten keresztül méretezheti a méretezési modelleken.
* **Írási érték:** A változások bármely modellből visszakerülnek az SQL-készletbe.

További információ: [integráció a Azure Machine Learningsal](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
A Azure Stream Analytics egy összetett, teljes körűen felügyelt infrastruktúra az Azure Event hub-ból generált események feldolgozásához és felhasználásához.  Az SQL-készlettel való integráció lehetővé teszi, hogy a folyamatos átvitelt és a fejlettebb elemzést is lehetővé tévő, a kapcsolódó adattovábbítási szolgáltatásokkal együtt hatékonyan dolgozza fel és tárolja  

* **Feladatok kimenete:** Kimenet küldése Stream Analytics-feladatokból közvetlenül az SQL-készletbe.

További információ: [integráció a Azure stream Analyticssal](sql-data-warehouse-integrate-azure-stream-analytics.md).


