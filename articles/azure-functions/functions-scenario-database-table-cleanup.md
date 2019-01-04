---
title: Hajtsa végre a karbantartási feladat egy adatbázist az Azure Functions használatával |} A Microsoft Docs
description: Az Azure Functions használatával, amely csatlakozik az Azure SQL Database rendszeres időközönként sorait Tisztítja a feladat ütemezését.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: glenga
ms.openlocfilehash: 4ec2e9b931e6405aca5b4237bc044647af3b8bb3
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53608579"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Csatlakozás az Azure SQL Database az Azure Functions használatával

Ez a cikk bemutatja, hogyan hozzon létre egy ütemezett feladatot, amely egy Azure SQL Database-példányhoz csatlakozik az Azure Functions használatával. A függvénykód törli azokat a sorokat egy táblában az adatbázisban. Az új C# függvény egy előre meghatározott időzítő eseményindító sablonjához a Visual Studio 2017 alapján jön létre. Ennek támogatásához is meg kell adni egy adatbázis-kapcsolati karakterlánc, a függvényalkalmazáshoz egy alkalmazásbeállításhoz. Ebben a forgatókönyvben az adatbázison tömeges műveletet használja. 

Ha ez az első élményre C# funkciók, olvassa el a [Azure Functions C# – fejlesztői referencia](functions-dotnet-class-library.md).

## <a name="prerequisites"></a>Előfeltételek

+ A cikkben leírtak elvégzése [Visual Studio használatával az első függvény létrehozása](functions-create-your-first-function-visual-studio.md) a verzió 2.x verziójú futtatókörnyezet célzó helyi függvényalkalmazás létrehozásához. Meg kell is közzétette a projekthez egy függvényalkalmazáshoz az Azure-ban.

+ Ez a cikk bemutatja a Transact-SQL parancsot, amely végrehajtja a műveletcsoport törlése a **SalesOrderHeader** az AdventureWorksLT mintaadatbázis táblájában. Az AdventureWorksLT mintaadatbázis létrehozása, hajtsa végre a cikk lépéseit [egy Azure SQL database létrehozása az Azure Portalon](../sql-database/sql-database-get-started-portal.md).

+ Hozzá kell adnia egy [kiszolgálószintű tűzfalszabály](../sql-database/sql-database-get-started-portal-firewall.md) az útmutatóhoz használt számítógép nyilvános IP-címét. Ez a szabály szükséges az SQL database példányát a helyi számítógépről érhet el.  

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése

A kapcsolati sztring lekérése befejezésekor létrehozott adatbázis kell [egy Azure SQL database létrehozása az Azure Portalon](../sql-database/sql-database-get-started-portal.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza ki **SQL-adatbázisok** elemet a bal oldali menüben válassza ki az adatbázist a a **SQL-adatbázisok** lapot.

1. Válassza ki **kapcsolati karakterláncok** alatt **beállítások** , és másolja a teljes **ADO.NET** kapcsolati karakterláncot.

    ![Másolja az ADO.NET kapcsolati karakterláncot.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>A kapcsolati sztring beállítása

A függvények végrehajtásához szükséges gazdaszolgáltatást az Azure-ban egy függvényalkalmazás biztosítja. Ajánlott biztonsági eljárásként a függvényalkalmazás-beállításokat kapcsolati karakterláncok és egyéb titkos adatait tárolja. Alkalmazásbeállítások használatával megakadályozza a véletlen közzétételét a kód a kapcsolati karakterláncot. Alkalmazásbeállítások a függvényalkalmazás közvetlenül a Visual Studióból is elérheti.

Meg kell korábban feltöltötte az alkalmazást az Azure-bA. Ha ezt még nem tette meg, [a függvényalkalmazás közzététele az Azure-bA](functions-develop-vs.md#publish-to-azure).

1. A Megoldáskezelőben kattintson a jobb gombbal a függvényalkalmazás projektjét, és válassza a **közzététel** > **Alkalmazásbeállítások kezelése...** . Válassza ki **beállítás hozzáadása**, a **új alkalmazásbeállítás neve**, típus `sqldb_connection`, és válassza ki **OK**.

    ![Alkalmazásbeállítások a függvényalkalmazáshoz.](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. Az új **sqldb_connection** beállítást, illessze be az előző szakaszban kimásolt kapcsolati karakterláncot a **helyi** mezőt, és cserélje le `{your_username}` és `{your_password}` valós helyőrzőt értékek. Válassza ki **érték beszúrása helyi** másolja a frissített értéket, hogy a **távoli** mezőben, majd válassza ki **OK**.

    ![Adja hozzá az SQL-kapcsolati sztring beállítása.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    A kapcsolati karakterláncokat tárolja titkosított formában az Azure-ban (**távoli**). Titkos kulcsok kiszivárgását a local.settings.json projektfájl elkerülése érdekében (**helyi**) ki kell zárni a forráskezelőből, mint például egy .gitignore fájlt használatával.

## <a name="add-the-sqlclient-package-to-the-project"></a>Adja hozzá a SqlClient csomagot a projekthez

Adja hozzá a NuGet-csomagot, amely már tartalmaz az SqlClient kell. A data access könyvtár egy SQL-adatbázishoz való kapcsolódáshoz van szükség.

1. Nyissa meg a helyi függvényalkalmazás projektjét a Visual Studio 2017-ben.

1. A Megoldáskezelőben kattintson a jobb gombbal a függvényalkalmazás projektjét, és válassza a **NuGet-csomagok kezelése**.

1. A **Tallózás** fülön keresse meg a(z) ```System.Data.SqlClient``` elemet, majd válassza ki.

1. Az a **System.Data.SqlClient** lapra, jelölje be verzió `4.5.1` majd **telepítése**.

1. A telepítés befejezése után tekintse át a módosításokat, majd kattintson az **OK** gombra az **Előnézet** ablak bezárásához.

1. Ha megjelenik egy **License Acceptance** (Licenc elfogadása) ablak, kattintson az **I Accept** (Elfogadom) gombra.

Most a C#-függvénykódot, amely csatlakozik az SQL-adatbázis is hozzáadhat.

## <a name="add-a-timer-triggered-function"></a>Időzítő által aktivált hozzáadása

1. A Megoldáskezelőben kattintson a jobb gombbal a függvényalkalmazás projektjét, és válassza a **Hozzáadás** > **új Azure-függvény**.

1. Az a **Azure Functions** , a kiválasztott sablonhoz adja az új elem hasonló `DatabaseCleanup.cs` válassza **Hozzáadás**.

1. Az a **új Azure-függvény** párbeszédpanelen válassza ki **időzítő eseményindító** , majd **OK**. Ezen a párbeszédpanelen az időzítő által aktivált függvény kódfájlra hoz létre.

1. Nyissa meg az új kód fájlt, és adja hozzá a következő using utasításokat a fájl elején:

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. Cserélje le a meglévő `Run` függvény a következő kóddal:

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

    Ez a függvény akkor fut, 15 másodpercenként frissíteni a `Status` oszlop a szállítási dátum alapján. Az időzítő eseményindító kapcsolatos további információkért lásd: [Azure Functions időzítő eseményindító](functions-bindings-timer.md).

1. Nyomja meg **F5** elindításához a függvényalkalmazást. A [Azure Functions Core Tools](functions-develop-local.md) végrehajtási ablak nyílik meg a Visual Studio mögött.

1. Indítás után 15 másodperc, a függvény futásakor. Tekintse meg a kimenetet, és jegyezze fel a frissített sorok száma a **SalesOrderHeader** tábla.

    ![A függvény naplóinak megtekintése.](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    Az első végrehajtás a frissítenie kell a 32 sornyi adatot. Következő futtatások nincsenek adatok sorok frissítése, kivéve, ha módosítja a SalesOrderHeader tábla adatait, hogy több sor kiválasztásakor a rendszer által a `UPDATE` utasítást.

Ha azt tervezi, hogy [közzétenni ezt a funkciót](functions-develop-vs.md#publish-to-azure), ne felejtse el módosítani a `TimerTrigger` további ésszerű attribútumot [cron-ütemezését](functions-bindings-timer.md#cron-expressions) , mint 15 másodpercenként.

## <a name="next-steps"></a>További lépések

Ezután megtudhatja, hogyan használható. Más szolgáltatások integrálása a Logic Apps együttműködik.

> [!div class="nextstepaction"]
> [A Logic Apps szolgáltatással integrálható függvények létrehozása](functions-twitter-email.md)

Függvények kapcsolatos további információkért lásd a következő cikkeket:

+ [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)  
  Programozói segédanyagok függvények kódolásához, valamint eseményindítók és kötések meghatározásához.
+ [Az Azure Functions tesztelése](functions-test-a-function.md)  
  Különböző függvénytesztelési eszközöket és technikákat ír le.  
