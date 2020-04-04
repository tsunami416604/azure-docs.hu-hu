---
title: Integrált megoldások készítése
description: Megoldáseszközök és partnerek, amelyek integrálhatók a Synapse SQL-készlettel.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2f6e091b6e0285bea5fef9e4d0be40faec936c6b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633144"
---
# <a name="integrate-other-services-with-a-sql-analytics-data-warehouse"></a>Egyéb szolgáltatások integrálása az SQL Analytics adattárházával

Az Azure Synapse Analytics-en belüli SQL Analytics-funkció lehetővé teszi a felhasználók számára, hogy integrálódjanak számos más azure-beli szolgáltatással. Az SQL Analytics használatával adattárházat hozhat létre az SQL Pool-erőforráson keresztül, amely ezután számos további szolgáltatást használhat, amelyek közül néhány a következőket tartalmazza:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Az Azure-beli integrációs szolgáltatásokkal kapcsolatos további információkért tekintse meg az [integrációs partnerek](sql-data-warehouse-partner-data-integration.md)ről szóló cikket.

## <a name="power-bi"></a>Power BI

A Power BI-integráció lehetővé teszi az adattárház számítási teljesítményének és a Power BI dinamikus jelentéskészítési és vizualizációs kombinálásának kombinálását. A Power BI-integráció jelenleg a következőket tartalmazza:

* **Direct Connect:** Egy fejlettebb kapcsolat logikai leküldéses egy SQL-készlet használatával kiépített adattárház hoz létre. A lenyomás gyorsabb elemzést tesz lehetővé nagyobb léptékben.
* **Megnyitás a Power BI-ban**: A "Megnyitás a Power BI-ban" gomb a power BI-nak továbbítja a példányadatokat a csatlakozás egyszerűbb módja érdekében.

További információt az [Integráció a Power BI-val](sql-data-warehouse-get-started-visualize-with-power-bi.md)vagy a Power BI [dokumentációjában](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/)talál.

## <a name="azure-data-factory"></a>Azure Data Factory

Az Azure Data Factory egy felügyelt platformot biztosít a felhasználóknak összetett kinyerési és betöltési folyamatok létrehozásához. Az Azure Data Factory-val való SQL-készletintegráció a következőket tartalmazza:

* **Tárolt eljárások**: A tárolt eljárások végrehajtásának koordinálása.
* **Másolás:** Az ADF segítségével helyezze át az adatokat az SQL-készletbe. Ez a művelet használhatja az ADF szabványos adatmozgatási mechanizmusát vagy a PolyBase-t a fedelek alatt.

További információ: [Integrálás az Azure Data Factory szolgáltatással](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)című témakörben talál.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Az Azure Machine Learning egy teljes körűen felügyelt elemzési szolgáltatás, amely lehetővé teszi bonyolult modellek létrehozását a prediktív eszközök nagy készletével. Az SQL-készlet a modellek forrása és célja egyaránt támogatott, és a következő funkciókkal rendelkezik:

* **Adatok olvasása:** A T-SQL-t az SQL-készlethez használva nagy méretekben hajtsa elő a modelleket.
* **Adatok írása:** Véglegesítse a módosításokat bármely modellről az SQL-készletbe.

További információ: [Integrálható az Azure Machine Learningszolgáltatással című témakörben.](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

Az Azure Stream Analytics egy összetett, teljes körűen felügyelt infrastruktúra az Azure Event Hubból létrehozott eseményadatok feldolgozásához és feldolgozásához.  Az SQL-készlettel való integráció lehetővé teszi az adatfolyam-adatfolyamadatok hatékony feldolgozását és tárolását a relációs adatok mellett, amelyek lehetővé teszik a mélyebb, fejlettebb elemzést.  

* **Munkahely kimenete:** A Stream Analytics-feladatok ból közvetlenül az SQL-készletbe küldhet kimenetet.

További információ: [Integrálás az Azure Stream Analytics szolgáltatással](sql-data-warehouse-integrate-azure-stream-analytics.md)című témakörben talál.
