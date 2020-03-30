---
title: Webhelynaplók elemzése az Azure Data Lake Analytics használatával
description: Ismerje meg, hogyan elemezheti a webhelynaplókat az Azure Data Lake Analytics használatával az U-SQL függvények és lekérdezések futtatásához.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 3a196735-d0d9-4deb-ba68-c4b3f3be8403
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 04c6d4c74a82ccfbcbb0faecb0dca5ec495f6663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672879"
---
# <a name="analyze-website-logs-using-azure-data-lake-analytics"></a>Webhelynaplók elemzése az Azure Data Lake Analytics használatával
Ismerje meg, hogyan elemezheti a webhelynaplókat a Data Lake Analytics segítségével, különösen annak megállapításához, hogy mely hivatkozók futottak hibákba, amikor megpróbálták meglátogatni a webhelyet.

## <a name="prerequisites"></a>Előfeltételek
* **Visual Studio 2015 vagy Visual Studio 2013**.
* **[Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs)**.

    A Data Lake Tools for Visual Studio telepítése után megjelenik egy **Data Lake** elem a Visual Studio **Eszközök** menüjében:

    ![Az U-SQL Visual Studio menü](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)
* **A Data Lake Analytics és a Visual Studio Data Lake Tools alapismeretei.** Első lépések:

  * [Az U-SQL parancsfájl fejlesztése a Data Lake visual studio-i eszközeivel.](data-lake-analytics-data-lake-tools-get-started.md)
* **Egy Data Lake Analytics-fiók.**  Lásd: [Azure Data Lake Analytics-fiók létrehozása.](data-lake-analytics-get-started-portal.md)
* **Telepítse a mintaadatokat.** Az Azure Portalon nyissa meg a Data Lake Analytics-fiókot, és kattintson a bal oldali menü **Mintaparancsfájljai parancsára,** majd a **Mintaadatok másolása parancsra.** 

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz
Mielőtt bármilyen U-SQL-parancsfájlt hozhat létre és tesztelhetne, először csatlakoznia kell az Azure-hoz.

**A Data Lake Analytics szolgáltatáshoz való kapcsolódás**

1. Nyissa meg a Visual Studiót.
2. Kattintson **a Data Lake > Beállítások és Beállítások parancsra.**
3. Kattintson **a Bejelentkezés**gombra, vagy **a Felhasználó módosítása gombra,** ha valaki bejelentkezett, és kövesse az utasításokat.
4. A Beállítások és beállítások párbeszédpanel bezárásához kattintson az **OK** gombra.

**A Data Lake Analytics-fiókok böngészése**

1. A Visual Studio alkalmazásban nyissa meg a **Server Explorer** alkalmazást a **CTRL+ALT+S**billentyűkombinációval.
2. A **Server Explorer** eszközben bontsa ki az **Azure** elemet, majd a **Data Lake Analytics** elemet. Ekkor megjelenik a Data Lake Analytics-fiókok listája, ha vannak ilyenek. A Data Lake Analytics-fiókok nem hozhatók létre a stúdióból. Fiók létrehozásához olvassa el az [Azure Data Lake Analytics azure-beli használatával ima kezdéshez,](data-lake-analytics-get-started-portal.md) vagy az Azure Data Lake Analytics [azure-beli PowerShell használatával – első lépések című témakört.](data-lake-analytics-get-started-powershell.md)

## <a name="develop-u-sql-application"></a>U-SQL alkalmazás fejlesztése
Az U-SQL-alkalmazások többnyire U-SQL parancsfájl. Ha többet szeretne megtudni az U-SQL-ről, [olvassa el az Első lépések az U-SQL-t című témakörben.](data-lake-analytics-u-sql-get-started.md)

Hozzáadhat egy felhasználó által definiált operátorokat az alkalmazáshoz.  További információ: [U-SQL-felhasználó által definiált operátorok fejlesztése a Data Lake Analytics-feladatokhoz című témakörben.](data-lake-analytics-u-sql-develop-user-defined-operators.md)

**Data Lake Analytics-feladat létrehozása és elküldése**

1. Kattintson > az **Új > > fájlra**.
2. Válassza ki a U-SQL projekt típust.

    ![új U-SQL Visual Studio-projekt](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
3. Kattintson az **OK** gombra. A Visual Studio létrehoz egy megoldást egy Script.usql fájllal.
4. Írja be a következő parancsfájlt a Script.usql fájlba:

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

    Az U-SQL megismeréséhez olvassa el a [Data Lake Analytics U-SQL nyelvének első lépéseit.](data-lake-analytics-u-sql-get-started.md)    
5. Adjon hozzá egy új U-SQL parancsfájlt a projekthez, és írja be a következőket:

        // Query the referrers that ran into errors
        @content =
            SELECT *
            FROM SampleDBTutorials.dbo.ReferrersPerDay
            WHERE sc_status >=400 AND sc_status < 500;

        OUTPUT @content
        TO @"/Samples/Outputs/UnsuccessfulResponses.log"
        USING Outputters.Tsv();
6. Váltson vissza az első U-SQL-parancsfájlra, és a **Küldés** gomb mellett adja meg Analytics-fiókját.
7. A **Solution Explorer** eszközben kattintson a jobb gombbal a **Script.usql** fájlra, majd kattintson a **Build Script** (Parancsfájl létrehozása) elemre. Ellenőrizze az eredményeket a Kimenet ablaktáblán.
8. A **Solution Explorer** eszközben kattintson a jobb gombbal a **Script.usql** fájlra, majd kattintson a **Submit Script** (Parancsfájl elküldése) lehetőségre.
9. Ellenőrizze, hogy az **Analytics-fiók** az, ahol futtatni szeretné a feladatot, majd kattintson a **Küldés**gombra. Az elküldés után az eredmények és a feladatra mutató hivatkozás megjelenik a Data Lake Tools for Visual Studio Eredmények ablakában.
10. Várjon, amíg a feladat sikeresen befejeződik.  Ha a feladat nem sikerült, akkor valószínűleg hiányzik a forrásfájl.  Tekintse meg az oktatóanyag Előfeltétel című részét. További hibaelhárítási információkért olvassa el az [Azure Data Lake Analytics-feladatok figyelése és hibaelhárítása című témakört.](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

    Amikor a feladat befejeződött, a következő képernyőjelenik meg:

    ![Data Lake analytics elemzi weblogs weboldal naplók](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)
11. Most ismételje meg a lépéseket 7-10 **Script1.usql**.

**Feladat kimenetének megtekintése**

1. A **Server Explorer** eszközben bontsa ki az **Azure** elemet, majd a **Data Lake Analytics** elemet, bontsa ki a saját Data Lake Analytics-fiókjait, bontsa ki a **Storage Accounts** (Tárfiókok) elemet, kattintson a jobb gombbal az alapértelmezett Data Lake-tárfiókra, majd kattintson az **Explorer** elemre.
2. Kattintson duplán **a Minták** elemre a mappa megnyitásához, majd kattintson duplán **a Kimenetek ikonra.**
3. Kattintson duplán **a Sikertelenválaszok.log fájlra.**
4. A feladat grafikonnézetében lévő kimeneti fájlra duplán is kattinthat, hogy közvetlenül a kimenetre navigálhasson.

## <a name="see-also"></a>Lásd még
A Data Lake Analytics különböző eszközökkel való használatának megismeréséhez lásd:

* [Ismerkedés a Data Lake Analytics szolgáltatással az Azure Portal használatával](data-lake-analytics-get-started-portal.md)
* [A Data Lake Analytics használatának első lépései az Azure PowerShell-lel](data-lake-analytics-get-started-powershell.md)
* [A Data Lake Analytics használatának első lépései a .NET SDK-val](data-lake-analytics-get-started-net-sdk.md)
