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
ms.date: 05/06/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: cb2da3515cfe5fd460e16b019d3738f4a9a050bb
ms.contentlocale: hu-hu
ms.lasthandoff: 05/08/2017


---
# <a name="overview-of-microsoft-azure-data-lake-analytics"></a>A Microsoft Azure Data Lake Analytics áttekintése
## <a name="what-is-azure-data-lake-analytics"></a>Mi az az Azure Data Lake Analytics?
Az Azure Data Lake Analytics egy igény szerinti elemzési feladatokat végző szolgáltatás a big data-elemzés egyszerűsítésére. Ez a szolgáltatás lehetővé teszi, hogy a feladatok írására, futtatására és kezelésére összpontosítson, és nem kell az elosztott infrastruktúra működtetésével bajlódnia. Az üzembe helyezés, a konfigurálás és a hardver beállítása helyett lekérdezéseket írhat az adatátalakítás és az értékes információk kinyerése érdekében. Az elemző szolgáltatás bármekkora munkát képes elvégezni, csak be kell állítania, hogy mekkora teljesítményre van szüksége. Költséghatékony megoldás, hiszen csak az elvégzett munkáért kell fizetni. Az elemző szolgáltatás az Azure Active Directoryt is támogatja, ami lehetővé teszi a helyszíni identitáskezelő rendszerbe integrált hozzáférések és szerepkörök kezelését. A szolgáltatás tartalmazza a U-SQL lekérdezésnyelvet is, amely az SQL előnyeit egyesíti a felhasználói kód kifejezőerejével. A U-SQL skálázható elosztott futtatókörnyezete segítségével hatékonyan elemezheti az adattárban és az Azure, az Azure SQL Database és az Azure SQL Data Warehouse szolgáltatásban működő SQL Server kiszolgálókon található adatokat.

## <a name="key-capabilities"></a>Főbb képességek
* **Dinamikus méretezés**
  
    A Data Lake Analytics a felhőbeli méretezéshez és teljesítményhez lett tervezve.  Dinamikusan kiosztja az erőforrásokat, és lehetővé teszi az adatok terabájtjain vagy akár exabájtjain való elemzést. Amikor a feladat kész, automatikusan lecsökkenti az erőforrás-használatot, és csak a felhasznált számítási kapacitásért kell fizetnie. Ha növeli vagy csökkenti a tárolt adatok méretét, vagy a felhasznált számítási teljesítményt, nem kell újraírnia a programot. Kizárólag az üzleti logikára összpontosíthat, és nem kell törődnie a nagy adatkészletek feldolgozásának vagy tárolásának módjával.
* **Gyorsabb fejlesztés, intelligensebb hibakeresés és optimalizálás a már ismerős eszközökkel**
  
    A Data Lake Analytics átfogóan integrálja a Visual Studiót, így ismerős eszközökkel futtathatja a programját, végezhet hibakeresést vagy finomhangolást. Az U-SQL feladatok vizualizációja révén láthatja, hogyan fut a program nagyobb méretek esetén, így egyszerűbb azonosítani a teljesítménybeli szűk keresztmetszeteket, valamint optimalizálni a költségeket.
* **U-SQL: egyszerű és ismerős, sokoldalú és bővíthető**
  
    A Data Lake Analytics tartalmazza a U-SQL lekérdezésnyelvet, amely kibővíti az SQL ismerős, egyszerű, deklaratív természetét a C# kifejezőerejével. A U-SQL nyelv alapja ugyanaz az elosztott futtatási környezet, amely a big data-rendszereket működteti a Microsoft vállalatnál. Mostantól SQL- és .NET-fejlesztők milliói dolgozhatják fel az adataikat a már meglévő ismereteik segítségével.
* **Zökkenőmentes integráció az informatikai befektetésekkel**
  
    A Data Lake Analytics képes a meglévő informatikai befektetések használatára az identitások, a kezelés, a biztonság és az adatraktározás terén. Ez a megközelítés leegyszerűsíti az adatszabályozást, és megkönnyíti az aktuális adatalkalmazások bővítését. A Data Lake Analytics integrálja az Active Directoryt a felhasználók kezeléséhez és a jogosultságokhoz, és beépített figyelést és felülvizsgálatot is biztosít.
* **Megfizethető és költséghatékony**
  
    A Data Lake Analytics egy költséghatékony megoldás big data-számítási feladatok futtatásához. Az adatok feldolgozásakor feladatonként kell fizetnie. Nem szükséges hardver-, licenc- vagy szolgáltatásspecifikus támogatási megállapodásokat kötnie. A rendszer automatikusan méretezi magát a feladat indulásakor és befejezésekor, azaz sosem kell a szükségesnél többért fizetnie.
* **Az összes Azure Data-szolgáltatással együttműködik**
  
    A Data Lake Analytics az Azure Data Lake szolgáltatással való együttműködésre lett optimalizálva, így a legmagasabb szintű teljesítményt, adatátvitelt és párhuzamos folyamatkezelést biztosítja a big data-számítási feladatokhoz.  A Data Lake Analytics az Azure Blob Storage és az Azure SQL Database szolgáltatással is képes együttműködni.

## <a name="see-also"></a>Lásd még:
* Bevezetés
  
  * [A Data Lake Analytics használatának első lépései az Azure Portallal](data-lake-analytics-get-started-portal.md)
  * [A Data Lake Analytics használatának első lépései az Azure PowerShell-lel](data-lake-analytics-get-started-powershell.md)
  * [A Data Lake Analytics használatának első lépései az Azure .NET SDK-val](data-lake-analytics-get-started-net-sdk.md)
  * [U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)
  * [Ismerkedés az Azure Data Lake Analytics U-SQL nyelvével](data-lake-analytics-u-sql-get-started.md)

* Kezelés
  
  * [Az Azure Data Lake Analytics kezelése az Azure Portal használatával](data-lake-analytics-manage-use-portal.md)
  * [Az Azure Data Lake Analytics kezelése az Azure PowerShell-lel](data-lake-analytics-manage-use-powershell.md)
  * [Az Azure Data Lake Analytics-feladatok figyelése és hibaelhárítása az Azure Portal használatával](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

* Ossza meg velünk a véleményét
  
  * [Funkciókérés küldése](http://aka.ms/adlafeedback)
  * [Segítség kérése az MSDN-fórumokban](http://aka.ms/adlaforums)


