---
title: Az SQL Data Warehouse szolgáltatással integrált megoldásokat hozhat létre |} A Microsoft Docs
description: 'Az eszközök és az SQL Data Warehouse szolgáltatással integrált megoldásokat kínáló partnerek. '
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: d2fcdd4aa28a7d3b1a8e50629e5be1d88046a12b
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621209"
---
# <a name="integrate-other-services-with-sql-data-warehouse"></a>Más szolgáltatások integrálása az SQL Data warehouse-bA
A fő funkciói mellett az SQL Data Warehouse lehetővé teszi a felhasználók számos, az Azure-ban a más szolgáltatások integrálása. Ezek a szolgáltatások a következők:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Az SQL Data Warehouse továbbra is fennáll, további szolgáltatások integrálása az Azure-ban, és további [az integrációs partnerek](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI
Power BI-integráció lehetővé teszi az SQL Data Warehouse a számítási teljesítmény kombinálva dinamikus jelentéskészítő és vizualizációs a Power bi. A Power BI integrációja jelenleg tartalmazza:

* **Közvetlen csatlakozás**: Az SQL Data Warehouse elleni logikai legördülő lista speciális kapcsolatot. Legördülő lista gyorsabb elemzés által nagyobb méretekben biztosít.
* **Nyissa meg a Power bi-ban**: A "Megnyitás Power BI-ban" gomb példányadatai csatlakoztathat így egyszerűbb a Power bi-bA továbbítja.

További információkért lásd: [integráció a Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md), vagy a [Power BI dokumentációja](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory
Az Azure Data Factory egy felügyelt platform összetett kinyerési létrehozására és betöltésére folyamatok biztosítja a felhasználók számára. Az SQL Data Warehouse-integráció az Azure Data Factory tartalmazza:

* **Tárolt eljárások**: Koordinálhatja az SQL Data Warehouse a tárolt eljárások végrehajtását.
* **Másolás**: Az ADF használata adatok áthelyezéséhez az SQL Data Warehouse-bA. Ez a művelet az ADF standard szintű adatátviteli mechanizmus vagy a PolyBase a háttérben. 

További információkért lásd: [integrálás az Azure Data Factoryvel](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Az Azure Machine Learning egy teljes körűen felügyelt elemzési szolgáltatás, amely lehetővé teszi, hogy bonyolult modellek prediktív eszközök használatával hozzon létre egy. Az SQL Data Warehouse a forrás- és a cél ezen modellek is támogatott, a következő funkciók:

* **Olvassa el az adatokat:** A meghajtó modellek ipari méretekben, szemben az SQL Data Warehouse a T-SQL használatával.
* **Adatok írása:** Minden modell a változtatások véglegesítése a vissza az SQL Data Warehouse.

További információkért lásd: [integrálás az Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Az Azure Stream Analytics egy összetett, teljes körűen felügyelt infrastruktúra feldolgozás és az Azure Event Hubs generált események adatainak felhasználása.  Integráció az SQL Data Warehouse lehetővé teszi a streamelési adatok hatékony feldolgozni és tárolt relációs adatok mélyebb, fejlett elemzési engedélyezése mellett.  

* **Feladat kimenete:** A Stream Analytics-feladatok kimeneti küldjön közvetlenül az SQL Data warehouse-bA.

További információkért lásd: [integrálás az Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).


