---
title: Az SQL Data Warehouse szolgáltatással integrált megoldásokat hozhat létre |} A Microsoft Docs
description: 'Az eszközök és az SQL Data Warehouse szolgáltatással integrált megoldásokat kínáló partnerek. '
services: sql-data-warehouse
author: kavithaj
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 5b302be582bd22a7b38601c90f5fe475062afb26
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51239671"
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

* **Közvetlen csatlakozás**: egy speciális logikai legördülő lista szemben az SQL Data Warehouse-kapcsolatot. Legördülő lista gyorsabb elemzés által nagyobb méretekben biztosít.
* **Nyissa meg a Power bi-ban**: A "Megnyitás Power BI-ban" gomb példány információt továbbítja a Power bi-hoz való csatlakozáshoz simplifed módját.

További információkért lásd: [integráció a Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md), vagy a [Power BI dokumentációja](https://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx).

## <a name="azure-data-factory"></a>Azure Data Factory
Az Azure Data Factory egy felügyelt platform összetett kinyerési létrehozására és betöltésére folyamatok biztosítja a felhasználók számára. Az SQL Data Warehouse-integráció az Azure Data Factory tartalmazza:

* **Tárolt eljárások**: koordinálhatja az SQL Data Warehouse a tárolt eljárások végrehajtását.
* **Másolás**: adatok áthelyezése SQL Data Warehouse-bA az ADF. Ez a művelet az ADF standard szintű adatátviteli mechanizmus vagy a PolyBase a háttérben. 

További információkért lásd: [integrálás az Azure Data Factoryvel](sql-data-warehouse-get-started-visualize-with-power-bi.md).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Az Azure Machine Learning egy teljes körűen felügyelt elemzési szolgáltatás, amely lehetővé teszi, hogy bonyolult modellek prediktív eszközök használatával hozzon létre egy. Az SQL Data Warehouse a forrás- és a cél ezen modellek is támogatott, a következő funkciók:

* **Adatok olvasása:** modellek meghajtó ipari méretekben, szemben az SQL Data Warehouse a T-SQL használatával.
* **Adatok írása:** véglegesítési visszavált bármely modellből az SQL Data warehouse-bA.

További információkért lásd: [integrálás az Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Az Azure Stream Analytics egy összetett, teljes körűen felügyelt infrastruktúra feldolgozás és az Azure Event Hubs generált események adatainak felhasználása.  Integráció az SQL Data Warehouse lehetővé teszi a streamelési adatok hatékony feldolgozni és tárolt relációs adatok mélyebb, fejlett elemzési engedélyezése mellett.  

* **Feladat kimenetének:** elküldeni a kimeneti Stream Analytics-feladatok közvetlenül az SQL Data warehouse-bA.

További információkért lásd: [integrálás az Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).

## <a name="next-steps"></a>További lépések
Az Azure SQL Database integrálásához tekintse meg [konfigurálása az SQL Database rugalmas lekérdezése](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md)

