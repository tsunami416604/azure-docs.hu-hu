<properties 
   pageTitle="A Microsoft Azure Data Lake Analytics áttekintése | Azure" 
   description="A Data Lake Analytics egy Azure Big Data-számítási szolgáltatás, amely segítségével javíthatja az üzletvitelt az adatokból a felhőben nyert információk révén, függetlenül attól, hogy hol tárolja az adatokat, és mekkora a méretük. A Data Lake Analytics ezt a legegyszerűbb, legjobban méretezhető és leggazdaságosabb módon teszi lehetővé. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/26/2016"
   ms.author="edmaca"/>

# A Microsoft Azure Data Lake Analytics áttekintése

## Mi az az Azure Data Lake Analytics?

Az Azure Data Lake Analytics egy új szolgáltatás, amely a big data-elemzések egyszerűsítését szolgálja. Ez a szolgáltatás lehetővé teszi, hogy a feladatok írására, futtatására és kezelésére összpontosítson, és nem kell az elosztott infrastruktúra működtetésével bajlódnia. Az üzembe helyezés, a konfigurálás és a hardver beállítása helyett lekérdezéseket írhat az adatátalakítás és az értékes információk kinyerése érdekében. Az analitikai szolgáltatás bármekkora munkát képes elvégezni, csak be kell állítania, hogy mekkora teljesítményre van szüksége. Költséghatékony megoldás, hiszen csak az elvégzett munkáért kell fizetni. Az analitikai szolgáltatás az Azure Active Directoryt is támogatja, ami lehetővé teszi a helyszíni identitáskezelő rendszerbe integrált hozzáférések és szerepkörök egyszerű kezelését. A szolgáltatás tartalmazza a U-SQL lekérdezésnyelvet is, amely az SQL előnyeit egyesíti a felhasználói kód kifejezőerejével. A U-SQL skálázható elosztott futtatókörnyezete segítségével hatékonyan elemezheti az adattárban és az Azure, az Azure SQL Database és az Azure SQL Data Warehouse szolgáltatásban működő SQL Server kiszolgálókon található adatokat.


## Főbb képességek

- **Dinamikus méretezés** 

    A Data Lake Analytics az alapoktól kezdve a felhőbeli méretezéshez és teljesítményhez lett tervezve.  Dinamikusan kiosztja az erőforrásokat, és lehetővé teszi az adatok terabájtjain vagy akár exabájtjain való elemzést. Amikor a feladat kész, automatikusan lecsökkenti az erőforrás-használatot, és csak a felhasznált számítási kapacitásért kell fizetnie. Ha növeli vagy csökkenti a tárolt adatok méretét, vagy a felhasznált számítási teljesítményt, nem kell újraírnia a programot. Így csak az üzleti logikára kell összpontosítania, és nem kell törődnie a nagy adatkészletek feldolgozásának vagy tárolásának módjával. 

- **Gyorsabb fejlesztés, intelligensebb hibakeresés és optimalizálás a már ismerős eszközökkel**

    A Data Lake Analytics átfogóan integrálja a Visual Studiót, így ismerős eszközökkel futtathatja a programját, végezhet hibakeresést vagy finomhangolást. Az U-SQL feladatok vizualizációja révén láthatja, hogyan fut a program nagyobb méretek esetén, így egyszerűbb azonosítani a teljesítménybeli szűk keresztmetszeteket, valamint optimalizálni a költségeket. 

- **U-SQL: egyszerű és ismerős, sokoldalú és bővíthető**

    A Data Lake Analytics tartalmazza a U-SQL lekérdezésnyelvet, amely kibővíti az SQL ismerős, egyszerű, deklaratív természetét a C# kifejezőerejével. A U-SQL nyelv alapja ugyanaz az elosztott futtatási környezet, amely a big data-rendszereket működteti a Microsoft vállalatnál. Mostantól SQL- és .NET-fejlesztők milliói dolgozhatják fel az adataikat a már meglévő ismereteik segítségével.

- **Zökkenőmentes integráció az informatikai befektetésekkel**

    A Data Lake Analytics képes a meglévő informatikai befektetések használatára az identitások, a kezelés, a biztonság és az adatraktározás terén. Ez leegyszerűsíti az adatszabályozást, és megkönnyíti az aktuális adatalkalmazások bővítését. A Data Lake Analytics integrálja az Active Directoryt a felhasználók kezeléséhez és a jogosultságokhoz, és beépített figyelést és felülvizsgálatot is biztosít.

- **Megfizethető és költséghatékony**

    A Data Lake Analytics egy költséghatékony megoldás big data-számítási feladatok futtatásához. Az adatok feldolgozásakor feladatonként kell fizetnie. Nem szükséges hardver-, licenc- vagy szolgáltatásspecifikus támogatási megállapodásokat kötnie. A rendszer automatikusan méretezi magát a feladat indulásakor és befejezésekor, azaz sosem kell a szükségesnél többért fizetnie. 

- **Az összes Azure Data-szolgáltatással együttműködik**

    A Data Lake Analytics számos Azure-adatforrással együttműködik. Ilyen például az Azure Blob Storage, az Azure SQL Database, és persze a Data Lake Analytics kifejezetten az Azure Data Lake Store-ral való használathoz lett optimalizálva, így a legmagasabb szintű teljesítményt, adatátvitelt és párhuzamos folyamatkezelést biztosítja a big data-számítási feladatokhoz.

## Lásd még:

- Első lépések
    - [A Data Lake Analytics használatának első lépései az Azure portállal](data-lake-analytics-get-started-portal.md)
    - [A Data Lake Analytics használatának első lépései az Azure PowerShell-lel](data-lake-analytics-get-started-powershell.md)
    - [A Data Lake Analytics használatának első lépései az Azure .NET SDK-val](data-lake-analytics-get-started-net-sdk.md)
    - [U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)
    - [Ismerkedés az Azure Data Lake Analytics U-SQL nyelvével](data-lake-analytics-u-sql-get-started.md)
    
- U-SQL és fejlesztés
    - [Ismerkedés az Azure Data Lake Analytics U-SQL nyelvével](data-lake-analytics-u-sql-get-started.md)
    - [U-SQL-ablakfunkciók használata Azure Data Lake Analytics-feladatokhoz](data-lake-analytics-use-window-functions.md)
    - [Felhasználó által definiált U-SQL-operátorok fejlesztése Data Lake Analytics-feladatokhoz](data-lake-analytics-u-sql-develop-user-defined-operators.md)
    
- Kezelés
    - [Az Azure Data Lake Analytics kezelése az Azure portál használatával](data-lake-analytics-manage-use-portal.md)
    - [Az Azure Data Lake Analytics kezelése az Azure PowerShell-lel](data-lake-analytics-manage-use-powershell.md)
    - [Az Azure Data Lake Analytics-feladatok figyelése és hibaelhárítása az Azure portállal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

- Átfogó oktatóanyag
    - [Az Azure Data Lake Analytics interaktív oktatóanyagainak használata](data-lake-analytics-use-interactive-tutorials.md)
    - [Webhelyek naplóinak elemzése az Azure Data Lake Analytics használatával](data-lake-analytics-analyze-weblogs.md)

- Ossza meg velünk a véleményét
    - [A dokumentációs várólista véleményezése](data-lake-analytics-documentation-backlog.md)
    - [Funkciókérés küldése](http://aka.ms/adlafeedback)
    - [Segítség kérése fórumokon](http://aka.ms/adlaforums)





<!--HONumber=Jun16_HO2-->


