---
title: Az SQL Data Warehouse szolgáltatással integrált megoldások |} Microsoft Docs
description: 'Eszközök és a partnerek, amelyekbe beépül az SQL Data Warehouse-megoldás. '
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: f198a99fc03a079be77c7f8167580bb7b758579e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31599832"
---
# <a name="integrate-other-services-with-sql-data-warehouse"></a>Más szolgáltatásokkal integrálható az SQL Data Warehouse szolgáltatással
Az alapvető funkciók mellett az SQL Data Warehouse lehetővé teszi a felhasználók integrálása számos egyéb szolgáltatásokat az Azure-ban. Ezek a szolgáltatások többek között:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Az SQL Data Warehouse továbbra is fennáll, további szolgáltatások integrálása az Azure és egyebek között [integrációs partnerek](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI
A Power BI-integráció lehetővé teszi a számítási teljesítményt az SQL Data Warehouse dinamikus jelentéskészítés és a Power bi-ban a képi megjelenítés kombinálva. A Power BI integrációs jelenleg tartalmazza:

* **Közvetlen csatlakozás**: egy speciális logikai leküldéses közzététele elleni SQL Data Warehouse-kapcsolatot. Leküldéses közzététele gyorsabb elemzése nagyobb méretű biztosít.
* **Nyissa meg a Power BI**: A "Megnyitás Power BI-ban" gombra simplifed úgy csatlakozni a Power bi-bA továbbítja a példány adatait.

További információkért lásd: [integráció a Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md), vagy a [Power BI-dokumentáció](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx).

## <a name="azure-data-factory"></a>Azure Data Factory
Az Azure Data Factory lehetőséget ad a felhasználók egy felügyelt platformon összetett kivonat létrehozására és betöltésére folyamatok. Az SQL Data Warehouse-integráció az Azure Data Factoryvel tartalmazza:

* **Tárolt eljárások**: Levezényelni a SQL Data Warehouse tárolt eljárások végrehajtása.
* **Másolás**: használata ADF áthelyezni az adatokat az SQL Data Warehouse. Ez a művelet a ADF szabványos adatok mechanizmusát vagy a PolyBase a színfalak. 

További információkért lásd: [integráció az Azure Data Factoryvel](sql-data-warehouse-get-started-visualize-with-power-bi.md).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Az Azure Machine Learning egy teljes körűen felügyelt analytics szolgáltatás, amely lehetővé teszi nagy prediktív eszközök használatával bonyolult modellek létrehozásához. Az SQL Data Warehouse az alábbi funkciókat, a forrás- és a célkiszolgálón, ezek a modellek használata támogatott:

* **Adatok olvasása:** modellek meghajtó léptékű T-SQL használatával az SQL Data Warehouse ellen.
* **Adatok írása:** véglegesítési visszavált a modellből az SQL Data Warehouse.

További információkért lásd: [integráció az Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Az Azure Stream Analytics egy összetett, teljes körűen felügyelt infrastruktúra dolgoz fel, és az Azure Event Hubs alapján generált események adatainak felhasználása.  Az SQL Data Warehouse integrációja lehetővé teszi, hogy az adatfolyamként történő hatékony feldolgozása és relációs adatok mélyebb, speciális elemzési engedélyezése mellett tárolt adatok.  

* **Job Output:** kimenetként a Stream Analytics-feladatok közvetlenül az SQL Data Warehouse.

További információkért lásd: [integráció az Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).

## <a name="next-steps"></a>További lépések
Az Azure SQL Database integrálásához tekintse meg [rugalmas lekérdezési SQL-adatbázis beállítása](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md)

