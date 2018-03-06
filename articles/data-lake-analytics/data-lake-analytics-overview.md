---
title: "A Microsoft Azure Data Lake Analytics áttekintése | Microsoft Docs"
description: "A Data Lake Analytics egy Azure Big Data-szolgáltatás, amelynek segítségével javíthatja az üzletvitelt a felhőbeli adatokból nyert információk révén, függetlenül az adatok méretétől vagy pontos helyétől."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 1e1d443a-48a2-47fb-bc00-bf88274222de
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/23/2017
ms.author: saveenr
ms.openlocfilehash: d327a3c28e928550b361c07569df74060cfcac0d
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="overview-of-microsoft-azure-data-lake-analytics"></a>A Microsoft Azure Data Lake Analytics áttekintése
## <a name="what-is-azure-data-lake-analytics"></a>Mi az az Azure Data Lake Analytics?
Az Azure Data Lake Analytics egy igény szerinti elemzési feladatokat végző szolgáltatás, amely leegyszerűsíti a big data-elemzéseket. Az üzembe helyezés, a konfigurálás és a hardver beállítása helyett lekérdezéseket írhat az adatátalakítás és az értékes információk kinyerése érdekében. Az elemző szolgáltatás bármekkora munkát képes elvégezni, csak be kell állítania, hogy mekkora teljesítményre van szüksége. Költséghatékony megoldás, hiszen csak az elvégzett munkáért kell fizetni. Az elemzési szolgáltatás az U-SQL nyelvet is támogatja, amely egyesíti az SQL és az imperatív kódok előnyeit. Az U-SQL lehetővé teszi a Data Lake Store, az Azure-beli SQL Server, az Azure SQL Database és az Azure SQL Data Warehouse adatainak elemzését.

## <a name="key-capabilities"></a>Főbb képességek
* **Dinamikus méretezés**
  
    A Data Lake Analytics a felhőbeli méretezéshez és teljesítményhez lett tervezve.  Dinamikusan kiosztja az erőforrásokat, és lehetővé teszi az adatok terabájtjain vagy akár exabájtjain való elemzést. Amikor a feladat kész, automatikusan lecsökkenti az erőforrás-használatot, és csak a felhasznált számítási kapacitásért kell fizetnie. Nem kell újraírnia a programot, ha növeli vagy csökkenti a tárolt adatmennyiséget vagy a felhasznált számítási erőforrások számát. Kizárólag az üzleti logikára összpontosíthat, és nem kell törődnie a nagy adatkészletek feldolgozásának vagy tárolásának módjával.
* **Gyorsabb fejlesztés, intelligensebb hibakeresés és optimalizálás a már ismerős eszközökkel**
  
    A Data Lake Analytics átfogóan integrálja a Visual Studiót, így a már ismert eszközökkel futtathatja a kódot, vagy végezhet rajta hibakeresést és finomhangolást. Az U-SQL feladatok vizualizációja révén láthatja, hogyan fut a program nagyobb méretek esetén, így egyszerűbb azonosítani a teljesítménybeli szűk keresztmetszeteket, valamint optimalizálni a költségeket.
* **U-SQL: egyszerű és ismerős, sokoldalú és bővíthető**
  
    A Data Lake Analytics tartalmazza a U-SQL lekérdezésnyelvet, amely kibővíti az SQL ismerős, egyszerű, deklaratív természetét a C# kifejezőerejével. A U-SQL nyelv alapja ugyanaz az elosztott futtatási környezet, amely a big data-rendszereket működteti a Microsoft vállalatnál. Mostantól SQL- és .NET-fejlesztők milliói dolgozhatják fel az adataikat a már meglévő ismereteik segítségével.
* **Zökkenőmentes integráció az informatikai befektetésekkel**
  
    A Data Lake Analytics képes a meglévő informatikai befektetések használatára az identitások, a kezelés, a biztonság és az adatraktározás terén. Ez a megközelítés leegyszerűsíti az adatszabályozást, és megkönnyíti az aktuális adatalkalmazások bővítését. A Data Lake Analytics integrálja az Active Directoryt a felhasználók kezeléséhez és a jogosultságokhoz, és beépített figyelést és felülvizsgálatot is biztosít.

* **Megfizethető és költséghatékony**
  
    A Data Lake Analytics egy költséghatékony megoldás big data-számítási feladatok futtatásához. Az adatok feldolgozásakor feladatonként kell fizetnie. Nem szükséges hardver-, licenc- vagy szolgáltatásspecifikus támogatási megállapodásokat kötnie. Mivel a feladat indulásakor és befejezésekor a rendszer automatikusan méretezi magát, soha nem kell a szükségesnél többet fizetnie. [További tudnivalók a költségek szabályozásáról és a pénzmegtakarításról](https://1drv.ms/f/s!AvdZLquGMt47h213Hg3rhl-Tym1c).
    
* **Az összes Azure Data-szolgáltatással együttműködik**
  
    A Data Lake Analytics az Azure Data Lake szolgáltatással való együttműködésre lett optimalizálva, így a legmagasabb szintű teljesítményt, adatátvitelt és párhuzamos folyamatkezelést biztosítja a big data-számítási feladatokhoz.  A Data Lake Analytics képes együttműködni az Azure Blob Storage és az Azure SQL Database szolgáltatással is.

## <a name="next-steps"></a>További lépések
 
  * A Data Lake Analytics használatának első lépései – [Azure Portal](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [parancssori felület](data-lake-analytics-get-started-cli2.md)
  * Az Azure Data Lake Analytics kezelése – [Azure Portal](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) | [Azure .NET SDK](data-lake-analytics-manage-use-dotnet-sdk.md) | [Node.js](data-lake-analytics-manage-use-nodejs.md)
  * [A költségek szabályozása és pénz megtakarítása a Data Lake Analytics használatával](https://1drv.ms/f/s!AvdZLquGMt47h213Hg3rhl-Tym1c)
