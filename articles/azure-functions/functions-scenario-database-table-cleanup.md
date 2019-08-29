---
title: Adatbázis-tisztítási feladat végrehajtása a Azure Functions használatával | Microsoft Docs
description: A Azure Functions használatával ütemezhet egy olyan feladatot, amely a Azure SQL Databasehoz kapcsolódik a sorok rendszeres tisztításához.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: glenga
ms.openlocfilehash: 0388c712d6f44755e768e491944df1a9451653b7
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085239"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Azure Functions használata Azure SQL Databasehoz való kapcsolódáshoz

Ez a cikk bemutatja, hogyan használható a Azure Functions egy Azure SQL Database-példányhoz csatlakozó ütemezett feladatok létrehozásához. A függvény kódja megtisztítja a sorokat egy táblában az adatbázisban. Az új C# függvény egy előre definiált időzítő-trigger sablon alapján jön létre a Visual Studio 2019-ben. Ennek a forgatókönyvnek a támogatásához egy adatbázis-kapcsolódási karakterláncot kell beállítania egy alkalmazás-beállításként a Function alkalmazásban. Ez a forgatókönyv egy tömeges műveletet használ az adatbázison. 

Ha első alkalommal dolgozik a functions szolgáltatással C# , olvassa el a [Azure functions C# fejlesztői referenciát](functions-dotnet-class-library.md).

## <a name="prerequisites"></a>Előfeltételek

+ Az [első függvény létrehozása a Visual Studióval](functions-create-your-first-function-visual-studio.md) című cikkben ismertetett lépéseket követve hozzon létre egy helyi function alkalmazást, amely a 2. x verziójú futtatókörnyezetet célozza meg. Emellett közzé kell tennie a projektet egy Azure-beli Function alkalmazásban.

+ Ez a cikk egy Transact-SQL-parancsot mutat be, amely egy tömeges karbantartási műveletet hajt végre a AdventureWorksLT-mintaadatbázis **SalesOrderHeader** táblájában. A AdventureWorksLT-mintaadatbázis létrehozásához hajtsa végre a [Azure Portal Azure SQL Database-adatbázis létrehozása](../sql-database/sql-database-get-started-portal.md)című cikkben ismertetett lépéseket.

+ A rövid útmutatóhoz használt számítógép nyilvános IP-címéhez hozzá kell adnia egy [kiszolgálói szintű tűzfalszabály-szabályt](../sql-database/sql-database-get-started-portal-firewall.md) . Ez a szabály szükséges ahhoz, hogy hozzáférhessen az SQL Database-példányhoz a helyi számítógépről.  

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

Az [Azure SQL Database-adatbázis létrehozásakor](../sql-database/sql-database-get-started-portal.md)létrehozott adatbázishoz tartozó kapcsolódási karakterláncot le kell kérni a Azure Portal.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza ki az **SQL-adatbázisok** elemet a bal oldali menüben, és válassza ki az adatbázist az **SQL-adatbázisok** lapon.

1. Válassza a **kapcsolatok karakterláncok** lehetőséget a **Beállítások** területen, és másolja a teljes **ADO.net** -kapcsolatok karakterláncát.

    ![Másolja a ADO.NET-kapcsolatok karakterláncát.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>A kapcsolati sztring beállítása

A függvények végrehajtásához szükséges gazdaszolgáltatást az Azure-ban egy függvényalkalmazás biztosítja. Ajánlott biztonsági eljárásként a Function app-beállításokban tárolja a kapcsolatok karakterláncait és egyéb titkait. Az Alkalmazásbeállítások használata megakadályozza a kapcsolatok karakterláncának véletlen közzétételét a kóddal. A Function alkalmazáshoz közvetlenül a Visual studióból férhet hozzá.

Előzőleg közzé kell tennie az alkalmazást az Azure-ban. Ha még nem tette meg, [tegye közzé a Function alkalmazást az Azure](functions-develop-vs.md#publish-to-azure)-ban.

1. Megoldáskezelő kattintson a jobb gombbal a Function app projektre, és > válassza az**Alkalmazásbeállítások kezelése...** lehetőséget. Válassza a **beállítás hozzáadása**lehetőséget, az **új Alkalmazásbeállítás neve**mezőbe `sqldb_connection`írja be a nevet, majd kattintson **az OK gombra**.

    ![A Function alkalmazás Alkalmazásbeállítások.](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. Az új **sqldb_connection** -beállításban illessze be az előző szakaszban a **helyi** mezőbe másolt, majd a valós értékekkel `{your_username}` lecserélt és `{your_password}` helyőrzőket tartalmazó hálózati karakterláncot. Válassza a **helyi érték beszúrása** lehetőséget, hogy a frissített értéket a **távoli** mezőbe másolja, majd válassza az **OK**gombot.

    ![Adja meg az SQL-kapcsolatok karakterláncának beállítását.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    A rendszer az Azure-ban (**távoli**) tárolja a kapcsolatok karakterláncait. A titkok kiszivárgásának megelőzése érdekében a local. Settings. JSON projektfájlt (**Local**) ki kell zárni a forrás-vezérlőelemből, például egy. gitignore-fájl használatával.

## <a name="add-the-sqlclient-package-to-the-project"></a>A SqlClient-csomag hozzáadása a projekthez

Hozzá kell adnia a SqlClient könyvtárat tartalmazó NuGet-csomagot. Ez az adatelérési függvénytár egy SQL Database-adatbázishoz való kapcsolódáshoz szükséges.

1. Nyissa meg a helyi function alkalmazás projektjét a Visual Studio 2019-ben.

1. Megoldáskezelő kattintson a jobb gombbal a Function app projektre, és válassza a **NuGet-csomagok kezelése**lehetőséget.

1. A **Tallózás** fülön keresse meg a(z) ```System.Data.SqlClient``` elemet, majd válassza ki.

1. A **System. SqlClient** lapon válassza a verzió `4.5.1` lehetőséget, majd kattintson a **telepítés**gombra.

1. A telepítés befejezése után tekintse át a módosításokat, majd kattintson az **OK** gombra az **Előnézet** ablak bezárásához.

1. Ha megjelenik egy **License Acceptance** (Licenc elfogadása) ablak, kattintson az **I Accept** (Elfogadom) gombra.

Most hozzáadhatja a C# SQL Databasehoz csatlakozó függvény kódját is.

## <a name="add-a-timer-triggered-function"></a>Időzítő által aktivált hozzáadása

1. Megoldáskezelő kattintson a jobb gombbal a Function app projektre, és válassza az**új Azure-függvény** **hozzáadása** > lehetőséget.

1. A kiválasztott **Azure functions** sablonnal adja meg az új elemet `DatabaseCleanup.cs` , majd válassza a **Hozzáadás**lehetőséget.

1. Az **új Azure-függvény** párbeszédpanelen válassza az **időzítő trigger** lehetőséget, majd **az OK gombot**. Ez a párbeszédablak létrehoz egy programkódot az időzítő által aktivált függvényhez.

1. Nyissa meg az új kódot, és adja hozzá a következő using utasításokat a fájl elejéhez:

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. Cserélje le a `Run` meglévő függvényt a következő kódra:

    ```cs
    [FunctionName("DatabaseCleanup")]
    public static async Task Run([TimerTrigger("*/15 * * * * *")]TimerInfo myTimer, ILogger log)
    {
        // Get the connection string from app settings and use it to create a connection.
        var str = Environment.GetEnvironmentVariable("sqldb_connection");
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " +
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.LogInformation($"{rows} rows were updated");
            }
        }
    }
    ```

    Ez a függvény 15 másodpercenként fut az `Status` oszlopnak a szállítási dátum alapján történő frissítéséhez. További információ az időzítő triggerről: [időzítő trigger Azure Functionshoz](functions-bindings-timer.md).

1. Nyomja le az **F5** billentyűt a Function alkalmazás elindításához. Megnyílik a [Azure functions Core Tools](functions-develop-local.md) végrehajtási ablak a Visual Studio mögött.

1. Az indítás után 15 másodperccel a függvény fut. Tekintse meg a kimenetet, és jegyezze fel, hogy hány sor frissült a **SalesOrderHeader** táblában.

    ![Tekintse meg a függvények naplóit.](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    Az első végrehajtáskor frissítenie kell az 32-es adatsorokat. A következő futtatások nem frissítik az adatsorokat, hacsak nem módosítja a SalesOrderHeader, hogy az `UPDATE` utasításban több sort is kiválasszanak.

Ha azt tervezi, hogy [közzéteszi ezt](functions-develop-vs.md#publish-to-azure)a függvényt, `TimerTrigger` ne felejtse el módosítani az attribútumot egy ésszerű [cron](functions-bindings-timer.md#ncrontab-expressions) -ütemtervre, mint 15 másodpercenként.

## <a name="next-steps"></a>További lépések

A következő lépésben megtudhatja, hogyan használható. A más szolgáltatásokkal való integrációhoz Logic Apps függvényekkel.

> [!div class="nextstepaction"]
> [Logic Apps-sel integrált függvény létrehozása](functions-twitter-email.md)

A functions szolgáltatással kapcsolatos további információkért tekintse meg a következő cikkeket:

+ [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)  
  Programozói segédanyagok függvények kódolásához, valamint eseményindítók és kötések meghatározásához.
+ [Az Azure Functions tesztelése](functions-test-a-function.md)  
  Különböző függvénytesztelési eszközöket és technikákat ír le.  
