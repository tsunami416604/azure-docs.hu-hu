---
title: "Az Azure Functions segítségével hajtsa végre a tisztítási feladat adatbázis |} Microsoft Docs"
description: "Az Azure Functions használatával, amely kapcsolódik az Azure SQL-adatbázis rendszeres időközönként sorait tisztítja meg a feladat ütemezését."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/22/2017
ms.author: glenga
ms.openlocfilehash: 9d8261a22f5ea9ce61bcdc79d24a6c054597039b
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2017
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Használja az Azure Functions egy Azure SQL adatbázishoz való kapcsolódáshoz
Ez a témakör bemutatja, hogyan hozzon létre egy ütemezett feladatot, amely egy Azure SQL-adatbázis egy tábla sorainak a szükségtelenné vált az Azure Functions használatával. Az új C# függvény az Azure-portálon előre definiált időzítő eseményindító sablon alapján jön létre. Ez a forgatókönyv támogatása érdekében is meg kell adni egy adatbázis-kapcsolati karakterláncot, a függvény alkalmazás Alkalmazásbeállítás. Ez a forgatókönyv használ egy tömeges művelet az adatbázison. 

Szeretné, hogy a függvény folyamat egyéni létrehozás, Olvasás, frissítés és Törlés (CRUD) operations a Mobile Apps tábla, Ehelyett használjon [Mobile Apps kötések](functions-bindings-mobile-apps.md).

## <a name="prerequisites"></a>Előfeltételek

+ Ez a témakör egy időzítő indított függvényt használja. Hajtsa végre a következő témakör lépéseit [függvény létrehozása az Azure-időzítő által elindított](functions-create-scheduled-function.md) egy C# verzió függvény létrehozásához.   

+ Ebben a témakörben bemutatjuk hajt végre egy tömeges törlésének művelete a Transact-SQL parancsot a **SalesOrderHeader** a AdventureWorksLT mintaadatbázis tábla. A AdventureWorksLT mintaadatbázis létrehozásához hajtsa végre a témakör lépéseit [Azure SQL-adatbázis létrehozása az Azure portálon](../sql-database/sql-database-get-started-portal.md). 

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

Le kell töltenie a kapcsolati karakterláncot az adatbázishoz való befejezésekor létrehozott [Azure SQL-adatbázis létrehozása az Azure portálon](../sql-database/sql-database-get-started-portal.md).

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
 
3. Válassza ki **SQL-adatbázisok** a bal oldali menüben válassza ki az adatbázist a a **SQL-adatbázisok** lap.

4. Válassza ki **adatbázis-kapcsolati karakterláncok megjelenítése** , és másolja a teljes **ADO.NET** kapcsolati karakterláncot.

    ![Másolja az ADO.NET kapcsolati karakterláncot.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>A kapcsolati karakterlánc beállítása 

A függvények végrehajtásához szükséges gazdaszolgáltatást az Azure-ban egy függvényalkalmazás biztosítja. Ajánlott a kapcsolati karakterláncokat és más titkos adatokat tárolja a függvény Alkalmazásbeállítások. Alkalmazásbeállítások használatával elkerülheti a véletlen közzétételét a kód a kapcsolati karakterlánc. 

1. Keresse meg a létrehozott függvény app [függvény létrehozása az Azure-időzítő által elindított](functions-create-scheduled-function.md).

2. Válassza ki **Platform funkciói** > **Alkalmazásbeállítások**.
   
    ![A függvény alkalmazás Alkalmazásbeállítások.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings.png)

2. Görgessen le a **kapcsolati karakterláncok** , és adja hozzá egy kapcsolati karakterláncot a táblázatban megadott beállításokkal.
   
    ![A kapcsolati karakterlánc hozzáadásának a függvény alkalmazás beállításai.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-strings.png)

    | Beállítás       | Ajánlott érték | Leírás             | 
    | ------------ | ------------------ | --------------------- | 
    | **Name (Név)**  |  sqldb_connection  | A tárolt kapcsolati karakterlánc, a függvény kódban elérésére használhatók.    |
    | **Érték** | Másolt karakterlánc  | Illessze be a az előző szakaszban kimásolt kapcsolati karakterláncot, és cserélje le `{your_username}` és `{your_password}` helyőrzőt valódi értékek. |
    | **Típus** | SQL Database | Az alapértelmezett SQL-adatbázis-kapcsolat használata. |   

3. Kattintson a **Save** (Mentés) gombra.

Most a C# függvény kódot, amely összeköti az SQL-adatbázis is hozzáadhat.

## <a name="update-your-function-code"></a>Frissítse a függvény kódot

1. A függvény alkalmazást válassza ki a időzítő-eseményindítóval aktivált függvény.
 
3. A meglévő funkciókódot tetején adja hozzá a következő szerelvény hivatkozásokat:

    ```cs
    #r "System.Configuration"
    #r "System.Data"
    ```

3. Adja hozzá a következő `using` utasítást, hogy a funkciót:
    ```cs
    using System.Configuration;
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

4. Cserélje le a meglévő `Run` függvény a következő kóddal:
    ```cs
    public static async Task Run(TimerInfo myTimer, TraceWriter log)
    {
        var str = ConfigurationManager.ConnectionStrings["sqldb_connection"].ConnectionString;
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " + 
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.Info($"{rows} rows were updated");
            }
        }
    }
    ```

    Ez a minta parancs frissíti a `Status` oszlop a szállítási dátum alapján. Azt frissítenie kell a 32 sornyi adatot.

5. Kattintson a **mentése**, tekintse meg a **naplók** windows a következő függvény végrehajtása, majd jegyezze fel a frissített sorok száma a **SalesOrderHeader** tábla.

    ![A függvény naplók megtekintéséhez.](./media/functions-scenario-database-table-cleanup/functions-logs.png)

## <a name="next-steps"></a>Következő lépések

A következő további funkciók használata a Logic Apps a más szolgáltatásokkal való integrációjához.

> [!div class="nextstepaction"] 
> [Hozzon létre egy függvényt, amely integrálható a Logic Apps](functions-twitter-email.md)

Funkciók kapcsolatos további információkért lásd a következő témaköröket:

* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)  
  Programozói segédanyagok függvények kódolásához, valamint eseményindítók és kötések meghatározásához.
* [Az Azure Functions tesztelése](functions-test-a-function.md)  
  Különböző függvénytesztelési eszközöket és technikákat ír le.  
