---
title: "Az SQL Data Warehouse szolgáltatással integrált megoldások |} Microsoft Docs"
description: "Eszközök és a partnerek, amelyekbe beépül az SQL Data Warehouse-megoldás. "
services: sql-data-warehouse
documentationcenter: NA
author: mlee3gsd
manager: jhubbard
editor: 
ms.assetid: e2dc8f3f-10e3-4589-a4e2-50c67dfcf67f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: martinle;barbkess
ms.openlocfilehash: d407c29f99fd7537590ec787febd84a9e3f4f353
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="leverage-other-services-with-sql-data-warehouse"></a>Használja ki az egyéb szolgáltatásokat az SQL Data Warehouse szolgáltatással
Az alapvető funkciók mellett az SQL Data Warehouse lehetővé teszi a felhasználók számos egyéb szolgáltatásokat az Azure-ban mellett azt.  Pontosabban jelenleg a rendszer átirányította mélyen integrálása a következő lépéseket:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Jelenleg dolgozunk, az Azure-ökoszisztéma között további szolgáltatások csatlakozhatnak.

## <a name="power-bi"></a>Power BI
A Power BI-integráció lehetővé teszi, hogy kihasználja az SQL Data Warehouse a dinamikus jelentéskészítéssel a számítási teljesítmény és a Power bi képi megjelenítések. A Power BI integrációs jelenleg tartalmazza:

* **Közvetlen csatlakozás**: egy speciális logikai leküldéses közzététele elleni SQL Data Warehouse-kapcsolatot.  Magasabb szinten gyorsabb elemzése biztosít.
* **Nyissa meg a Power BI**: A "Megnyitás Power BI-ban" gombra példány információt átadja a Power BI lehetővé teszi a zökkenőmentesebb kapcsolatot.

Lásd: [integráció a Power BI](sql-data-warehouse-integrate-power-bi.md) vagy a [Power BI-dokumentáció](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx) további információt.

## <a name="azure-data-factory"></a>Azure Data Factory
Az Azure Data Factory lehetőséget ad a felhasználók egy felügyelt platformon összetett kivonat-betöltési folyamatok létrehozásához.  Az SQL Data Warehouse-integráció az Azure Data Factoryvel az alábbiakat tartalmazza:

* **Tárolt eljárások**: Levezényelni a SQL Data Warehouse tárolt eljárások végrehajtása.
* **Másolás**: használata ADF áthelyezni az adatokat az SQL Data Warehouse.  Ez a művelet a ADF szabványos adatok mechanizmusát vagy a PolyBase a színfalak. 

Lásd: [integráció az Azure Data Factoryvel](sql-data-warehouse-integrate-azure-data-factory.md) vagy a [Azure Data Factory dokumentáció](https://azure.microsoft.com/documentation/services/data-factory/) további információt.

## <a name="azure-machine-learning"></a>Azure Machine Learning
Az Azure Machine Learning egy teljes körűen felügyelt analytics szolgáltatás, amely lehetővé teszi a felhasználók számára, hogy bonyolult modellek prediktív eszközök számos kihasználva.  Az SQL Data Warehouse az alábbi funkciókat, a forrás- és a célkiszolgálón, ezek a modellek használata támogatott:

* **Adatok olvasása:** modellek meghajtó léptékű T-SQL használatával az SQL Data Warehouse ellen.
* **Adatok írása:** véglegesítési visszavált a modellből az SQL Data Warehouse.

Lásd: [integráció az Azure Machine Learning](sql-data-warehouse-integrate-azure-machine-learning.md) vagy a [Azure Machine Learning dokumentációs](https://azure.microsoft.com/services/machine-learning/) további információt.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Az Azure Stream Analytics egy összetett, teljes körűen felügyelt infrastruktúra dolgoz fel, és az Azure Event Hubs alapján generált események adatainak felhasználása.  Az SQL Data Warehouse integrációja lehetővé teszi, hogy az adatfolyamként történő hatékony feldolgozása és relációs adatok mélyebb, speciális elemzési engedélyezése mellett tárolt adatok.  

* **Job Output:** kimenetként a Stream Analytics-feladatok közvetlenül az SQL Data Warehouse.

Lásd: [integráció az Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md) vagy a [Azure Stream Analytics-dokumentáció](https://azure.microsoft.com/documentation/services/stream-analytics/) további információt.

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop/

[Azure Data Factory]: sql-data-warehouse-integrate-azure-data-factory.md
[Azure Machine Learning]: sql-data-warehouse-integrate-azure-machine-learning.md
[Azure Stream Analytics]: sql-data-warehouse-integrate-azure-stream-analytics.md
[Power BI]: sql-data-warehouse-integrate-power-bi.md
[Partners]: sql-data-warehouse-partner-business-intelligence.md

<!--MSDN references-->

<!--Other Web references-->
