---
title: Webhelyek naplóinak elemzése az Azure Data Lake Analytics használatával
description: Megtudhatja, hogyan használja a Data Lake Analytics webhelyek naplóinak elemzése.
services: data-lake-analytics
author: saveenr
manager: saveenr
editor: jasonwhowell
ms.assetid: 3a196735-d0d9-4deb-ba68-c4b3f3be8403
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: 8cb8e0f683c2790d7aebb87a684798ea0a36417f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623366"
---
# <a name="analyze-website-logs-using-azure-data-lake-analytics"></a>Webhelyek naplóinak elemzése az Azure Data Lake Analytics használatával
Megtudhatja, hogyan használja a Data Lake Analytics, különösen akkor tudni, melyik hivatkozó kérelmei hibába ütközött a webhelyen való webhelyek naplóinak elemzése.

## <a name="prerequisites"></a>Előfeltételek
* **Visual Studio 2015-öt vagy a Visual Studio 2013**.
* **[Data Lake Tools for Visual Studio](http://aka.ms/adltoolsvs)**.

    A Data Lake Tools for Visual Studio telepítése után megjelenik egy **Data Lake** elemnek a **eszközök** elemét a Visual Studióban:

    ![U-SQL Visual Studio menü](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)
* **A Data Lake Analytics és a Data Lake Tools for Visual Studio vonatkozó általános ismeretekre**. Első lépések, olvassa el:

  * [Fejlesztése a Data Lake tools for Visual Studio használatával U-SQL parancsfájl](data-lake-analytics-data-lake-tools-get-started.md).
* **Data Lake Analytics-fiók.**  Lásd: [Azure Data Lake Analytics-fiók létrehozása](data-lake-analytics-get-started-portal.md).
* **A mintaadatok telepítéséhez.** Az Azure portálon, nyissa meg, Data Lake Analytics-fiók, és kattintson a **mintaparancsfájlok** a bal oldali menüben kattintson a **mintaadatok másolása**. 

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz
Előtt létrehozhatja és a U-SQL-parancsfájlok tesztelése, először csatlakoztatnia kell az Azure-bA.

**A Data Lake Analytics szolgáltatáshoz való kapcsolódás**

1. Nyissa meg a Visual Studiót.
2. Kattintson a **a Data Lake > lehetőségek és beállítások**.
3. Kattintson a **bejelentkezés**, vagy **felhasználói módosítása** Ha valaki bejelentkezett-e, és kövesse az utasításokat.
4. Kattintson a **OK** lehetőségek és beállítások párbeszédpanel bezárásához.

**A Data Lake Analytics-fiókok tallózással**

1. Nyissa meg a Visual Studio eszközből **Server Explorer** press által **CTRL + ALT + S**.
2. A **Server Explorer** eszközben bontsa ki az **Azure** elemet, majd a **Data Lake Analytics** elemet. Ekkor megjelenik a Data Lake Analytics-fiókok listája, ha vannak ilyenek. A Studio Data Lake Analytics-fiókok nem hozható létre. A fiókok létrehozásával kapcsolatos információkért lásd: [Az Azure Data Lake Analytics használatának első lépései az Azure portállal](data-lake-analytics-get-started-portal.md) vagy [Az Azure Data Lake Analytics használatának első lépései az Azure PowerShell-lel](data-lake-analytics-get-started-powershell.md).

## <a name="develop-u-sql-application"></a>U-SQL-alkalmazások fejlesztése
A U-SQL-alkalmazások többnyire U-SQL parancsfájl. További információk a U-SQL, [Ismerkedés a U-SQL](data-lake-analytics-u-sql-get-started.md).

Az alkalmazás hozzáadása felhasználói operátorok adhat hozzá.  További információkért lásd: [fejlesztése U-SQL-felhasználó által definiált Data Lake Analytics-feladatok operátorok](data-lake-analytics-u-sql-develop-user-defined-operators.md).

**Data Lake Analytics-feladat létrehozása és elküldése**

1. Kattintson a **fájl > Új > projekt**.
2. Válassza ki a U-SQL projekt.

    ![új U-SQL Visual Studio-projekt](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. Kattintson az **OK** gombra. A Visual studio létrehoz egy megoldást Script.usql fájllal.
4. A Script.usql fájlba írja be a következő parancsfájlt:

        // Create a database for easy reuse, so you don't need to read from a file every time.
        CREATE DATABASE IF NOT EXISTS SampleDBTutorials;

        // Create a Table valued function. TVF ensures that your jobs fetch data from the weblog file with the correct schema.
        DROP FUNCTION IF EXISTS SampleDBTutorials.dbo.WeblogsView;
        CREATE FUNCTION SampleDBTutorials.dbo.WeblogsView()
        RETURNS @result TABLE
        (
            s_date DateTime,
            s_time string,
            s_sitename string,
            cs_method string,
            cs_uristem string,
            cs_uriquery string,
            s_port int,
            cs_username string,
            c_ip string,
            cs_useragent string,
            cs_cookie string,
            cs_referer string,
            cs_host string,
            sc_status int,
            sc_substatus int,
            sc_win32status int,
            sc_bytes int,
            cs_bytes int,
            s_timetaken int
        )
        AS
        BEGIN

            @result = EXTRACT
                s_date DateTime,
                s_time string,
                s_sitename string,
                cs_method string,
                cs_uristem string,
                cs_uriquery string,
                s_port int,
                cs_username string,
                c_ip string,
                cs_useragent string,
                cs_cookie string,
                cs_referer string,
                cs_host string,
                sc_status int,
                sc_substatus int,
                sc_win32status int,
                sc_bytes int,
                cs_bytes int,
                s_timetaken int
            FROM @"/Samples/Data/WebLog.log"
            USING Extractors.Text(delimiter:' ');
            RETURN;
        END;

        // Create a table for storing referrers and status
        DROP TABLE IF EXISTS SampleDBTutorials.dbo.ReferrersPerDay;
        @weblog = SampleDBTutorials.dbo.WeblogsView();
        CREATE TABLE SampleDBTutorials.dbo.ReferrersPerDay
        (
            INDEX idx1
            CLUSTERED(Year ASC)
            DISTRIBUTED BY HASH(Year)
        ) AS

        SELECT s_date.Year AS Year,
            s_date.Month AS Month,
            s_date.Day AS Day,
            cs_referer,
            sc_status,
            COUNT(DISTINCT c_ip) AS cnt
        FROM @weblog
        GROUP BY s_date,
                cs_referer,
                sc_status;

    A U-SQL ismertetése: [Ismerkedés a Data Lake Analytics U-SQL nyelv](data-lake-analytics-u-sql-get-started.md).    
5. Új U-SQL parancsfájl hozzáadása a projekthez, és írja be a következőt:

        // Query the referrers that ran into errors
        @content =
            SELECT *
            FROM SampleDBTutorials.dbo.ReferrersPerDay
            WHERE sc_status >=400 AND sc_status < 500;

        OUTPUT @content
        TO @"/Samples/Outputs/UnsuccessfulResponses.log"
        USING Outputters.Tsv();
6. Váltson vissza az első U-SQL-parancsfájlt, és mellett a **Submit** gombra, adja meg az Analytics-fiókja.
7. A **Solution Explorer** eszközben kattintson a jobb gombbal a **Script.usql** fájlra, majd kattintson a **Build Script** (Parancsfájl létrehozása) elemre. Ellenőrizze az eredményeket a Tesztkimenet ablaktáblán.
8. A **Solution Explorer** eszközben kattintson a jobb gombbal a **Script.usql** fájlra, majd kattintson a **Submit Script** (Parancsfájl elküldése) lehetőségre.
9. Ellenőrizze a **Analytics-fiók** , a egy, ahol szeretné futtatni a feladatot, és kattintson a **Submit**. Az elküldés után az eredmények és a feladatra mutató hivatkozás megjelenik a Data Lake Tools for Visual Studio Eredmények ablakában.
10. Várjon, amíg a feladat sikeresen befejeződött.  A feladat meghiúsult, valószínűleg hiányzik a forrásfájl.  Lásd: a jelen oktatóanyag című cikk Előfeltételek szakaszát. További hibaelhárítási információért lásd: [figyelése és hibaelhárítása az Azure Data Lake Analytics-feladatok](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).

    A feladat befejezése után kell jelenik meg a következő képernyő:

    ![a Data lake analytics webes naplók webhelyek naplóinak elemzése](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)
11. Most ismételje meg a 7 – 10 a **Script1.usql**.

**Feladat kimenetének megtekintése**

1. A **Server Explorer** eszközben bontsa ki az **Azure** elemet, majd a **Data Lake Analytics** elemet, bontsa ki a saját Data Lake Analytics-fiókjait, bontsa ki a **Storage Accounts** (Tárfiókok) elemet, kattintson a jobb gombbal az alapértelmezett Data Lake-tárfiókra, majd kattintson az **Explorer** elemre.
2. Kattintson duplán a **minták** nyissa meg a mappát, és kattintson duplán a **kimenetek**.
3. Kattintson duplán a **UnsuccessfulResponsees.log**.
4. Ahhoz, hogy közvetlenül a kimeneti keresse meg a kimeneti fájl belül a diagram nézet, a feladat is duplán.

## <a name="see-also"></a>Lásd még
A Data Lake Analytics különböző eszközökkel való használatának megismeréséhez lásd:

* [A Data Lake Analytics használatának első lépései az Azure Portallal](data-lake-analytics-get-started-portal.md)
* [A Data Lake Analytics használatának első lépései az Azure PowerShell-lel](data-lake-analytics-get-started-powershell.md)
* [A Data Lake Analytics használatának első lépései a .NET SDK-val](data-lake-analytics-get-started-net-sdk.md)
