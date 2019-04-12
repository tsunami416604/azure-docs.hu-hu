---
title: Webhelynaplók elemzése az Azure Data Lake Analytics használatával
description: Ismerje meg, hogyan használja a Data Lake Analytics webhelyek naplóinak elemzése.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 3a196735-d0d9-4deb-ba68-c4b3f3be8403
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 83742a4f82fb4d67fd258ff0d242847eab634c78
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2019
ms.locfileid: "54402285"
---
# <a name="analyze-website-logs-using-azure-data-lake-analytics"></a>Webhelynaplók elemzése az Azure Data Lake Analytics használatával
Ismerje meg, hogyan használja a Data Lake Analytics különösen tudni, mely hivatkozók hibába ütközött a hibákat, a webhelyén való hozzáféréskor webhelynaplók elemzése.

## <a name="prerequisites"></a>Előfeltételek
* **Visual Studio 2015 vagy Visual Studio 2013**.
* **[Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs)**.

    A Data Lake Tools for Visual Studio telepítése után megjelenik egy **Data Lake** elemnek, a **eszközök** elemét a Visual Studióban:

    ![U-SQL Visual Studio menüjében](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)
* **A Data Lake Analytics és a Data Lake Tools for Visual Studio alapszintű ismerete**. Első lépésként lásd:

  * [A Data Lake tools for Visual Studio használatával U-SQL-parancsfájlok fejlesztése](data-lake-analytics-data-lake-tools-get-started.md).
* **Data Lake Analytics-fiók.**  Lásd: [hozzon létre egy Azure Data Lake Analytics-fiók](data-lake-analytics-get-started-portal.md).
* **Telepítse a mintaadatokat.** Az Azure Portalon nyissa meg azt a Data Lake Analytics-fiók, és kattintson a **Mintaszkriptek** a bal oldali menüben, majd kattintson **mintaadatok másolása**. 

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz
Mielőtt hozhat létre, és tesztelje a U-SQL-szkripteket, akkor először csatlakoznia kell az Azure-bA.

**A Data Lake Analytics szolgáltatáshoz való kapcsolódás**

1. Nyissa meg a Visual Studiót.
2. Kattintson a **a Data Lake > lehetőségek és beállítások**.
3. Kattintson a **bejelentkezés**, vagy **felhasználó váltása** Ha valaki jelentkezett be, és kövesse az utasításokat.
4. Kattintson a **OK** a lehetőségek és beállítások párbeszédpanel bezárásához.

**A Tallózás gombra a Data Lake Analytics-fiókok**

1. Nyissa meg a Visual Studióban a **Server Explorer** Press **CTRL + ALT + S**.
2. A **Server Explorer** eszközben bontsa ki az **Azure** elemet, majd a **Data Lake Analytics** elemet. Ekkor megjelenik a Data Lake Analytics-fiókok listája, ha vannak ilyenek. Data Lake Analytics-fiókok a studióból nem hozható létre. A fiókok létrehozásával kapcsolatos információkért lásd: [Az Azure Data Lake Analytics használatának első lépései az Azure portállal](data-lake-analytics-get-started-portal.md) vagy [Az Azure Data Lake Analytics használatának első lépései az Azure PowerShell-lel](data-lake-analytics-get-started-powershell.md).

## <a name="develop-u-sql-application"></a>U-SQL-alkalmazás fejlesztése
A U-SQL-alkalmazások többnyire egy U-SQL parancsfájl. U-SQL kapcsolatos további információkért lásd: [Ismerkedés a U-SQL](data-lake-analytics-u-sql-get-started.md).

Emellett a felhasználó által definiált operátorok adhat hozzá az alkalmazást.  További információkért lásd: [fejlesztése U-SQL-felhasználó által definiált operátorok a Data Lake Analytics-feladatok](data-lake-analytics-u-sql-develop-user-defined-operators.md).

**Data Lake Analytics-feladat létrehozása és elküldése**

1. Kattintson a **fájl > Új > projekt**.
2. Válassza ki a U-SQL projekt.

    ![új U-SQL Visual Studio-projekt](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. Kattintson az **OK** gombra. A Visual studio létrehoz egy megoldást egy Script.usql fájllal.
4. Adja meg a következő szkriptet a Script.usql fájlba:

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

    A U-SQL ismertetése: [Data Lake Analytics U-SQL nyelv – első lépések](data-lake-analytics-u-sql-get-started.md).    
5. Új U-SQL parancsfájl hozzáadása a projekthez, és adja meg a következőket:

        // Query the referrers that ran into errors
        @content =
            SELECT *
            FROM SampleDBTutorials.dbo.ReferrersPerDay
            WHERE sc_status >=400 AND sc_status < 500;

        OUTPUT @content
        TO @"/Samples/Outputs/UnsuccessfulResponses.log"
        USING Outputters.Tsv();
6. Váltson vissza az első U-SQL-szkriptet, és mellett a **küldés** gombra, adja meg az Analytics-fiók.
7. A **Solution Explorer** eszközben kattintson a jobb gombbal a **Script.usql** fájlra, majd kattintson a **Build Script** (Parancsfájl létrehozása) elemre. Ellenőrizze az eredményeket a Tesztkimenet ablaktáblán.
8. A **Solution Explorer** eszközben kattintson a jobb gombbal a **Script.usql** fájlra, majd kattintson a **Submit Script** (Parancsfájl elküldése) lehetőségre.
9. Ellenőrizze a **Analytics-fiók** kívánja futtatni a feladatot, és kattintson a rendszer **küldés**. Az elküldés után az eredmények és a feladatra mutató hivatkozás megjelenik a Data Lake Tools for Visual Studio Eredmények ablakában.
10. Várjon, amíg a feladat sikeresen befejeződött.  Ha a feladat sikertelen volt, valószínűleg hiányzik a forrásfájl.  Tekintse meg az ebben az oktatóanyagban Előfeltételek szakaszát. További hibaelhárítási információkért lásd: [figyelése és hibaelhárítása az Azure Data Lake Analytics-feladatok](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).

    A feladat befejezése után meg kell a következő képernyő jelenik meg:

    ![a Data lake analytics webes naplók webhelynaplók elemzése](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)
11. A 7 – 10 lépést megismételve **Script1.usql**.

**Feladat kimenetének megtekintése**

1. A **Server Explorer** eszközben bontsa ki az **Azure** elemet, majd a **Data Lake Analytics** elemet, bontsa ki a saját Data Lake Analytics-fiókjait, bontsa ki a **Storage Accounts** (Tárfiókok) elemet, kattintson a jobb gombbal az alapértelmezett Data Lake-tárfiókra, majd kattintson az **Explorer** elemre.
2. Kattintson duplán a **minták** nyissa meg a mappát, majd kattintson duplán a **kimenetek**.
3. Kattintson duplán a **UnsuccessfulResponses.log**.
4. Annak érdekében, hogy közvetlenül a kimenetben keresse meg a kimeneti fájlt a diagram nézet a feladat is duplán.

## <a name="see-also"></a>Lásd még
A Data Lake Analytics különböző eszközökkel való használatának megismeréséhez lásd:

* [A Data Lake Analytics használatának első lépései az Azure Portallal](data-lake-analytics-get-started-portal.md)
* [A Data Lake Analytics használatának első lépései az Azure PowerShell-lel](data-lake-analytics-get-started-powershell.md)
* [A Data Lake Analytics használatának első lépései a .NET SDK-val](data-lake-analytics-get-started-net-sdk.md)
